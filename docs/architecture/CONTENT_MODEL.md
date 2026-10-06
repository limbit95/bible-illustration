# Content Model v1.1

> 상태: **CONFIRMED / 2026-10-06 pre-production hardening**
>
> 목적: 성경 본문 → 제작 Episode → Cut으로 이어지는 콘텐츠 모델과 식별 체계를 먼저 안정화한다.
>
> Episode/Cut 세부 모델, Continuity, Library Entity, Run, Provider, Image Asset 책임은 현재 각 정식 Architecture 문서에서 확정되어 있다.

## 1. 핵심 모델

### 1.1 Scripture Reference

성경 본문 자체와 제작 구조를 분리한다.

Scripture Reference는 이미지 제작의 **근거가 되는 본문 위치**를 가리키며, Episode ID나 Cut ID 자체에 장/절을 결합하지 않는다.

Episode의 본문 참조는 역할을 구분한다.

- **primary_scripture**: Episode가 직접 시각화하는 기준 본문
- **supporting_scripture**: 병행 본문, 보조 설명, 역사적 맥락 등 필요 시 함께 참고하는 본문

Episode ID의 `BOOK`은 항상 `primary_scripture`의 책 코드를 기준으로 한다.

v0에서는 하나의 Episode에 속한 모든 `primary_scripture` 범위가 **같은 성경 책(Book Code)** 안에 있어야 한다. 다른 책의 병행·보조 본문은 `supporting_scripture`로 기록한다. 서로 다른 두 책의 본문을 모두 주본문으로 삼아야 하는 제작 단위가 실제로 필요해지면, 우선 Episode 분리를 검토하고 그것으로 해결되지 않을 때 Content Model 확장 여부를 다시 결정한다.

본문 범위는 번역본의 문장 텍스트가 아니라 우선 다음과 같은 구조화된 위치 정보로 표현한다.

```yaml
primary_scripture:
  - book: GEN
    start:
      chapter: 1
      verse: 1
    end:
      chapter: 1
      verse: 13

supporting_scripture: []
```

주본문은 장 경계를 넘을 수 있다.

```yaml
primary_scripture:
  - book: GEN
    start:
      chapter: 1
      verse: 31
    end:
      chapter: 2
      verse: 3
```

서로 연속되지 않은 구간은 하나의 범위로 억지로 합치지 않고 여러 항목으로 기록한다.

병행 본문이나 다른 책의 관련 본문은 `supporting_scripture`에 별도 등록할 수 있다.

이 구분은 "어떤 본문을 실제로 제작하는가"와 "어떤 본문을 참고하는가"가 섞이지 않도록 하기 위한 것이다.

### 1.2 Episode

Episode는 **성경 장(chapter)과 독립된 제작 단위**다.

Episode는 하나의 시각적·서사적 흐름으로 묶어 제작할 범위를 뜻한다.

따라서 다음을 허용한다.

- 한 성경 장을 여러 Episode로 분할
- 하나의 Episode가 여러 장에 걸침
- 하나의 Episode가 서로 떨어진 여러 주본문 구간을 포함
- 하나의 Episode가 다른 책의 병행·보조 본문을 함께 참조
- 서로 다른 Episode가 일부 동일한 본문 구간을 참조

사이트에서 보이는 `Chapter 1`, `빛과 하늘과 땅` 같은 제목은 표시 정보이며 내부 식별자가 아니다.

### 1.3 Cut

Cut은 한 Episode 안에서 제작되는 **개별 시각 장면의 최소 관리 단위**다.

하나의 Cut은 반드시 하나의 Episode에만 소속된다.

하나의 본문 절이 여러 Cut으로 나뉠 수 있고, 하나의 Cut이 여러 절을 함께 시각화할 수도 있다.

Cut의 상세 Canonical Scene Specification은 `EPISODE_CUT_MODEL.md`가 Source of Truth다.

### 1.4 Storyboard

Storyboard는 Episode 내부의 Cut들을 **어떤 순서와 흐름으로 보여줄지 관리하는 Episode 수준의 계획표**다.

Storyboard는 Cut의 상세 장면 정의를 중복 저장하지 않는다.

Storyboard가 책임지는 최소 정보는 다음으로 한정한다.

- Cut의 표시 순서
- Cut ID
- 해당 Cut의 Scripture Anchor
- 장면의 짧은 서사적 목적 또는 beat
- 필요 시 **직전 active Cut → 현재 Cut**으로 들어오는 high-level transition note

상세 카메라, 조명, 인물 배치, 환경, Continuity 등은 Cut/Continuity 모델에서 관리한다.

## 2. 관계

기본 관계는 다음과 같다.

```text
Scripture Reference
        ↓ 1..N
     Episode
        ↓ 1..N
      Cut
```

단, Scripture Reference는 Episode와 Cut을 기계적으로 1:1 분할하는 경계가 아니다.

실제로는 다음 관계를 허용한다.

- 하나의 Episode → 하나 이상의 primary Scripture Range
- 하나의 Episode → 0개 이상의 supporting Scripture Range
- 하나의 Scripture Range → 여러 Episode에서 참조 가능
- 하나의 Episode → 여러 Cut
- 하나의 Cut → 정확히 하나의 Episode
- 하나의 Cut → 하나 이상의 Scripture Anchor 가능

Cut의 Scripture Anchor는 기본적으로 Episode의 `primary_scripture` 안에서 지정한다.
보조 본문이 Cut 해석에 직접 필요한 경우 supporting reference를 추가할 수 있지만, 보조 본문만으로 Cut의 제작 근거를 대체하지 않는다.

즉 **성경 본문은 Source이고 Episode/Cut은 Production Model**이다.

## 3. Book Code

내부 식별자에서는 성경 책 이름을 안정적인 영문 대문자 코드로 사용한다.

초기 예:

```text
GEN  Genesis
EXO  Exodus
LEV  Leviticus
NUM  Numbers
DEU  Deuteronomy
```

전체 Book Code registry는 실제 필요 시 별도 규칙으로 확정한다.

한국어 표시명은 내부 ID와 분리한다.

## 4. Episode ID

기본 형식:

```text
<BOOK>-<STORY_KEY>-<NN>
```

예:

```text
GEN-EXAMPLE-01
GEN-EXAMPLE-02
GEN-EXAMPLE-03
GEN-EXAMPLE-04
GEN-EXAMPLE-05
GEN-EXAMPLE-06
```

규칙:

1. `BOOK`은 해당 Episode의 `primary_scripture` 기준 Book Code를 사용한다.
2. `STORY_KEY`는 영문 대문자와 하이픈으로 구성한 안정적인 내부 키다.
3. `NN`은 동일 STORY_KEY 안에서 Episode를 구분하기 위한 2자리 번호다.
4. Episode ID에는 장/절 번호를 넣지 않는다.
5. 표시 제목이 바뀌어도 Episode ID는 바꾸지 않는다.
6. 한 번 사용된 Episode ID는 다른 Episode에 재사용하지 않는다.
7. Episode의 표시 순서가 바뀌어도 ID를 다시 번호 매기지 않는다.

`STORY_KEY`는 현재 ID를 안정화하기 위한 naming token으로만 사용한다.

ID가 이미 발급된 뒤 제목이나 표현 방식이 바뀌었다는 이유로 `STORY_KEY`를 다시 이름 붙이지 않는다. Episode의 정체성이 달라질 정도로 주본문이나 제작 범위가 크게 재설계되는 경우 기존 ID를 억지로 개명하기보다 새 Episode ID를 부여하는 방향을 우선한다. 기존 Episode의 폐기·대체 상태는 `EPISODE_CUT_MODEL.md`를 따른다.

STEP 0 v0 단계에서는 별도의 `Story Arc` 엔터티를 먼저 만들지 않는다. 실제로 독립된 Arc 데이터가 필요해질 때 도입 여부를 다시 검토한다.

## 5. Cut ID

기본 형식:

```text
<EPISODE_ID>-C<NN>
```

예:

```text
GEN-EXAMPLE-01-C01
GEN-EXAMPLE-01-C02
GEN-EXAMPLE-01-C03
```

규칙:

1. Cut ID는 저장소 전체에서 유일해야 한다.
2. 한 번 부여한 Cut ID는 변경하지 않는다.
3. Cut ID의 숫자는 Cut의 영구 식별을 위한 번호이며 현재 표시 순서 자체를 Source of Truth로 삼지 않는다.
4. 중간에 새 Cut이 삽입되어도 기존 Cut ID를 다시 번호 매기지 않는다.
5. 삭제·폐기된 Cut의 ID를 다른 Cut에 재사용하지 않는다.
6. 실제 Cut의 표시 순서는 Storyboard의 별도 order 값으로 관리한다.

예를 들어 기존 C02와 C03 사이에 새 Cut이 추가되더라도 C03을 C04로 바꾸지 않는다.

새 Cut에는 새로운 ID를 부여하고 Storyboard order만 조정한다.

## 6. Retired Identity Tombstone

한 번 발급되어 이력이 생긴 Episode / Cut ID는 current tree에서 production data가 제거되더라도 재사용하지 않는다.

과거 Git history만으로 재사용 여부를 매번 추론하지 않도록 `content/identity-tombstones.yaml`을 current-tree registry로 사용한다.

새 Episode / Cut ID를 발급하기 전:

1. current active tree에서 동일 ID가 없는지 확인한다.
2. `content/identity-tombstones.yaml`에 동일 ID가 없는지 확인한다.
3. 둘 중 하나에 존재하면 새 identity에 재사용하지 않는다.

tombstone은 상세 production archive가 아니며 과거 장면 정의·Prompt를 복제하지 않는다.

## 7. 표시 순서와 ID 분리

ID는 **identity**, order는 **presentation sequence**로 분리한다.

따라서 다음 값은 변경 가능하다.

- Episode 표시 순서
- Cut 표시 순서
- 표시 제목
- 사이트용 Chapter 번호

반면 다음 값은 원칙적으로 변경하지 않는다.

- Episode ID
- Cut ID

이 원칙을 통해 이미지, Prompt, Generation Run, 승인 기록, 외부 provider mapping 등이 축적된 이후에도 참조가 깨지지 않도록 한다.

## 8. Storyboard 기본 구조

개념 예:

```yaml
episode_id: GEN-EXAMPLE-01

storyboard:
  - order: 10
    cut_id: GEN-EXAMPLE-01-C01
    scripture_anchor:
      - book: GEN
        start: { chapter: 1, verse: 1 }
        end:   { chapter: 1, verse: 2 }
    beat: 태초의 혼돈과 수면 위의 어둠

  - order: 20
    cut_id: GEN-EXAMPLE-01-C02
    scripture_anchor:
      - book: GEN
        start: { chapter: 1, verse: 3 }
        end:   { chapter: 1, verse: 5 }
    beat: 빛의 출현
```

`order`는 ID가 아니므로 필요하면 재정렬할 수 있다.

초기에는 10, 20, 30처럼 간격을 두고 작성할 수 있지만, 이후 재정렬 시 값을 다시 정리해도 식별자에는 영향이 없다.

## 9. Episode 간 연결과 Canonical Sequence

Episode 간 연결은 ID 번호 자체로 추론하지 않는다.

예를 들어 `GEN-EXAMPLE-01` 다음이 항상 `GEN-EXAMPLE-02`라고 코드가 자동 추론하게 만들지 않는다.

프로젝트의 기본 역사 흐름은 별도의 **Canonical Episode Sequence**가 책임진다.

개념 예:

```yaml
episodes:
  - order: 10
    episode_id: GEN-EXAMPLE-01
  - order: 20
    episode_id: GEN-EXAMPLE-02
  - order: 30
    episode_id: GEN-EXAMPLE-03
```

원칙:

1. Canonical Sequence가 Episode의 기본 역사·제작 순서의 Source of Truth다.
2. Episode ID의 번호는 다음 Episode를 결정하지 않는다.
3. `previous_episode_id` / `next_episode_id`를 각 Episode에 중복 저장해 양방향 링크를 관리하지 않는다.
4. Episode 삽입이나 재배치는 sequence의 order만 조정하고 기존 Episode ID를 변경하지 않는다.
5. 사이트가 향후 별도의 편집 순서나 컬렉션을 필요로 하면 Canonical Sequence를 덮어쓰지 않고 별도 presentation/collection 계층을 추가한다.

이렇게 하면 다음 상황에 대응할 수 있다.

- Episode 삽입
- Episode 분할
- 사이트 표시 순서 변경
- 특별편/보충 Episode 추가
- 동일 본문을 다른 시각적 관점으로 재구성

Canonical Sequence의 물리 Source of Truth는 `content/episode-sequence.yaml`이다.

Episode 사이의 시각적 continuity 연결은 `CONTINUITY_MODEL.md`의 incoming episode boundary 규칙을 따른다.

## 10. Content Model 불변 조건

STEP 0-1 기준 핵심 invariant는 다음과 같다.

1. 성경 장/절과 Episode는 동일 개념이 아니다.
2. Episode는 실제 제작 기준인 `primary_scripture`와 필요 시 참고하는 `supporting_scripture`를 구분한다.
3. 하나의 Episode의 `primary_scripture`는 v0에서 하나의 Book Code 안에만 존재한다.
4. Episode ID의 `BOOK`은 `primary_scripture` 기준으로 정한다.
5. Episode ID는 장/절 범위와 표시 제목에 종속되지 않는다.
6. Cut은 정확히 하나의 Episode에 소속된다.
7. Episode와 Cut의 ID는 생성 후 안정적으로 유지한다.
8. Cut 표시 순서는 ID와 별도로 관리하며 Storyboard가 Source of Truth다.
9. Episode의 기본 역사 순서는 ID와 별도로 관리하며 Canonical Episode Sequence가 Source of Truth다.
10. Scripture Range는 서로 겹칠 수 있다.
11. 폴더 경로나 파일 정렬 순서는 콘텐츠 identity가 아니다.
12. 외부 이미지 생성 provider의 ID는 Content Model의 identity가 아니다.

## 11. STEP 0-1 후속 책임의 현재 해소 상태

STEP 0-1 당시 후속 단계로 넘긴 항목은 현재 다음 정식 문서에서 해소되었다.

- Episode / Cut metadata, 상태, revision, 분할 정책 → `EPISODE_CUT_MODEL.md`
- Continuity / reset → `CONTINUITY_MODEL.md`
- Library reference → `LIBRARY_MODEL.md`
- Run / Prompt snapshot → `GENERATION_RUN_MODEL.md`, `PROVIDER_INTEGRATION_MODEL.md`
- Image Asset 저장 → `ASSET_STORAGE_POLICY.md`
- 최종 물리 경로 / YAML → `REPOSITORY_STRUCTURE.md`, `templates/`
- 본문 직접 인용 / copyright → `TEXT_AND_COPYRIGHT.md`

현재 의도적으로 열어두는 Content Model 항목:

- 66권 전체 Book Code registry의 별도 파일화
- 여러 Book을 동시에 primary Scripture로 갖는 Episode가 실제 필요할 때의 확장
- 사이트용 별도 collection / editorial sequence

현재 Genesis production 시작에는 위 항목이 blocker가 아니다.

## 12. 현재 확정 상태

- Episode ID: `<BOOK>-<STORY_KEY>-<NN>`
- Cut ID: `<EPISODE_ID>-C<NN>`
- ID와 표시 순서 분리
- Scripture Range 구조화
- primary / supporting Scripture 분리
- v1에서 한 Episode의 primary Scripture는 하나의 Book Code
- Storyboard가 Cut order / scripture_anchor / beat의 Source of Truth
- Canonical Episode Sequence는 `content/episode-sequence.yaml`
- retired ID는 `content/identity-tombstones.yaml`과 current tree를 함께 확인
- 별도 Story Arc entity는 만들지 않음

**Content Model v1.1 — COMPLETED / CONFIRMED**
