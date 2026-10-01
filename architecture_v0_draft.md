# 제안하는 저장소 아키텍처 v0

> 상태: **DRAFT / STEP 0 아키텍처 정의 전 초안**
>
> 이 문서는 성경의 내용을 역사 흐름에 따라 고품질 일러스트로 구현하기 위한 `bible-illustration` 저장소의 초기 아키텍처 제안과 설계 논의를 보존하기 위한 문서다.
>
> 아직 최종 구조가 아니며, 다음 채팅부터 **STEP 0 — Architecture Definition**을 진행하면서 항목별로 검토·수정·확정한다.

---

## 1. 아키텍처 설계의 출발점

이 저장소를 단순히

`성경별 폴더 → 그림 → 프롬프트`

정도로 구성하면, 장기적으로 반복 등장 인물과 지역, 역사 고증 자료, 컷 간 연속성, 여러 이미지 생성 서비스, 생성 이력과 승인 상태가 누적될 때 구조가 쉽게 복잡해질 수 있다.

따라서 이 프로젝트는 단순 이미지 보관소가 아니라 다음을 함께 관리하는 **성경 일러스트 제작 시스템**으로 설계한다.

1. 성경 원문/본문
2. 제작 단위
3. 시각 세계관 자산
4. 생성 파이프라인
5. 결과물
6. 진행·결정 기록

OpenArt, Higgsfield, ChatGPT 이미지 생성 기능 등은 이 시스템 자체가 아니라 **이미지를 렌더링하는 외부 생성 엔진(provider)** 으로 취급한다.

핵심 개념은 다음과 같다.

> **GitHub = 제작 세계의 원본 데이터와 Source of Truth**
>
> **ChatGPT = 기획·분석·프롬프트 설계·검토**
>
> **OpenArt / Higgsfield / 기타 생성 서비스 = 렌더링 엔진**

특정 생성 서비스가 변경되거나 사라지더라도 프로젝트의 캐릭터, 장면, 역사 고증, 연속성, 제작 기록은 유지되어야 한다.

---

## 2. 제안하는 저장소 아키텍처 v0

```text
bible-illustration/
│
├─ AGENTS.md
├─ README.md
│
├─ docs/
│  ├─ architecture/
│  │  ├─ OVERVIEW.md
│  │  ├─ CONTENT_MODEL.md
│  │  ├─ PRODUCTION_PIPELINE.md
│  │  └─ ASSET_MANAGEMENT.md
│  │
│  ├─ rules/
│  │  ├─ MASTER_RULES.md
│  │  ├─ SCRIPTURE_RULES.md
│  │  ├─ VISUAL_RULES.md
│  │  ├─ CONTINUITY_RULES.md
│  │  ├─ HISTORICAL_RULES.md
│  │  ├─ TEXT_AND_COPYRIGHT.md
│  │  └─ GENERATION_RULES.md
│  │
│  ├─ decisions/
│  │  ├─ README.md
│  │  └─ DEC-0001-...
│  │
│  └─ progress/
│     ├─ CURRENT.md
│     └─ MILESTONES.md
│
├─ library/
│  ├─ characters/
│  ├─ locations/
│  ├─ objects/
│  ├─ costumes/
│  ├─ environments/
│  └─ visual-styles/
│
├─ content/
│  └─ old-testament/
│     └─ genesis/
│        └─ creation/
│           └─ GEN-CREATION-01/
│              ├─ episode.md
│              ├─ storyboard.md
│              ├─ continuity.md
│              │
│              └─ cuts/
│                 ├─ C01/
│                 │  ├─ cut.md
│                 │  ├─ prompt.md
│                 │  ├─ runs/
│                 │  └─ assets/
│                 ├─ C02/
│                 ├─ C03/
│                 └─ ...
│
├─ integrations/
│  ├─ openart/
│  │  ├─ README.md
│  │  ├─ character-bindings.yaml
│  │  └─ generation-profiles/
│  │
│  └─ higgsfield/
│     ├─ README.md
│     ├─ character-bindings.yaml
│     └─ generation-profiles/
│
└─ templates/
   ├─ episode-template.md
   ├─ cut-template.md
   ├─ character-template.md
   ├─ continuity-template.md
   └─ generation-run-template.md
```

**중요:** 위 트리는 현재 확정 구조가 아니다. 미래 확장을 예상해 제안한 개념 모델이며, STEP 0에서 필요성과 복잡도를 검토한 뒤 실제 디렉터리를 생성한다.

---

## 3. `AGENTS.md`의 역할

루트 `AGENTS.md`는 모든 제작 규칙을 한 파일에 몰아넣는 규칙집이 아니라 **저장소 탐색의 시작점이자 AI 작업 지도** 역할을 맡는다.

새 채팅이나 새로운 AI 작업자가 저장소를 열었을 때 다음을 빠르게 이해할 수 있어야 한다.

- 이 저장소가 무엇을 위한 것인지
- 무엇이 Source of Truth인지
- 어떤 문서를 어떤 순서로 읽어야 하는지
- 현재 진행 상태를 어디서 확인해야 하는지
- 하위 디렉터리에 추가 규칙이 있는지
- 작업 전에 무엇을 확인해야 하는지

향후 기본 탐색 순서는 다음 형태를 목표로 한다.

```text
AGENTS.md
→ docs/architecture/OVERVIEW.md
→ docs/rules/MASTER_RULES.md
→ docs/progress/CURRENT.md
→ 현재 작업 중인 Episode 문서
→ 필요한 세부 규칙 / library 자산
```

필요성이 확인되면 `content/AGENTS.md`, `library/AGENTS.md`, `integrations/AGENTS.md`처럼 하위 영역별 지침을 둘 수 있다. 단, 실제 필요가 생기기 전에 과도하게 만들지 않는다.

---

## 4. 성경 구조와 일러스트 제작 구조의 분리

성경의 장(chapter)과 사이트에서 보여주는 일러스트 제작 단위가 항상 일치하지 않는다.

예를 들어 제작상 “Chapter 1”이 창세기 1:1–13을 다루더라도, 성경의 창세기 1장은 1:1–31이다.

따라서 내부 데이터에서는 성경 장 번호와 별개로 **Episode ID**를 둔다.

예시:

```text
GEN-CREATION-01
GEN-CREATION-02
GEN-EDEN-01
GEN-FALL-01
GEN-CAIN-ABEL-01
GEN-FLOOD-01
```

사이트 표시 제목은 자유롭게 사용할 수 있다.

예:

`Chapter 1 — 빛과 하늘과 땅`

하지만 저장소 내부 식별자는 안정적으로 유지한다.

컷 ID 예시:

```text
GEN-CREATION-01-C01
GEN-CREATION-01-C02
GEN-CREATION-01-C03
...
```

이 ID 체계는 장기적으로 수백·수천 컷으로 확장해도 충돌하지 않아야 한다.

---

## 5. `library/` — 재사용 가능한 성경 세계 자산

`library/`는 장기적으로 이 프로젝트의 핵심 자산층이 된다.

반복 등장하는 인물, 지역, 사물, 의복, 환경, 시각 기준 등을 매 Episode마다 새로 정의하지 않고 **Canonical Asset**으로 관리한다.

예시:

```text
library/
└─ characters/
   └─ CHR-ABRAHAM/
      ├─ profile.md
      ├─ appearance.md
      ├─ age-stages.md
      ├─ costume.md
      └─ references/
```

향후 다음과 같은 식별자를 고려할 수 있다.

```text
CHR-ABRAHAM
CHR-MOSES
LOC-EDEN
LOC-EGYPT-NILE
LOC-SINAI
OBJ-ARK-COVENANT
OBJ-TABERNACLE
```

목적은 다음과 같다.

- 반복 인물의 외형 일관성
- 연령 변화 관리
- 시대별 의복 일관성
- 지역·환경 재사용
- 역사적 고증 축적
- 생성 서비스가 바뀌어도 원본 정의 유지

---

## 6. Continuity — 컷 간 연속성의 독립 관리

컷 하나의 품질뿐 아니라 **인접 컷이 같은 세계의 다음 순간처럼 이어지는가**를 독립적인 품질 기준으로 관리한다.

실제 초기 제작 과정에서 다음 문제가 확인되었다.

- 이전 컷에서 빛이 오른쪽에서 등장했는데
- 다음 컷에서 빛이 왼쪽으로 바뀌자
- 각 이미지는 개별적으로 좋더라도 장면 연결성이 크게 깨졌다.

따라서 Episode에는 `continuity` 개념이 필요하다.

관리 대상 예:

- 광원의 위치와 방향
- 카메라 높이와 시점
- 공간 방향성
- 수면·지형·건축물의 기본 구조
- 주요 인물의 위치
- 캐릭터 외형과 의상
- 색조와 날씨
- 사건 진행 방향
- 이전 컷에서 다음 컷으로 유지되어야 할 요소
- 의도적으로 변경되는 요소와 그 근거

개념 예시:

```yaml
scene:
  environment: primordial waters

camera:
  height: low-wide
  direction: forward

lighting:
  primary_source_position: right
  primary_color: warm-white-gold

water:
  tone: deep-navy
  state: primordial
  horizon: undefined

continuity:
  C02 -> C03:
    retain_water_structure: true
  C03 -> C04:
    retain_light_position: right
```

실제 저장 포맷이 Markdown인지 YAML인지는 STEP 0에서 결정한다.

핵심은 **연속성 정보를 기억이나 프롬프트에만 의존하지 않고 프로젝트 데이터로 보존한다**는 것이다.

---

## 7. 컷과 프롬프트의 배치

초기에는 최상위 `prompts/` 폴더를 고려했지만, 개별 컷용 프롬프트는 해당 컷과 함께 두는 방향이 더 자연스럽다.

예시:

```text
C04/
├─ cut.md
├─ prompt.md
├─ runs/
│  ├─ RUN-001.md
│  └─ RUN-002.md
└─ assets/
```

역할 구분:

- `cut.md`: 이 컷이 무엇을 표현해야 하는지에 대한 Canonical Scene Specification
- `prompt.md`: 현재 승인된 생성 프롬프트 또는 provider별 프롬프트
- `runs/`: 실제 생성 시도와 평가 기록
- `assets/`: 해당 컷과 직접 연결되는 이미지/참조 자산

---

## 8. 생성 Run 기록

이미지를 생성할 때 최종 결과만 남기는 것이 아니라, 의미 있는 생성 시도와 실패 이유를 기록할 수 있는 구조를 고려한다.

예:

```text
RUN-001
provider: chatgpt
result: rejected
reason: light source moved to left
```

```text
RUN-002
provider: chatgpt
result: approved
reason: maintained CUT 3 right-side light source
```

OpenArt 도입 이후 예:

```text
RUN-007
provider: openart
model: ...
character_refs: ...
result: approved
```

이 기록이 장기적으로 축적되면 다음을 분석할 수 있다.

- 어떤 생성 서비스가 어떤 유형의 장면에 강한가
- 어떤 프롬프트 구조가 효과적인가
- 반복적으로 발생하는 실패 유형은 무엇인가
- 캐릭터 일관성을 가장 잘 유지하는 방법은 무엇인가
- 생성 비용 대비 품질은 어떤가

프로젝트의 목표는 단순히 이미지를 빠르게 많이 만드는 것이 아니라, **제작 과정에서 데이터를 축적하여 시간이 지날수록 품질과 효율을 함께 개선하는 것**이다.

---

## 9. OpenArt를 Canonical Character DB로 사용하지 않는다

OpenArt를 적극 활용할 계획이지만, OpenArt 내부의 Character 슬롯이나 모델이 프로젝트의 원본 데이터가 되어서는 안 된다.

예:

```text
CHR-MOSES
```

라는 Canonical Character가 GitHub에 존재하고, OpenArt에는 그 캐릭터와 연결되는 외부 식별자만 관리하는 구조를 목표로 한다.

개념 예시:

```yaml
CHR-MOSES:
  provider: openart
  external_character_id: ...
```

Higgsfield 역시 동일하다.

```yaml
CHR-MOSES:
  provider: higgsfield
  external_character_id: ...
```

이렇게 하면 다음 상황에도 대응할 수 있다.

- OpenArt 캐릭터 슬롯 정리
- 구독 변경
- 외부 캐릭터 삭제
- 생성 서비스 교체
- 다른 AI 이미지 서비스 추가

외부 서비스를 초기화하더라도 GitHub의 Canonical Character Definition과 참조 자료를 바탕으로 다시 등록할 수 있어야 한다.

---

## 10. 생성 서비스 독립성

현재 고려 중인 생성 수단:

- ChatGPT 이미지 생성
- OpenArt
- Higgsfield

그러나 장기적으로 새로운 생성 서비스가 추가되거나 기존 서비스가 교체될 수 있다.

따라서 특정 서비스용 prompt 자체를 프로젝트의 최상위 원본으로 두지 않는다.

목표 구조:

```text
성경 본문
↓
Canonical Scene / Visual Specification
↓
Provider별 생성 설정
├─ ChatGPT prompt
├─ OpenArt prompt / profile
├─ Higgsfield prompt / profile
└─ 향후 provider
↓
Generated Asset
```

즉 **장면 정의가 먼저이고 생성 서비스용 prompt는 그 장면 정의를 렌더링하기 위한 adapter**로 취급한다.

---

## 11. 전체 제작 파이프라인 초안

장기적으로 다음 흐름을 목표로 한다.

```text
성경 본문 확인
↓
Episode 범위 결정
↓
Storyboard 설계
↓
CUT 설계
↓
이전 CUT과 Continuity 확인
↓
Canonical Visual Specification 작성
↓
ChatGPT / OpenArt / Higgsfield용 생성 설정
↓
이미지 생성
↓
본문 정확성 검토
↓
역사 고증 검토
↓
시각 품질 검토
↓
컷 연결성 검토
↓
Reject / Revise / Approve
↓
확정본 등록
↓
다음 CUT
```

각 단계의 세부 승인 조건과 기록 형식은 STEP 0에서 확정한다.

---

## 12. 과도한 선행 구조 생성 금지

장기 확장성을 고려하되, 미래에 필요할 것이라는 이유만으로 모든 폴더와 파일을 미리 만들어 놓지 않는다.

아키텍처 문서에서 먼저 개념과 책임을 검토하고, **실제 필요성이 확인된 구조만 단계적으로 생성**한다.

따라서 현재 제안된 전체 디렉터리 트리를 즉시 생성하지 않는다.

---

# STEP 0 — Architecture Definition

실제 제작 구조를 생성하기 전에 다음 7개 영역을 차례로 검토하고 확정한다.

## 0-1. Content Model

정의할 내용:

- 성경 본문과 제작 Episode의 관계
- Episode ID 규칙
- Cut ID 규칙
- 장/절 범위 표현
- Storyboard 구조
- 한 Episode와 다음 Episode의 연결 방식

## 0-2. Episode / Cut Model

정의할 내용:

- Episode가 가져야 하는 필수 데이터
- Cut이 가져야 하는 필수 데이터
- 상태 값
- 승인/수정/폐기 흐름
- 본문 분량과 사건 흐름에 따라 Episode와 Cut 수를 유연하게 조절하는 기준
- 텍스트와 이미지 관계

## 0-3. Continuity Model

정의할 내용:

- 무엇을 연속성 데이터로 기록할지
- 컷 간 연결 정보 표현 방법
- 인물 / 광원 / 카메라 / 위치 / 환경 연속성
- 시간·장소 전환 시 continuity reset 조건
- Markdown/YAML 등 저장 방식

## 0-4. Library Model

정의할 내용:

- Character
- Location
- Object
- Costume
- Environment
- Visual Style

각 자산이 어떤 식별자와 메타데이터를 가질지, Episode/Cut이 이를 어떻게 참조할지 정한다.

## 0-5. Generation Run Model

정의할 내용:

- provider
- model
- prompt version
- reference asset
- 생성 결과
- 평가
- 승인 상태
- 실패 이유
- 비용/크레딧 기록 필요 여부
- 동일 컷의 여러 생성 결과 관리 방식

## 0-6. Provider Integration Model

특히 다음을 고려한다.

### OpenArt

- consistent character 연결
- Character Builder 활용
- OpenArt character ID와 Canonical Character ID 매핑
- generation profile
- reference image
- provider별 prompt 최적화
- 캐릭터 슬롯 교체/재등록 대응

### Higgsfield

- 캐릭터/참조 자산 매핑
- 영상 확장 가능성
- provider별 생성 프로필

외부 서비스는 언제든 교체 가능하도록 dependency를 격리한다.

## 0-7. Image / Asset Storage Policy

확정해야 할 핵심 문제:

- 고해상도 원본 이미지를 일반 Git에 직접 저장할지
- Git LFS를 사용할지
- 외부 스토리지와 Git 메타데이터를 분리할지
- 테스트 이미지와 확정 이미지 보존 범위
- 생성 중간 산출물 보존 정책
- 파일명 / ID / 버전 정책
- 웹사이트 전달용 최종 이미지와 제작 원본의 관계

이 항목은 실제 이미지 수가 수백~수천 장으로 증가할 가능성을 고려해 초기에 충분히 검토한다.

---

# STEP 0 완료 후 예상 작업

STEP 0의 7개 영역이 검토·승인되면 다음을 진행한다.

1. 최종 디렉터리 구조 확정
2. 루트 `AGENTS.md` 정식화
3. Architecture 문서 생성
4. Rules 문서 체계 생성
5. Template 생성
6. Current / Decision 기록 체계 생성
7. 지금까지 진행한 Genesis Creation CUT 1–4 작업 이관
8. CUT 5 제작 재개

---

# 현재 상태

- 저장소: `limbit95/bible-illustration`
- 기본 브랜치: `main`
- 저장소는 새로 생성된 상태에서 아키텍처 정의를 시작함
- 실제 제작용 디렉터리 구조는 아직 확정하지 않음
- OpenArt는 향후 적극 활용 예정
- Higgsfield도 향후 provider 후보로 고려
- 이전 대화에서 Genesis Creation의 초기 컷 제작을 진행했으나, **아키텍처 확정 전까지 기존 작업물을 성급히 새로운 구조에 이관하지 않는다**
- 다음 작업은 이미지 제작이 아니라 **STEP 0 — Architecture Definition**부터 진행한다

---

# 다음 채팅에서의 시작 지점

새 채팅에서는 저장소의 루트 `AGENTS.md`와 이 문서를 먼저 확인한 뒤 작업을 재개한다.

다음 작업:

> **STEP 0-2 — Episode / Cut Model 정의**

STEP 0-1에서 확정한 Content Model을 전제로 Episode와 Cut이 실제 제작 과정에서 가져야 할 데이터와 상태 흐름을 설계한다.

STEP 0 진행 중 중요한 설계 결정은 대화에만 남기지 말고 이 저장소에 기록한다.


---

# STEP 0-1 — Content Model v1.0

> 상태: **CONFIRMED / 2026-10-01 사용자 승인**
>
> 목적: 성경 본문 → 제작 Episode → Cut으로 이어지는 콘텐츠 모델과 식별 체계를 먼저 안정화한다.
>
> 이 단계에서는 Episode/Cut의 세부 필드, 승인 상태, Continuity, Provider, 이미지 저장 정책까지 확정하지 않는다. 해당 항목은 이후 STEP 0-2~0-7에서 다룬다.

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

Cut의 상세 Canonical Scene Specification은 STEP 0-2에서 정의한다.

### 1.4 Storyboard

Storyboard는 Episode 내부의 Cut들을 **어떤 순서와 흐름으로 보여줄지 관리하는 Episode 수준의 계획표**다.

Storyboard는 Cut의 상세 장면 정의를 중복 저장하지 않는다.

Storyboard가 책임지는 최소 정보는 다음으로 한정한다.

- Cut의 표시 순서
- Cut ID
- 해당 Cut의 Scripture Anchor
- 장면의 짧은 서사적 목적 또는 beat
- 필요 시 앞뒤 Cut 전환 메모

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
GEN-CREATION-01
GEN-CREATION-02
GEN-EDEN-01
GEN-FALL-01
GEN-CAIN-ABEL-01
GEN-FLOOD-01
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

ID가 이미 발급된 뒤 제목이나 표현 방식이 바뀌었다는 이유로 `STORY_KEY`를 다시 이름 붙이지 않는다. Episode의 정체성이 달라질 정도로 주본문이나 제작 범위가 크게 재설계되는 경우 기존 ID를 억지로 개명하기보다 새 Episode ID를 부여하는 방향을 우선한다. 기존 Episode의 폐기·대체 상태 표현은 STEP 0-2에서 정의한다.

STEP 0 v0 단계에서는 별도의 `Story Arc` 엔터티를 먼저 만들지 않는다. 실제로 독립된 Arc 데이터가 필요해질 때 도입 여부를 다시 검토한다.

## 5. Cut ID

기본 형식:

```text
<EPISODE_ID>-C<NN>
```

예:

```text
GEN-CREATION-01-C01
GEN-CREATION-01-C02
GEN-CREATION-01-C03
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

## 6. 표시 순서와 ID 분리

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

## 7. Storyboard 기본 구조

개념 예:

```yaml
episode_id: GEN-CREATION-01

storyboard:
  - order: 10
    cut_id: GEN-CREATION-01-C01
    scripture_anchor:
      - book: GEN
        start: { chapter: 1, verse: 1 }
        end:   { chapter: 1, verse: 2 }
    beat: 태초의 혼돈과 수면 위의 어둠

  - order: 20
    cut_id: GEN-CREATION-01-C02
    scripture_anchor:
      - book: GEN
        start: { chapter: 1, verse: 3 }
        end:   { chapter: 1, verse: 5 }
    beat: 빛의 출현
```

`order`는 ID가 아니므로 필요하면 재정렬할 수 있다.

초기에는 10, 20, 30처럼 간격을 두고 작성할 수 있지만, 이후 재정렬 시 값을 다시 정리해도 식별자에는 영향이 없다.

## 8. Episode 간 연결과 Canonical Sequence

Episode 간 연결은 ID 번호 자체로 추론하지 않는다.

예를 들어 `GEN-CREATION-01` 다음이 항상 `GEN-CREATION-02`라고 코드가 자동 추론하게 만들지 않는다.

프로젝트의 기본 역사 흐름은 별도의 **Canonical Episode Sequence**가 책임진다.

개념 예:

```yaml
episodes:
  - order: 10
    episode_id: GEN-CREATION-01
  - order: 20
    episode_id: GEN-CREATION-02
  - order: 30
    episode_id: GEN-EDEN-01
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

Canonical Sequence의 최종 파일명과 저장 위치는 최종 디렉터리 구조를 확정할 때 결정한다.

Episode 사이의 시각적 continuity 연결은 STEP 0-3에서 별도로 정의한다.

## 9. Content Model 불변 조건

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

## 10. STEP 0-1에서 의도적으로 미확정하는 항목

다음은 이번 단계에서 확정하지 않는다.

- Episode 필수 메타데이터 전체
- Cut 필수 메타데이터 전체
- Draft / Approved / Rejected 등의 상태 값
- 승인·수정·폐기 흐름
- Episode당 기본 Cut 수 8개의 취급
- Cut의 Canonical Scene Specification 필드
- Continuity 필드와 reset 규칙
- Markdown/YAML 최종 저장 포맷
- Storyboard/episode/cut 파일의 최종 경로
- Provider prompt 구조
- 이미지 저장 방식

이 항목들은 STEP 0-2 이후에서 순차적으로 정의한다.

## 11. STEP 0-1 확정 사항

다음 항목은 사용자 승인으로 확정되었다.

- Episode ID 형식 `<BOOK>-<STORY_KEY>-<NN>` 유지 여부
- Cut ID 형식 `<EPISODE_ID>-C<NN>` 유지 여부
- ID와 표시 순서를 분리하는 원칙
- Scripture Range를 구조화된 범위 정보로 관리하는 원칙
- `primary_scripture` / `supporting_scripture`를 구분하는 원칙
- v0에서 한 Episode의 `primary_scripture`를 하나의 Book Code로 제한하는 원칙
- Storyboard를 Episode 내부 Cut 순서의 Source of Truth로 두는 원칙
- Canonical Episode Sequence를 Episode 기본 순서의 Source of Truth로 두는 원칙
- v0에서는 별도의 Story Arc 엔터티를 만들지 않는 원칙

위 항목은 2026-10-01 사용자 승인으로 확정되었다.

**STEP 0-1 — Content Model: COMPLETED / CONFIRMED**

다음 작업은 **STEP 0-2 — Episode / Cut Model**이다.


---

# STEP 0-2 — Episode / Cut Model Working Draft v0.5

> 상태: **REVIEW READY / 상태 모델 분리 반영**
>
> 선행 조건: **STEP 0-1 — Content Model v1.0 CONFIRMED**
>
> 목적: Episode와 Cut이 실제 제작 과정에서 가져야 할 최소 데이터, 상태 흐름, 본문 기반 Episode/Cut 분할 정책, 텍스트와 이미지의 관계를 정의한다.
>
> 이 단계에서는 Continuity의 상세 필드, Library 자산 스키마, Provider별 Prompt/Run, 이미지 저장 경로와 파일 정책은 확정하지 않는다.

## 1. 설계 원칙

STEP 0-2는 다음 원칙을 따른다.

1. Episode와 Cut은 **제작 의도와 기준을 보존하는 Canonical Production Data**다.
2. 생성된 이미지나 Provider Prompt가 Episode/Cut의 원본 정의를 대체하지 않는다.
3. Episode는 전체 제작 단위의 범위와 목적을 정의하고, Cut은 개별 장면의 의도를 정의한다.
4. 동일한 내용을 Episode, Storyboard, Cut에 반복 저장하지 않는다.
5. 상태 값은 제작 진행을 이해할 수 있을 만큼만 두고 Provider 실행 상태와 혼합하지 않는다.
6. 승인된 정의를 크게 바꿀 때 과거 승인 이력이 사라지지 않도록 revision 개념을 둔다.
7. Episode와 Cut의 수는 미리 정한 숫자가 아니라 **성경 본문의 분량, 사건 흐름, 시각적 전환점**에 따라 결정한다.

## 2. Episode Model

Episode는 하나의 시각적·서사적 제작 범위를 정의한다.

### 2.1 Episode 필수 데이터

개념상 Episode가 가져야 하는 필수 데이터는 다음과 같다.

```yaml
episode_id: GEN-CREATION-01
title: 빛과 하늘과 땅

primary_scripture:
  - book: GEN
    start: { chapter: 1, verse: 1 }
    end:   { chapter: 1, verse: 13 }

supporting_scripture: []

production_intent: >
  이 Episode가 어떤 본문 흐름을 어떤 시각적 목적 아래 묶어 보여주는지 설명한다.

definition_status: draft
revision: 1
```

각 필드의 책임:

- `episode_id`: STEP 0-1에서 확정한 영구 식별자
- `title`: 사람에게 보여주는 작업/표시 제목. 변경 가능
- `primary_scripture`: 직접 시각화하는 기준 본문
- `supporting_scripture`: 병행·보조·역사적 맥락 참고 본문
- `production_intent`: 왜 이 범위를 하나의 Episode로 묶었는지와 제작상의 핵심 목적
- `definition_status`: Episode 정의의 설계·검토·승인 상태
- `revision`: Episode 정의의 승인 이력을 추적하기 위한 정수 revision

### 2.2 Episode에 직접 저장하지 않는 데이터

다음 정보는 Episode 자체에 중복 저장하지 않는다.

- Cut의 실제 표시 순서 → Storyboard
- Cut별 상세 장면 → 각 Cut
- 인물·지역·사물의 Canonical 정의 → STEP 0-4 Library Model
- Cut 간 광원·카메라·위치 연속성 → STEP 0-3 Continuity Model
- Provider Prompt와 Generation Run → STEP 0-5/0-6
- 실제 이미지 파일 위치와 파생본 → STEP 0-7

Episode는 이 정보를 필요 시 **참조**할 수 있지만 원본 정의를 복제하지 않는다.

## 3. Cut Model

Cut은 하나의 Episode에 속하는 개별 시각 장면의 Canonical Specification이다.

Cut은 단순히 “생성할 이미지 한 장”이 아니다.

Cut의 본질은 **어떤 순간과 의미를 시각적으로 표현해야 하는지 정의하는 장면 단위**이며, 실제 생성 결과는 그 정의를 구현한 산출물이다.

### 3.1 Cut 필수 데이터

개념상 Cut이 가져야 하는 필수 데이터는 다음과 같다.

```yaml
cut_id: GEN-CREATION-01-C01
episode_id: GEN-CREATION-01

scene_intent: >
  관객이 이 Cut을 통해 반드시 이해해야 하는 사건·상태·정서를 설명한다.

scene:
  summary: >
    화면에 표현되어야 하는 Canonical Scene의 핵심 요약.
  required_elements: []
  forbidden_elements: []

definition_status: draft
revision: 1
```

각 필드의 책임:

- `cut_id`: STEP 0-1에서 확정한 영구 식별자
- `episode_id`: 정확히 하나의 소속 Episode
- `scene_intent`: 장면의 의미와 반드시 전달되어야 할 제작 의도
- `scene.summary`: 이미지 생성과 검토의 기준이 되는 장면 설명
- `scene.required_elements`: 반드시 존재해야 하는 핵심 요소
- `scene.forbidden_elements`: 본문 왜곡이나 잘못된 표현을 막기 위해 명시적으로 금지할 요소
- `definition_status`: Cut Canonical Scene 정의의 설계·검토·승인 상태
- `revision`: Canonical Cut Specification의 revision

### 3.2 Cut의 상세 시각 필드는 단계적으로 확장한다

다음은 Cut에 필요할 가능성이 높지만 STEP 0-2에서 세부 스키마를 확정하지 않는다.

- 인물과 인물 상태
- 장소
- 주요 사물
- 시간대
- 사건/행동
- 카메라
- 구도
- 조명
- 날씨
- 색감
- 의상
- 표정
- 공간 방향

이 중 무엇이 Cut 자체의 Canonical Scene 데이터이고 무엇이 Continuity/Library 참조인지는 STEP 0-3과 STEP 0-4에서 경계를 확정한다.

따라서 지금은 `scene.summary / required_elements / forbidden_elements`를 최소 핵심으로 정의한다.

## 4. Storyboard와 Cut의 책임 분리

STEP 0-1에서 Storyboard는 Episode 내부 Cut 순서의 Source of Truth로 확정되었다.

따라서 다음처럼 책임을 분리한다.

### Storyboard

- Cut 표시 순서
- Cut ID
- Scripture Anchor
- 짧은 beat
- 필요 시 Cut 간 transition note

### Cut

- Cut의 Canonical Scene Specification
- Scene intent
- 필수/금지 요소
- 상태
- revision

Storyboard에 Cut의 상세 Scene Specification을 복사하지 않는다.

STEP 0-1에서 Storyboard의 최소 책임으로 확정된 `scripture_anchor`와 `beat`는 **Storyboard를 단일 Source of Truth로 둔다.**

Cut 문서가 해당 값을 필요로 할 때는 자신의 `cut_id`로 Storyboard entry를 참조한다. 동일한 `scripture_anchor`나 `beat`를 Cut에 다시 복사해 두 군데를 동기화하지 않는다.

## 5. Episode Definition Status

Episode의 상태는 이미지 제작 진행률이 아니라 **Episode 정의 자체의 설계·검토·승인 상태**만 나타낸다.

기본 상태:

```text
draft
↓
in_review
↓
approved
```

검토 후 수정이 필요하면:

```text
in_review
→ revision_requested
→ draft
```

보조 종료 상태:

```text
cancelled
superseded
```

의미:

- `draft`: Episode 범위, 본문, production intent, Storyboard를 설계 중
- `in_review`: Episode 정의를 승인하기 위해 검토 중
- `revision_requested`: Episode 정의 수정이 필요함
- `approved`: 현재 revision의 Episode 정의가 Canonical 기준으로 승인됨
- `cancelled`: 승인 전에 Episode 정의 자체가 취소됨
- `superseded`: 과거 승인 Episode가 **다른 Episode ID**로 대체되어 더 이상 현행 기준이 아님

Episode가 `approved`라는 것은 “모든 이미지 제작이 끝났다”는 뜻이 아니다.

정확한 의미는:

> **이 Episode의 범위와 Storyboard, active Cut 구성과 제작 의도가 이미지 제작에 사용할 Canonical 기준으로 승인되었다.**

### Episode 정의 승인 조건

Episode를 `approved`로 만들기 위한 최소 조건은 다음으로 둔다.

1. Episode 필수 데이터가 존재한다.
2. Storyboard가 존재한다.
3. Storyboard에 포함된 모든 active Cut의 **definition_status가 approved**다.
4. Storyboard 순서와 실제 Cut 참조가 유효하다.
5. Episode의 주본문 범위를 의도적으로 누락하거나 중복한 부분이 없는지 검토되었다.

Continuity 정의 승인 조건은 STEP 0-3에서 추가될 수 있다.

## 6. Cut Definition Status

Cut의 상태도 이미지 생성 진행률이 아니라 **Canonical Scene 정의의 설계·검토·승인 상태**만 나타낸다.

기본 상태:

```text
draft
↓
in_review
↓
approved
```

검토 후 수정이 필요하면:

```text
in_review
→ revision_requested
→ draft
```

보조 종료 상태:

```text
cancelled
superseded
```

의미:

- `draft`: Canonical Scene Specification 작성 중
- `in_review`: Scripture Anchor, Scene Intent, 필수/금지 요소 등을 검토 중
- `revision_requested`: 장면 정의 수정이 필요함
- `approved`: 현재 revision의 Canonical Scene 정의가 이미지 제작 기준으로 승인됨
- `cancelled`: 승인 전에 Cut 정의 자체가 취소됨
- `superseded`: 이미 승인된 Cut이 **다른 Cut ID**로 대체되어 더 이상 현행 기준이 아님

Cut의 `approved`는 최종 이미지 승인과 분리한다.

즉:

```text
Cut definition approved
        ↓
Generation / Rendering
        ↓
Generated Asset review
        ↓
Representative Asset approved
```

Cut 정의가 승인된 뒤 여러 번 이미지를 생성할 수 있으며, 생성 결과의 성공·실패·승인 상태는 STEP 0-5 Generation Run Model과 STEP 0-7 Asset 정책에서 관리한다.

### Cut 정의 승인 조건

Cut의 `definition_status`를 `approved`로 만들기 위한 최소 조건은 다음과 같다.

1. Cut 필수 Canonical Scene 데이터가 존재한다.
2. 해당 Cut의 Storyboard entry가 존재한다.
3. Storyboard의 `scripture_anchor`와 `beat`가 유효하다.
4. Scene Intent와 required/forbidden elements가 본문과 충돌하지 않는지 검토되었다.
5. 이미지 생성에 사용할 수 있을 만큼 장면 정의가 명확하다.

대표 생성 이미지의 존재 여부는 **Cut 정의 승인 조건이 아니다.**

Continuity 정의 관련 승인 조건은 STEP 0-3에서 추가될 수 있다.

## 7. 승인 후 수정과 Revision

Episode와 Cut의 ID는 identity이고 revision은 정의의 버전이다.

예:

```text
GEN-CREATION-01-C03
revision: 1
```

승인 후 의미 있는 Canonical Scene 변경이 필요하면 같은 Cut ID 아래 revision을 증가시킨다.

```text
GEN-CREATION-01-C03
revision: 2
```

원칙:

1. 최초 승인 전의 설계 수정은 기본적으로 `revision: 1` 안에서 진행한다.
2. 오탈자처럼 의미를 바꾸지 않는 수정은 revision 증가를 강제하지 않는다.
3. 승인 이후 Scripture Anchor, Scene Intent, 주요 등장 요소, 사건 표현처럼 생성 결과를 바꿀 수 있는 수정은 revision을 증가시킨다.
4. Generation Run은 어떤 Cut revision을 기준으로 생성했는지 추적할 수 있어야 한다.
5. 승인된 이전 revision의 기록을 삭제하지 않는다.
6. Cut의 정체성 자체가 바뀌는 경우 revision으로 억지로 유지하지 않고 새 Cut ID를 발급한다.

### Episode revision 규칙

Episode도 동일한 원칙을 따른다.

승인 이후 다음과 같은 변경은 Episode revision 증가 대상으로 본다.

- `primary_scripture`의 의미 있는 범위 변경
- `production_intent`의 의미 있는 변경
- active Cut 구성의 추가/삭제/교체
- 이야기 흐름을 바꾸는 Storyboard 순서 변경

단순 제목 수정이나 오탈자 수정처럼 제작 의미를 바꾸지 않는 변경은 revision 증가를 강제하지 않는다.

### Revision 증가 시 상태 전환

승인된 정의에 의미 있는 변경이 생기면 revision만 증가시키고 `approved` 상태를 그대로 유지하지 않는다.

- Episode: 새 revision 생성 → `definition_definition_status: draft`
- Cut: 새 revision 생성 → `definition_definition_status: draft`

새 revision은 다시 검토와 승인을 거쳐야 한다.

이때 이전 승인 revision은 기록으로 남지만 **현재 canonical revision은 최신 revision**이다.

`superseded`는 revision 증가에 사용하지 않는다. 같은 ID의 revision 변경은 동일한 Episode/Cut의 발전이며, `superseded`는 다른 Episode ID 또는 Cut ID가 기존 항목을 대체할 때만 사용한다.

revision 이력의 실제 저장 방식은 최종 파일 구조와 Generation Run Model을 함께 검토한 뒤 확정한다.

## 8. 본문 기반 Episode / Cut 분할 정책

이 프로젝트에서는 Episode당 Cut 수에 기본값, 최소값, 최대값을 두지 않는다.

Cut 수와 Episode 경계는 **성경 본문의 실제 분량과 사건 구조**를 기준으로 정한다.

원칙:

1. 짧고 단순한 본문은 적은 수의 Cut으로 구성할 수 있다.
2. 하나의 절이라도 시각적으로 서로 다른 순간이나 사건이 중요하면 여러 Cut으로 나눌 수 있다.
3. 여러 절이 하나의 동일한 장면이나 사건을 설명하면 하나의 Cut으로 묶을 수 있다.
4. 성경의 한 장이 길거나 여러 사건·장소·시간 전환을 포함하면 하나의 Episode에 모두 압축하지 않는다.
5. 필요한 경우 **하나의 성경 장을 여러 Episode로 분할**한다.
6. 반대로 짧은 장이나 연속된 사건은 필요하면 장 경계를 넘어 하나의 Episode로 구성할 수 있다. 단, STEP 0-1의 primary Scripture 규칙을 따른다.
7. Cut을 늘리거나 줄이는 목적은 숫자를 맞추는 것이 아니라 본문 흐름을 정확하고 자연스럽게 시각화하는 것이다.

예:

```text
짧은 본문
성경 본문
→ Episode 1
   → C01
   → C02
   → C03

긴 성경 장
성경 1장
→ Episode 1
   → C01 ... C05
→ Episode 2
   → C01 ... C07
→ Episode 3
   → C01 ... C04
```

위 숫자는 예시일 뿐 고정 규칙이 아니다.

### 8.1 성경 Chapter와 사이트 Chapter의 구분

성경의 Chapter 번호와 제작 Episode, 사이트에서 보여주는 Chapter는 동일한 개념으로 묶지 않는다.

```text
Biblical Chapter
      ↓
1개 이상의 Episode
      ↓
각 Episode의 Storyboard / Cuts
      ↓
필요 시 사이트용 Chapter 표시
```

따라서 성경의 한 장이 길다면 여러 Episode로 나눈 뒤 사이트에서도 여러 Chapter처럼 보여줄 수 있다.

반대로 짧은 성경 장이라고 해서 반드시 하나의 독립 사이트 Chapter를 만들어야 하는 것은 아니다.

사이트용 Chapter의 구체적인 표시·그룹핑 방식은 추후 presentation 계층을 설계할 때 결정한다.

### 8.2 Cut 수의 Source of Truth

Episode의 Cut 수는 별도 숫자 필드로 저장하지 않는다.

현재 Episode에 속한 **active Storyboard entry의 수**가 실제 Cut 수다.

따라서 `target_cut_count`, `cut_count` 같은 값을 Episode에 중복 저장하여 Storyboard와 동기화하지 않는다.

필요한 경우 UI나 자동화에서 Storyboard를 기준으로 Cut 수를 계산한다.

## 9. 텍스트와 이미지의 관계

이 프로젝트에서 텍스트는 세 종류로 구분한다.

### 9.1 Scripture Reference

성경 본문의 위치 정보다.

예:

```text
Genesis 1:1–2
```

Canonical Scene의 근거이며 이미지보다 상위의 Source다.

특정 번역본의 본문 전문 저장 여부와 저작권 정책은 별도 Rules 단계에서 결정한다.

### 9.2 Production Text

제작을 위해 작성하는 텍스트다.

예:

- Episode production intent
- Storyboard beat
- Cut scene intent
- Canonical Scene summary
- 제작 메모

이 텍스트는 내부 제작 정의이며 Scripture 자체가 아니다.

### 9.3 Display Text

사이트나 최종 결과물에서 사용자에게 보여줄 수 있는 텍스트다.

예:

- Episode 표시 제목
- Cut caption
- 설명문
- 인용문

Display Text는 Canonical Scene과 별도로 관리한다.

번역본 인용이나 본문 전문이 들어가는 경우 저작권 규칙을 따라야 한다.

## 10. Cut 정의 승인과 이미지 제작의 분리

Episode/Cut의 Definition Status와 이미지 제작 상태는 서로 다른 축으로 관리한다.

```text
Episode / Cut Definition
        ↓ approved
Rendering / Generation
        ↓
Generated Asset Review
        ↓
Production Complete
```

원칙:

1. Episode/Cut의 `approved`는 **정의 승인**을 뜻한다.
2. 이미지 제작이 시작되거나 완료되어도 Episode/Cut의 Definition Status를 `in_production` 같은 값으로 바꾸지 않는다.
3. 이미지 생성 진행률과 Run 성공/실패는 STEP 0-5 Generation Run Model이 책임진다.
4. 대표 승인 이미지와 Asset 상태는 STEP 0-7 Image / Asset Storage Policy에서 책임진다.
5. 필요하다면 향후 UI에서 `production_status`를 계산해 보여줄 수 있지만, STEP 0-2에서는 Episode/Cut Canonical 데이터에 이를 중복 저장하지 않는다.
6. 따라서 설계는 승인됐지만 이미지가 아직 없는 Cut도 정상적인 상태다.
7. 새 Provider로 이미지를 다시 생성하더라도 Canonical Scene 정의가 바뀌지 않았다면 Cut revision과 Definition Status는 그대로 유지할 수 있다.

### Production Complete의 개념

이미지 제작 완료 여부는 Definition Status와 별도로 판단한다.

초기 개념상 Cut이 production complete가 되려면 현재 승인된 Cut revision을 기준으로 **대표 승인 Asset이 최소 하나 존재**해야 한다.

Episode의 production complete는 active Cut 전체가 production complete일 때 도출할 수 있다.

이 값의 정확한 저장/계산 방법은 STEP 0-5와 STEP 0-7에서 확정한다.

## 11. 이미지와 Cut의 관계

Cut과 이미지의 관계는 다음 원칙으로 정의한다.

```text
Cut Canonical Specification
        ↓
Generation Run 1 → image A (rejected)
Generation Run 2 → image B (rejected)
Generation Run 3 → image C (approved)
```

따라서:

1. Cut 하나는 여러 Generation Run을 가질 수 있다.
2. Run 하나는 하나 이상의 생성 결과를 만들 수 있다.
3. 생성 이미지는 Cut 자체가 아니다.
4. 승인 이미지가 존재해도 Cut의 Canonical Specification은 별도로 유지한다.
5. 외부 Provider에서 이미지가 삭제되어도 Cut 정의는 남아 있어야 한다.
6. 대표 승인 이미지가 어떤 Asset인지 연결할 수 있어야 한다.

Generation Run과 Asset의 상세 식별 체계는 STEP 0-5와 STEP 0-7에서 확정한다.

## 12. Active Cut과 종료 상태

Storyboard에서 현재 제작 흐름에 포함되는 Cut을 **active Cut**으로 본다.

- `cancelled` Cut은 active Cut이 아니다.
- `superseded` Cut은 active Cut이 아니다.
- Storyboard의 현재 canonical sequence에 포함되어 있고 종료 상태가 아닌 Cut이 active Cut이다.

Episode 승인 조건에서 말하는 “모든 active Cut 승인”은 이 정의를 따른다.

## 13. Cut 삭제보다 Cancel / Supersede를 우선한다

ID가 발급되고 제작 기록이 생긴 Cut은 가급적 물리적으로 삭제하지 않는다.

상황별 기본 원칙:

- 아직 아무 이력도 없는 실수 생성 → 삭제 가능
- Storyboard에서 제외됐지만 제작 이력이 존재 → `cancelled`
- 승인되었거나 다른 기록에서 참조되는 Cut이 **다른 Cut ID**로 대체 → `superseded`

이렇게 해야 과거 Prompt, Run, 이미지 평가, Continuity 참조가 고아 데이터가 되지 않는다.

Episode에도 동일한 원칙을 적용한다.

## 14. STEP 0-2 불변 조건 후보

이번 단계에서 확정할 핵심 invariant 후보는 다음과 같다.

1. Episode는 제작 범위와 목적의 Canonical Source다.
2. Cut은 개별 장면 정의의 Canonical Source다.
3. 이미지와 Prompt는 Cut의 원본 정의를 대체하지 않는다.
4. Cut은 정확히 하나의 Episode에 소속된다.
5. Storyboard가 active Cut의 실제 표시 순서를 결정한다.
6. Storyboard가 `scripture_anchor`와 `beat`의 단일 Source of Truth다.
7. Episode와 Cut의 수에는 고정 기본값·최소값·최대값을 두지 않고 본문 구조에 따라 결정한다.
8. 개별 생성 실패는 Cut의 `rejected` 상태로 표현하지 않고 Generation Run에서 기록한다.
9. 승인 후 의미 있는 정의 변경은 revision으로 추적하고 새 revision은 다시 승인을 거친다.
10. `superseded`는 revision 변경이 아니라 다른 Episode/Cut ID에 의해 대체될 때 사용한다.
11. 이미 이력이 생긴 Episode/Cut은 삭제보다 `cancelled` 또는 `superseded`를 우선한다.
12. Episode/Cut의 `approved`는 이미지 제작 완료가 아니라 **Canonical 정의 승인**을 뜻한다.
13. Episode가 승인되려면 Storyboard의 모든 active Cut 정의가 승인되어야 한다.
14. Cut 정의 승인에는 대표 생성 이미지가 필요하지 않다.
15. 이미지 제작 완료 여부는 Definition Status와 분리하며 Generation Run/Asset 데이터를 기준으로 판단한다.
16. 실제 Cut 수의 Source of Truth는 active Storyboard entry의 수이며 Episode에 별도 Cut count 값을 중복 저장하지 않는다.
17. Scripture Reference, Production Text, Display Text는 서로 다른 책임을 가진다.
18. 생성 이미지는 Canonical Cut Specification의 구현 결과이며 Source of Truth가 아니다.

## 15. STEP 0-2에서 의도적으로 미확정하는 항목

다음은 이후 STEP에서 정한다.

- 카메라/광원/공간 방향 등 Continuity 상세 필드
- Character / Location / Object 등 Library 참조 스키마
- Provider별 Prompt 구조
- Generation Run ID와 평가 필드
- 이미지 Asset ID와 실제 저장 위치
- 승인 이미지 여러 개/파생 비율/사이트별 variant 정책
- Markdown/YAML 최종 저장 형식
- revision의 물리적 파일 저장 방식
- 성경 번역본 전문 저장 및 저작권 규칙

## 16. STEP 0-2 검토 포인트

사용자 검토가 필요한 핵심 항목:

- Episode 필수 데이터 범위
- Cut 최소 Canonical Scene 데이터 범위
- Storyboard를 `scripture_anchor`와 `beat`의 단일 Source of Truth로 두는 원칙
- Episode Definition Status 모델
- Cut Definition Status 모델
- Definition 승인과 이미지 제작 완료를 분리하는 원칙
- 승인 후 revision 정책과 revision 증가 시 상태 재진입 규칙
- `superseded`를 다른 ID에 의한 대체에만 사용하는 원칙
- Episode/Cut 정의 승인 최소 조건
- production complete를 Generation Run/Asset 기반으로 별도 판단하는 원칙
- 고정 Cut 수를 두지 않고 본문 분량과 사건 흐름에 따라 Episode/Cut을 유연하게 분할하는 원칙
- 긴 성경 장을 여러 Episode로 분할할 수 있는 원칙
- 실제 Cut 수는 Storyboard에서 계산하고 Episode에 중복 저장하지 않는 원칙
- Scripture / Production / Display Text의 분리
- Cut과 Generated Image를 분리하는 원칙
- 삭제보다 Cancel / Supersede를 우선하는 원칙

이 항목들이 확정되면 STEP 0-2를 CONFIRMED로 전환하고 STEP 0-3 — Continuity Model로 진행한다.
