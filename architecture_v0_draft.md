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

> **STEP 0-7 — Image / Asset Storage Policy 정의**

확정된 Content / Episode-Cut / Continuity / Library / Generation Run / Provider Integration Model을 전제로 원본·후보·최종·Reference 이미지의 Asset ID, 파일명, 저장 위치, 보존·폐기·파생본 정책과 Git/Git LFS/외부 저장소의 역할을 설계한다.

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

# STEP 0-2 — Episode / Cut Model v1.0

> 상태: **CONFIRMED / 2026-10-01 사용자 승인**
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

- Episode: 새 revision 생성 → `definition_status: draft`
- Cut: 새 revision 생성 → `definition_status: draft`

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

다음 항목은 사용자 승인으로 확정되었다:

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

위 항목은 2026-10-01 사용자 승인으로 확정되었다.

**STEP 0-2 — Episode / Cut Model: COMPLETED / CONFIRMED**

다음 작업은 **STEP 0-3 — Continuity Model**이다.


---

# STEP 0-3 — Continuity Model v1.0

> 상태: **CONFIRMED / 2026-10-01 사용자 승인**
>
> 선행 조건:
> - **STEP 0-1 — Content Model v1.0 CONFIRMED**
> - **STEP 0-2 — Episode / Cut Model v1.0 CONFIRMED**
>
> 목적: 인접 Cut과 필요 시 Episode 경계 사이에서 무엇이 유지되고, 무엇이 의도적으로 변화하며, 어디서 continuity가 reset되는지를 Canonical 데이터로 정의한다.
>
> 이 단계에서는 Character / Location / Object의 최종 Library 스키마, Provider별 reference 전달 방식, 이미지 Asset 저장 방식은 확정하지 않는다.

## 1. Continuity의 역할

Continuity는 각 Cut의 장면 설명을 반복 저장하는 문서가 아니다.

Continuity의 책임은 다음 질문에 답하는 것이다.

> **이전 장면에서 다음 장면으로 넘어갈 때 무엇이 같은 세계의 연속으로 유지되어야 하고, 무엇이 사건 진행에 따라 달라져야 하는가?**

따라서 Continuity는 **Cut과 Cut 사이의 관계 데이터**로 취급한다.

기본 구조:

```text
Cut A Canonical Scene
        ↓
Continuity Transition
        ↓
Cut B Canonical Scene
```

이미지 생성 결과는 Continuity의 Source of Truth가 아니다.

승인 이미지가 존재하더라도 Canonical Continuity 정의가 별도로 남아 있어야 한다.

## 2. Source of Truth 책임 분리

연속성 정보가 여러 곳에 중복되지 않도록 역할을 분리한다.

### Library — 장기적으로 변하지 않는 Canonical 정의

예:

- 인물의 기본 외형
- 특정 age stage
- 지역의 기본 지형
- 주요 사물의 기본 형태
- 시대별 의복 기준

상세 스키마는 STEP 0-4에서 확정한다.

### Cut — 현재 장면 자체의 정의

예:

- 이 Cut에서 일어나는 사건
- 이 장면에 반드시 필요한 요소
- 금지 요소
- 현재 장면의 핵심 시각 상태

### Continuity — 장면 사이의 유지 / 변화 규칙

예:

- 같은 인물의 의상이 다음 Cut에서도 유지되어야 함
- 오른쪽에 있던 광원이 다음 Cut에서도 오른쪽에 있어야 함
- 인물이 화면 왼쪽에서 오른쪽으로 이동 중임
- 이전 Cut에서 들고 있던 물건을 다음 Cut에서도 들고 있어야 함
- 시간이 밤으로 바뀌므로 조명 상태는 reset됨

즉:

```text
Library = 무엇인가
Cut = 지금 무엇을 보여주는가
Continuity = 다음 장면으로 무엇이 이어지고 무엇이 변하는가
```

## 3. Continuity의 범위

Continuity는 모든 Episode와 모든 Cut 사이에 무조건 동일하게 적용하지 않는다.

세 가지 범위를 구분한다.

### 3.1 Intra-Episode Continuity

같은 Episode 안에서 Storyboard상 연속된 active Cut 사이의 관계다.

기본적으로 모든 인접 active Cut 쌍은 다음 중 하나로 분류되어야 한다.

- `continue`
- `partial_reset`
- `reset`

### 3.2 Cross-Episode Continuity

Episode가 나뉘었다고 해서 continuity가 자동으로 끊기거나 자동으로 이어지는 것은 아니다.

예를 들어 긴 성경 장을 제작 편의상 두 Episode로 나눴지만 사건은 같은 장소와 시간에서 바로 이어질 수 있다.

이 경우 이전 Episode의 마지막 Cut과 다음 Episode의 첫 Cut 사이에 **명시적인 continuity transition**을 둔다.

반대로 Canonical Episode Sequence상 바로 다음 Episode라도 시간·장소·사건이 크게 바뀐다면 `reset`이다.

**Episode 순서 자체로 continuity를 추론하지 않는다.**

### 3.3 No Continuity Requirement

서로 비교할 필요가 없는 Cut/Episode 사이에는 continuity 관계를 만들지 않는다.

Canonical Sequence 전체를 거대한 연속 장면처럼 취급하지 않는다.

## 4. Continuity Transition Mode

인접 장면의 관계는 세 가지 모드로 구분한다.

### continue

같은 장면·공간·시간 흐름이 이어진다.

단, `continue`라고 해서 모든 시각 요소를 자동 상속하지 않는다.

**Segment baseline과 명시적인 retain constraint만 유지 의무를 가진다.** 그 밖의 기록되지 않은 값은 생성 자유도를 유지한다.

예:

```text
C02
빛이 오른쪽에서 등장
↓ continue
C03
같은 오른쪽 광원을 유지하며 밝기 증가
```

### partial_reset

일부 continuity 축은 유지되지만 일부는 새로 설정된다.

예:

- 같은 인물이지만 시간이 며칠 뒤로 이동
- 같은 장소지만 날씨가 바뀜
- 같은 사건이지만 카메라 축을 의도적으로 변경
- 인물 외형은 유지하지만 의복이 사건상 변경됨

유지되는 항목과 reset되는 항목을 명시한다.

### reset

새로운 시간·장소·사건 맥락으로 전환되어 이전 장면의 상태를 기본적으로 상속하지 않는다.

단, 반복 등장 인물의 Canonical 외형처럼 Library에서 오는 기본 정의는 별개다.

`reset` 이후에도 반드시 유지해야 하는 요소가 있다면 명시적으로 다시 참조한다.

## 5. Continuity 데이터 영역

연속성은 최소 다음 영역으로 나눈다.

### 5.1 World

장면의 물리적 세계 상태.

예:

- location
- terrain
- architecture
- environment
- weather
- time of day
- 계절 또는 장기 시간 흐름

### 5.2 Character

등장 인물의 장면 간 상태.

예:

- character reference
- age stage
- 외형 상태
- costume
- accessories
- 상처 / 먼지 / 젖음 등 physical condition
- 들고 있는 물건
- 등장 여부

인물의 기본 외형 자체는 Library가 Source of Truth이며 Continuity에는 **해당 장면에서 이어져야 하는 상태**만 기록한다.

### 5.3 Object

장면에서 중요한 사물의 상태.

예:

- 위치
- 소유자
- 열림 / 닫힘
- 파손 / 완전
- 비어 있음 / 채워짐
- 사건 진행에 따른 상태 변화

### 5.4 Spatial

공간 방향과 상대 위치.

예:

- 인물이 화면의 어느 쪽에 있는가
- 인물 A와 B의 상대 위치
- 이동 방향
- 건물 / 산 / 강 등의 방향
- 화면 좌우가 바뀌면 안 되는 중요한 spatial axis

단순 화면 구도보다 **장면 연결성을 깨뜨릴 수 있는 공간 관계**를 우선 기록한다.

### 5.5 Event State

사건의 진행 상태.

예:

- 이전 Cut에서 시작된 행동이 어디까지 진행되었는지
- 누가 무엇을 들고 이동하고 있는지
- 어떤 물체가 이미 파괴되었는지
- 군중이 모이는 과정인지 흩어지는 과정인지

### 5.6 Visual

물리적 세계 상태와 별개로 컷 연결에 중요한 시각 규칙.

예:

- primary light source 방향
- 광원의 성격
- 핵심 색조
- camera axis
- 시선 방향
- 필요 시 framing continuity

모든 카메라 수치를 고정하려는 목적은 아니다.

다음 Cut에서 바뀌었을 때 관객에게 장면이 뒤집힌 것처럼 느껴지는 요소만 Canonical Continuity로 관리한다.

## 6. Continuity Segment

모든 Cut 전환마다 동일한 세계 상태를 반복 기록하지 않기 위해 Episode 안에 **Continuity Segment** 개념을 둔다.

Continuity Segment는 같은 기본 시간·장소·장면 맥락을 공유하는 **연속된 active Cut들의 범위**다.

이는 전역 식별자를 가진 독립 콘텐츠 엔터티가 아니다.

Episode 내부 continuity 문서에서 사용하는 로컬 구조다.

개념 예:

```yaml
episode_id: GEN-CREATION-01

segments:
  - key: S01
    start_cut_id: GEN-CREATION-01-C01
    end_cut_id: GEN-CREATION-01-C03

    baseline:
      world:
        environment: primordial-waters
      visual:
        primary_light_direction: right

transitions:
  - from: GEN-CREATION-01-C01
    to: GEN-CREATION-01-C02
    mode: continue

  - from: GEN-CREATION-01-C02
    to: GEN-CREATION-01-C03
    mode: continue
    change:
      - visual.light_intensity
```

`S01` 같은 segment key는 Episode 내부에서만 사용하며 Content Model의 영구 ID로 취급하지 않는다.

Segment에 Cut 목록이나 Cut 순서를 별도로 복제하지 않는다.

`start_cut_id`와 `end_cut_id` 사이에 포함되는 Cut은 **Storyboard의 현재 active Cut 순서**를 기준으로 계산한다.

따라서 Storyboard가 Cut order의 Source of Truth라는 STEP 0-1 원칙을 유지한다. Segment 내부에 새 Cut이 삽입되면 별도 목록 동기화 없이 해당 범위에 자연스럽게 포함된다.

### Transition의 저장 위치

Cut 간 transition은 특정 Segment 안에 소유시키지 않고 **Episode continuity의 단일 transition 목록**에서 관리한다.

이렇게 하면:

- 같은 Segment 내부 전환
- 서로 다른 Segment 사이의 전환
- partial reset
- full reset

을 모두 같은 규칙으로 표현할 수 있다.

모든 인접 active Cut 쌍에는 transition이 정확히 하나만 존재해야 한다.

## 7. Baseline과 Transition Delta

Continuity 데이터는 **전체 상태 복사**보다 baseline + 변화량(delta)을 우선한다.

### Baseline

해당 Continuity Segment에서 기본적으로 유지되는 중요한 상태를 정의한다.

예:

```yaml
baseline:
  world:
    location_ref: ...
    weather: clear
  visual:
    primary_light_direction: right
```

### Transition Delta

Cut 사이에서 실제로 달라지는 부분만 기록한다.

개념 예:

```yaml
from: C03
to: C04
mode: continue

retain:
  - world.location
  - visual.primary_light_direction

change:
  - path: character.CHR-XXX.position
    from: left
    to: center

reason: 인물이 장면 중앙으로 이동하는 사건 진행
```

단, baseline에 이미 유지가 명확한 항목을 모든 transition의 `retain`에 반복 작성할 필요는 없다.

`retain`은 특히 중요해서 명시적으로 강조할 필요가 있는 continuity constraint에 사용한다.

## 8. 값이 없는 것의 의미

Continuity 문서에 특정 항목이 기록되어 있지 않다고 해서 “이전 Cut과 반드시 같아야 한다”는 뜻은 아니다.

기본 원칙:

> **기록 없음 = continuity constraint 없음**

단, 현재 Segment의 baseline에 정의된 값은 segment 내에서 기본 continuity constraint로 적용된다.

이 원칙은 불필요하게 모든 시각 요소를 고정하여 이미지 생성 자유도를 잃는 것을 막기 위한 것이다.

## 9. Reset 규칙

다음 상황은 continuity reset 또는 partial reset 후보가 된다.

- 장소 이동
- 의미 있는 시간 점프
- 새로운 사건 시작
- 꿈 / 환상 / 회상 진입 또는 종료
- 날씨나 계절이 크게 변화
- 인물의 age stage 변화
- 의도적인 시각 언어 전환
- Episode 분할 지점에서 실제 사건 맥락도 함께 바뀜

하지만 위 조건이 발생했다고 자동으로 full reset하지 않는다.

예:

- 장소가 바뀌어도 같은 인물의 의상과 소지품은 유지될 수 있음
- 시간이 지나도 같은 건물 구조는 유지될 수 있음
- Episode가 바뀌어도 동일 사건 직후라면 대부분의 continuity를 이어갈 수 있음

따라서 reset은 **축별로 판단**한다.

## 10. Cross-Episode Boundary

연속된 두 Episode 사이에서 실제 장면 continuity가 중요하면 동일한 transition 개념을 사용한다.

개념 예:

```yaml
episode_boundary:
  from_cut: GEN-CREATION-01-C07
  to_cut: GEN-CREATION-02-C01
  mode: continue

  retain:
    - world.environment
    - visual.primary_light_direction
```

Cross-Episode Continuity는 Canonical Episode Sequence에 있다고 자동 생성하지 않는다.

명시적 연결이 있을 때만 관리한다.

동일한 경계 transition을 두 Episode에 중복 저장하지 않는다.

개념적으로 **도착하는 Episode가 자신의 incoming boundary를 소유**하도록 한다.

즉 이전 Episode의 마지막 Cut을 참조하되, 실제 transition 정의는 다음 Episode 쪽 continuity 데이터에서 한 번만 관리한다. 최종 물리 파일 배치는 STEP 0 전체 구조를 확정할 때 결정한다.

## 11. Continuity와 Revision

Continuity는 이미지 생성에 영향을 주는 Canonical 정의의 일부다.

따라서 이미 승인된 Cut에 대해 다음과 같은 continuity constraint가 의미 있게 바뀌면 영향을 받는 Cut revision을 다시 검토한다.

예:

- 광원 방향 변경
- 인물 위치 방향 변경
- 의상 유지 규칙 변경
- 사건 진행 상태 변경
- 중요한 object 상태 변경

원칙:

1. Continuity 자체에 별도 전역 revision 체계를 먼저 만들지 않는다.
2. Git 변경 이력은 문서 변경 기록을 보존한다.
3. 생성 결과를 달라지게 할 정도의 Continuity 변경은 영향을 받는 Cut의 revision 증가 대상으로 본다.
4. Episode 구조나 active Cut 구성이 바뀌면 STEP 0-2의 Episode revision 규칙도 적용한다.

## 12. Continuity와 Definition Approval

STEP 0-2에서 Cut / Episode 정의 승인과 이미지 제작 완료를 분리했다.

Continuity는 **정의 승인 측**에 포함된다.

### Cut 정의 승인 추가 조건

첫 active Cut을 제외하고, 이전 장면과 continuity가 필요한 Cut은 다음 중 하나가 해결되어야 한다.

- 유효한 `continue` transition
- 유효한 `partial_reset` transition
- 의도적인 `reset`

즉 장면 사이 관계를 미결정 상태로 둔 채 Cut 정의를 최종 승인하지 않는다.

### Episode 정의 승인 추가 조건

Episode의 모든 인접 active Cut 쌍에 대해 continuity mode가 결정되어야 한다.

Cross-Episode Continuity가 필요한 경우 Episode 경계 transition도 정의되어야 한다.

## 13. Continuity와 Generated Asset 검토

이미지 생성 후에는 Canonical Continuity 정의와 결과를 비교한다.

예:

```text
Canonical:
primary light source = right

Generated Asset:
primary light source = left

판정:
continuity mismatch
```

이 경우 Canonical Continuity 데이터를 생성 이미지에 맞춰 자동으로 수정하지 않는다.

기본 처리:

- 생성 결과를 수정 / 재생성하거나
- Canonical 정의 자체가 잘못되었다고 판단되는 경우에만 정식 revision 절차를 거쳐 Continuity/Cut 정의를 변경한다.

즉 **AI가 우연히 만든 결과가 Canonical 세계관을 역으로 결정하지 않는다.**

## 14. Continuity 불변 조건 후보

1. Continuity는 Cut 자체가 아니라 Cut 사이의 유지·변화 관계를 정의한다.
2. Library의 Canonical Asset 정의를 Continuity에 복제하지 않는다.
3. Cut의 전체 Scene Specification을 Continuity에 복제하지 않는다.
4. 같은 장면 맥락이 이어지는 구간은 Continuity Segment로 묶을 수 있다.
5. Segment baseline은 공통 continuity constraint를 정의한다.
6. Segment는 Cut 순서를 복제하지 않고 Storyboard의 active Cut 순서를 참조한다.
7. 모든 인접 active Cut 쌍의 transition은 Episode continuity의 단일 목록에서 정확히 한 번 정의한다.
8. transition은 baseline에서 달라지는 변화와 중요한 retain 조건을 기록한다.
9. `continue`라도 Segment baseline 또는 명시적 retain에 없는 값까지 자동 상속하지 않는다.
10. 기록되지 않은 값은 기본적으로 continuity constraint가 아니다.
11. 모든 인접 active Cut 쌍은 `continue / partial_reset / reset` 중 하나로 분류된다.
12. Episode 경계는 continuity를 자동으로 끊거나 자동으로 이어주지 않는다.
13. Cross-Episode Continuity가 필요하면 명시적으로 정의하며 동일 경계는 한 번만 저장한다.
14. 의미 있는 Continuity 변경은 영향을 받는 Cut revision 재검토 대상이다.
15. Generated Asset은 Continuity의 Source of Truth가 아니다.
16. 생성 결과와 Canonical Continuity가 충돌하면 기본적으로 생성 결과를 수정한다.
17. Continuity 검토는 Cut/Episode Definition Approval의 일부다.

## 15. STEP 0-3에서 의도적으로 미확정하는 항목

다음은 이후 STEP에서 정한다.

- Character / Location / Object의 실제 Library ID와 필드 구조
- Continuity에서 Library 자산을 참조하는 최종 문법
- Provider에 continuity reference image를 전달하는 방식
- reference strength / image weight 같은 provider 설정
- Generated Asset의 continuity 평가 점수 체계
- Markdown / YAML 최종 물리 저장 포맷
- Continuity Segment의 최종 파일 배치
- 웹사이트 표시용 transition 효과

## 16. STEP 0-3 검토 포인트

사용자 검토가 필요한 핵심 항목:

- Continuity를 Cut 사이 관계 데이터로 두는 원칙
- World / Character / Object / Spatial / Event State / Visual 영역 구분
- Episode 내부 Continuity Segment 사용과 Storyboard order 비중복 원칙
- 모든 인접 Cut transition을 Episode-level 단일 목록에서 관리하는 원칙
- baseline + transition delta 방식
- `continue / partial_reset / reset` 3단계 transition mode
- 기록 없음은 constraint 없음으로 해석하는 원칙
- Episode 경계 continuity를 자동 추론하지 않고 incoming boundary를 한 곳에서만 관리하는 원칙
- 의미 있는 Continuity 변경 시 영향을 받는 Cut revision을 다시 검토하는 원칙
- Continuity를 Cut/Episode Definition Approval 조건에 포함하는 원칙
- Generated Asset보다 Canonical Continuity 정의를 우선하는 원칙

위 항목은 2026-10-01 사용자 승인으로 확정되었다.

**STEP 0-3 — Continuity Model: COMPLETED / CONFIRMED**

다음 작업은 **STEP 0-4 — Library Model**이다.


---

# STEP 0-4 — Library Model v1.0

> 상태: **CONFIRMED / 2026-10-01 사용자 승인**
>
> 선행 조건:
> - **STEP 0-1 — Content Model v1.0 CONFIRMED**
> - **STEP 0-2 — Episode / Cut Model v1.0 CONFIRMED**
> - **STEP 0-3 — Continuity Model v1.0 CONFIRMED**
>
> 목적: 여러 Episode와 Cut에서 반복 사용되는 Character, Location, Object, Costume, Environment, Visual Style을 Provider와 독립적인 Canonical Library Asset으로 정의하고, 안정적인 ID·근거 정보·참조 규칙을 설계한다.
>
> 실제 이미지 파일 저장, Provider external ID, OpenArt/Higgsfield profile, Generation Run 구조는 이후 STEP에서 정한다.

## 1. Library의 역할

Library는 프롬프트 문구 모음이나 외부 서비스 설정 복사본이 아니다.

Library는 반복해서 등장하거나 장기 일관성이 필요한 성경 세계 요소의 **Canonical Definition Layer**다.

~~~text
Scripture / Historical Research
          ↓
Canonical Library Asset
          ↓
Episode / Cut reference
          ↓
Continuity state
          ↓
Provider-specific rendering
~~~

Library Asset은 Provider가 바뀌거나 외부 이미지가 삭제되어도 의미와 정체성이 유지되어야 한다.

## 2. Library Asset 생성 기준

모든 장면 요소를 Library로 승격하지 않는다.

다음 중 하나 이상이면 Library 등록을 우선한다.

- 여러 Episode/Cut에서 반복 등장
- 한 번만 등장하더라도 정체성이 매우 중요
- 인물 외형, 장소 구조, 주요 사물처럼 일관성 오류가 크게 보임
- 역사·문화적 고증을 여러 장면에서 재사용할 가치가 있음
- Provider가 바뀌어도 동일 정의를 다시 재현해야 함

반대로 한 Cut에만 등장하는 일반 배경 소품처럼 재사용성과 Canonical identity가 낮은 요소는 Cut-local 정의로 둘 수 있다.

즉 **재사용성과 일관성 가치가 있는 요소만 Library Asset으로 만든다.**

## 3. Asset 종류와 ID

v1 Library는 다음 여섯 종류를 사용한다.

~~~text
CHR  Character
LOC  Location
OBJ  Object
CST  Costume
ENV  Environment
STY  Visual Style
~~~

기본 ID 형식:

~~~text
<PREFIX>-<STABLE_KEY>
~~~

예:

~~~text
CHR-ABRAHAM
CHR-MOSES
LOC-EDEN
LOC-SINAI
OBJ-ARK-COVENANT
OBJ-TABERNACLE
CST-ANCIENT-HEBREW-MALE
CST-EGYPTIAN-ROYAL
ENV-ARID-HIGHLAND
ENV-NILE-FLOODPLAIN
STY-BIBLICAL-HISTORICAL-REALISM
~~~

규칙:

1. Prefix가 자산 종류를 결정한다.
2. ID는 저장소 전체에서 유일하다.
3. Episode/Cut 번호나 Provider 이름을 ID에 넣지 않는다.
4. 표시 이름이 바뀌어도 ID는 바꾸지 않는다.
5. 사용 이력이 있는 ID는 다른 자산에 재사용하지 않는다.
6. 자산의 정체성이 달라지면 새 ID를 발급한다.
7. 같은 실체를 여러 자산 종류로 중복 등록하지 않는다.

## 4. 공통 Library Asset Model

모든 Library Asset은 최소 다음 개념을 가진다.

~~~yaml
asset_id: CHR-MOSES
asset_type: character
name: 모세
aliases: []

canonical_summary: >
  이 자산이 무엇인지와 제작에서 어떤 정체성으로 다루는지 요약

basis:
  scripture: []
  historical: []
  reconstruction: []

uncertainties: []

definition_status: draft
revision: 1
~~~

공통 책임:

- asset_id: 영구 Canonical ID
- asset_type: 자산 종류
- name / aliases: 표시명과 검색용 별칭
- canonical_summary: 정체성의 핵심 요약
- basis: 근거 계층
- uncertainties: 미확정 역사·시각 쟁점
- definition_status: 설계·검토·승인 상태
- revision: 승인된 정의 변경 이력

### 4.1 Local Profile / Variant 원칙

하나의 Canonical Asset이 생애·시대·형태에 따라 달라진다고 해서 무조건 새 전역 Asset ID를 만들지 않는다.

정체성은 같고 표현 단계만 달라지는 경우 해당 Asset 내부의 local profile 또는 variant를 우선한다.

~~~text
CHR-MOSES
  ├─ profile: MIDIAN
  └─ profile: EXODUS

LOC-JERUSALEM
  ├─ profile: FIRST-TEMPLE
  └─ profile: SECOND-TEMPLE
~~~

원칙:

1. 전역 Asset ID는 identity를 나타낸다.
2. local profile/variant는 같은 identity의 생애·시대·형태 변화를 나타낸다.
3. local key는 해당 Asset 내부에서만 유일하면 된다.
4. 독립적으로 여러 자산에서 재사용되어야 하는 정의라면 별도 Library Asset 승격을 검토한다.
5. 상처, 먼지, 현재 위치, 파손처럼 사건 중 일시 상태는 profile이 아니라 Cut/Continuity가 관리한다.

## 5. 근거 계층

성경 본문 사실, 역사적 연구, 시각적 재구성을 같은 사실처럼 섞지 않는다.

### Scripture Basis

성경 본문에서 직접 확인되는 정보.

### Historical Basis

고고학, 고대 근동사, 복식사, 지리 자료 등 본문 외 역사적 근거.

### Visual Reconstruction

본문과 역사 자료만으로 하나의 외형을 확정할 수 없을 때 프로젝트가 일관성을 위해 선택한 시각적 해석.

예:

- 기록되지 않은 헤어스타일
- 정확한 색이 알려지지 않은 의복 색
- 자료가 제한적인 건물의 세부 장식
- 여러 가능한 역사적 형태 중 프로젝트가 채택한 하나

Visual Reconstruction을 Scripture Fact처럼 표현하지 않는다.

합리적인 대안이 여러 개이면 uncertainties에 남긴다.

## 6. Character Model

Character는 한 인물의 **지속적인 identity**를 정의한다.

예:

~~~text
CHR-MOSES
CHR-ABRAHAM
CHR-DAVID
~~~

핵심 데이터 후보:

- 성경상 identity와 역할
- 기본 외형 중 일관성이 필요한 특징
- appearance profile
- Scripture / Historical / Reconstruction basis
- uncertainties

### Appearance Profile

한 인물의 외형은 생애 전체에서 고정되지 않으므로 나이·시기마다 새 Character ID를 만들지 않는다.

~~~yaml
asset_id: CHR-MOSES

appearance_profiles:
  - key: MIDIAN
    label: 미디안 시기
    notes: ...

  - key: EXODUS
    label: 출애굽 시기
    notes: ...
~~~

원칙:

1. CHR-MOSES가 인물 identity다.
2. MIDIAN / EXODUS는 Character 내부 local profile key다.
3. profile은 별도의 전역 Character ID가 아니다.
4. Cut은 Character ID와 필요 시 appearance profile을 함께 참조한다.
5. 상처, 젖음, 먼지, 위치 같은 일시 상태는 Cut/Continuity가 관리한다.

### Character와 Costume

Character 안에 의복 정의 전체를 복사하지 않는다.

Character profile은 적합한 Costume을 참조할 수 있지만 Costume 자체의 원본 정의는 Costume Library가 소유한다.

## 7. Location Model

Location은 **정체성을 가진 장소**다.

예:

~~~text
LOC-EDEN
LOC-SINAI
LOC-JERUSALEM
LOC-EGYPT-NILE
~~~

핵심 데이터 후보:

- 지리적 정체성
- 본문에서의 역할
- 알려진 지형
- 주요 랜드마크 / 건축 특징
- 공간적 관계
- 필요 시 시대별 local profile
- Environment reference
- Scripture / Historical / Reconstruction basis

현재 날씨, 특정 시각의 빛, 사건 중 임시 배치는 Cut/Continuity가 관리한다.

## 8. Environment Model

Environment는 고유 장소 identity가 아니라 재사용 가능한 **지형·생태·환경 특성**이다.

예:

~~~text
ENV-ARID-HIGHLAND
ENV-NILE-FLOODPLAIN
ENV-MEDITERRANEAN-HILLS
ENV-PRIMORDIAL-WATERS
~~~

핵심 데이터 후보:

- terrain character
- vegetation pattern
- climate tendency
- water / soil / rock character
- atmospheric tendencies
- Historical / Reconstruction basis

구분:

~~~text
Location = 어디인가
Environment = 그 공간이 어떤 환경적 성격을 가지는가
~~~

Environment가 재사용되지 않는다면 불필요하게 별도 Asset으로 만들지 않는다.

## 9. Object Model

Object는 반복적으로 등장하거나 identity가 중요한 물리적 사물이다.

예:

~~~text
OBJ-ARK-COVENANT
OBJ-TABERNACLE
OBJ-OIL-LAMP
~~~

두 종류를 허용한다.

- unique: 언약궤처럼 특정 실체 하나의 identity가 중요
- type: 등잔처럼 동일 역사적 형태 기준을 여러 장면에서 재사용

핵심 데이터 후보:

- kind
- 재료
- 형태
- 치수 / 비례
- 구조
- 장식
- 기능
- Scripture / Historical / Reconstruction basis

현재 위치, 소유자, 파손, 열림/닫힘 등 사건 중 상태는 Cut/Continuity가 관리한다.

Object와 Location이 겹쳐 보이면 지리적 장소 identity가 핵심인지, 물질적 실체 identity가 핵심인지에 따라 하나만 Canonical 소유자로 선택한다.

## 10. Costume Model

Costume은 특정 인물 한 명에 종속되지 않는 **재사용 가능한 의복 또는 ensemble 정의**다.

예:

~~~text
CST-ANCIENT-HEBREW-MALE
CST-ANCIENT-HEBREW-FEMALE
CST-EGYPTIAN-ROYAL
CST-PRIEST-HIGH
~~~

핵심 데이터 후보:

- 시대 / 문화권
- 사회적 역할
- 적용 범위
- garment components
- materials
- construction
- accessories
- 색상 근거 또는 reconstruction
- 금지해야 할 시대착오 요소
- Scripture / Historical / Reconstruction basis

실제 Cut에서 누구에게 어떤 Costume을 배정하는지는 Cut이, 장면 사이 유지 여부는 Continuity가 책임진다.

## 11. Visual Style Model

Visual Style은 역사적 사실이 아니라 **프로젝트가 장면을 어떤 시각 언어로 렌더링할지 정의하는 Provider-independent 제작 자산**이다.

예:

~~~text
STY-BIBLICAL-HISTORICAL-REALISM
~~~

핵심 데이터 후보:

- realism level
- illustration / cinematic direction
- texture character
- color philosophy
- lighting philosophy
- composition tendencies
- 인물 표현 원칙
- historical constraints
- 피해야 할 style drift

원칙:

1. 역사적 고증 데이터와 Visual Style을 분리한다.
2. Provider 모델명이나 preset 이름을 Canonical Style에 넣지 않는다.
3. 실제 Provider style/profile mapping은 STEP 0-6이 맡는다.
4. 기본 Style은 Episode 수준에서 참조할 수 있다.
5. Cut별 차이가 필요할 때만 명시적 override를 허용한다.
6. v1에서는 복잡한 style 합성보다 하나의 base style + 필요 시 override를 우선한다.

## 12. Asset Reference 원칙

Episode / Cut / Continuity는 Library 정의를 복사하지 않고 ID로 참조한다.

개념 예:

~~~yaml
library_refs:
  characters:
    - asset_id: CHR-MOSES
      appearance_profile: EXODUS
      costume_ref: CST-ANCIENT-HEBREW-MALE

  location:
    asset_id: LOC-SINAI

  environments:
    - asset_id: ENV-ARID-HIGHLAND

  objects:
    - asset_id: OBJ-STAFF

  style:
    asset_id: STY-BIBLICAL-HISTORICAL-REALISM
~~~

최종 YAML 위치와 문법은 STEP 0 완료 시 결정한다.

핵심 원칙:

- 참조자는 Library ID를 저장한다.
- Library의 긴 외형 설명을 Cut에 복사하지 않는다.
- Cut-specific 상태만 Cut/Continuity에 추가한다.
- 특정 Character가 실제 장면에서 어떤 Costume을 입는지 같은 **자산 간 배정 관계는 Cut의 장면 정의가 소유**한다.
- Library는 Character와 Costume을 서로 영구 결합해 모든 장면에 강제하지 않는다.
- Library 수정 시 참조 관계를 통해 영향 범위를 검토할 수 있어야 한다.

## 13. Definition Status와 Revision

Library도 Episode/Cut처럼 **정의 승인과 이미지 제작 상태를 분리**한다.

기본 상태:

~~~text
draft
↓
in_review
↓
approved
~~~

보조 상태:

~~~text
revision_requested
cancelled
superseded
~~~

approved는 “현재 revision이 Canonical 기준으로 참조 가능”하다는 뜻이다.

superseded는 같은 Asset의 새 revision에 사용하지 않고, 다른 Asset ID가 기존 identity를 대체하는 경우에만 사용한다.

Reference image나 Provider 등록 완료 여부와는 별개다.

승인 후 실제 시각 결과에 영향을 줄 수 있는 정의 변경은 revision 증가 대상으로 본다.

### Library Definition 승인 조건

Library Asset의 definition_status를 approved로 만들기 위한 최소 조건:

1. 공통 필수 정의와 해당 asset type의 핵심 데이터가 존재한다.
2. Scripture / Historical / Visual Reconstruction이 가능한 범위에서 구분되어 있다.
3. 중요한 불확실성이 있다면 uncertainties에 기록되어 있다.
4. Provider external ID나 특정 서비스 preset이 Canonical 정의를 대신하지 않는다.
5. 동일 실체의 중복 Library Asset이 없는지 검토되었다.

Episode/Cut 작성 중에는 draft Library Asset을 임시 참조할 수 있지만, **Cut definition을 최종 approved로 만들 때 해당 Cut이 의존하는 Canonical Library Asset과 사용 profile은 approved 상태여야 한다.**

이 규칙은 승인된 장면이 아직 확정되지 않은 인물 외형이나 장소 정의에 기대는 것을 막기 위한 것이다.

## 14. Library 변경 영향 검토

공유 Library Asset의 revision이 올라가면 참조 Cut의 영향 범위를 검토한다.

~~~text
Library revision 변경
        ↓
참조 Cut 검색
        ↓
변경 필드 / 사용 profile 비교
        ↓
영향 있는 Cut만 revision 재검토
~~~

원칙:

1. Library revision 증가가 모든 참조 Cut revision의 자동 증가를 의미하지 않는다.
2. 실제 변경 필드와 해당 Cut이 사용하는 profile을 기준으로 영향 여부를 판단한다.
3. 생성 결과가 달라질 정도로 영향받는 승인 Cut만 STEP 0-2 revision 규칙을 적용한다.
4. Continuity가 영향받으면 STEP 0-3 규칙도 함께 적용한다.

## 15. Reference Image와 Provider Binding 분리

~~~text
Canonical Library Asset
        ↓
Reference Asset(s)
        ↓
Provider Binding
        ↓
OpenArt / Higgsfield / future provider
~~~

- Reference Asset 저장·버전 정책 → STEP 0-7
- Provider external ID / profile → STEP 0-6
- Provider용 prompt 최적화 → STEP 0-6
- Library에는 Provider와 무관한 Canonical 정의만 유지

외부 서비스 슬롯이 삭제되어도 Library Asset ID와 정의는 유지되어야 한다.

## 16. 자산 경계 판단 규칙

### Character vs Costume

- 인물 identity / 기본 외형 → Character
- 재사용 가능한 의복 구조 → Costume
- 특정 Cut에서 착용 상태 → Cut
- 장면 사이 의복 유지 → Continuity

### Location vs Environment

- 고유 장소 identity → Location
- 재사용 가능한 지형·생태·환경 특성 → Environment
- 현재 날씨 / 시간대 → Cut 또는 Continuity

### Location vs Object

- 지리적 장소 identity → Location
- 물질적 실체 / 제작된 구조물 identity → Object
- 같은 실체를 양쪽에 중복 등록하지 않음

### Environment vs Visual Style

- 세계 안에 실제 존재하는 환경 특성 → Environment
- 관객에게 어떻게 렌더링할지 → Visual Style

### Library vs Cut

- 여러 장면에서 재사용할 Canonical 정체성 → Library
- 이번 장면에만 필요한 상태 / 행동 → Cut 또는 Continuity

## 17. Library 불변 조건 후보

1. Library는 반복 사용되는 성경 세계 요소의 Canonical Definition Layer다.
2. Library Asset은 Provider와 독립적이다.
3. 모든 Library Asset은 안정적인 type prefix ID를 가진다.
4. 동일 실체를 여러 asset type으로 중복 등록하지 않는다.
5. Scripture Basis / Historical Basis / Visual Reconstruction을 구분한다.
6. 불확실한 고증을 확정 사실처럼 숨기지 않는다.
7. 전역 Asset identity와 같은 Asset 내부의 local profile/variant를 분리한다.
8. Character identity와 생애 단계별 appearance profile을 분리한다.
9. Character와 Costume 원본 정의를 중복하지 않는다.
10. Location과 Environment 책임을 분리한다.
11. Object의 사건 중 상태는 Cut/Continuity가 관리한다.
12. Visual Style은 역사적 사실과 분리된 Provider-independent 제작 자산이다.
13. Episode/Cut/Continuity는 Library 정의를 복사하지 않고 ID로 참조한다.
14. Character와 Costume 같은 자산 간 실제 장면 배정은 Cut이 소유한다.
15. Library Definition Approval과 Reference Image / Provider 등록 상태를 분리한다.
16. 승인된 Library 변경은 참조 Cut에 대한 영향 검토를 수행한다.
17. Library revision 증가가 모든 참조 Cut revision 자동 증가를 의미하지 않는다.
18. 재사용성과 일관성 가치가 없는 일회성 요소를 과도하게 Library Asset으로 만들지 않는다.
19. 승인된 Cut은 자신이 의존하는 Canonical Library Asset과 사용 profile이 approved 상태여야 한다.

## 18. STEP 0-4에서 의도적으로 미확정하는 항목

다음은 이후 STEP에서 정한다.

- Reference Image 실제 저장 위치
- 이미지 파일명 / 버전 규칙
- OpenArt / Higgsfield external ID mapping
- Provider별 Character / Style profile
- Provider reference strength / weight
- Generation Run에서 사용한 Library revision snapshot 기록 방식
- 역사 연구 출처의 최종 citation 문법
- Markdown / YAML 최종 물리 파일 구조
- Library 하위 파일 분할 방식
- Reference Asset 승인 정책

## 19. STEP 0-4 검토 포인트

다음 항목은 사용자 승인으로 확정되었다:

- Library Asset 생성 기준
- CHR / LOC / OBJ / CST / ENV / STY ID 체계
- 공통 Library Asset Model
- Scripture / Historical / Visual Reconstruction 근거 분리
- 전역 Asset ID와 local profile/variant 분리 원칙
- Character appearance profile 방식
- Character와 Costume 분리 및 실제 장면 배정은 Cut이 소유하는 원칙
- Location과 Environment 분리
- Object unique / type 구분
- Visual Style을 Provider-independent Library Asset으로 관리하는 원칙
- Episode/Cut/Continuity에서 Library ID를 참조하는 원칙
- Library Definition Status / revision 및 승인 조건
- 승인된 Cut이 참조하는 Library Asset도 approved여야 한다는 원칙
- Library 변경 시 참조 Cut 영향 검토
- Reference Image / Provider Binding을 Canonical Library에서 분리
- 일회성 요소를 과도하게 Library Asset으로 만들지 않는 원칙

위 항목은 2026-10-01 사용자 승인으로 확정되었다.

**STEP 0-4 — Library Model: COMPLETED / CONFIRMED**

다음 작업은 **STEP 0-5 — Generation Run Model**이다.


---

# STEP 0-5 — Generation Run Model v1.0

> 상태: **CONFIRMED / 2026-10-02 사용자 승인**
>
> 선행 조건:
> - **STEP 0-1 — Content Model v1.0 CONFIRMED**
> - **STEP 0-2 — Episode / Cut Model v1.0 CONFIRMED**
> - **STEP 0-3 — Continuity Model v1.0 CONFIRMED**
> - **STEP 0-4 — Library Model v1.0 CONFIRMED**
>
> 목적: 하나의 Cut을 실제 이미지로 렌더링하기 위해 Provider에 제출한 각 생성 시도를 재현·비교·평가할 수 있도록 Generation Run과 그 결과를 정의한다.
>
> Provider별 Character Binding과 generation profile의 최종 구조는 STEP 0-6에서, 실제 이미지 Asset 저장·승인 정책은 STEP 0-7에서 확정한다.

## 1. Generation Run의 역할

Generation Run은 Canonical Scene 자체가 아니다.

Run은 **특정 시점의 Canonical 정의와 생성 입력을 사용하여 외부 Provider에 실제로 제출한 한 번의 렌더링 요청 기록**이다.

~~~text
Approved or Working Cut Definition
          ↓
Generation Input Snapshot
          ↓
Generation Run
          ↓
0..N Generated Results
          ↓
Result Evaluation
          ↓
Asset handling
~~~

Cut / Continuity / Library가 “무엇을 만들어야 하는가”의 Source of Truth라면, Generation Run은 “그 정의를 어떤 입력으로 실제 생성해 보았는가”를 기록한다.

## 2. Run의 경계

v1에서는 **Provider에 실제로 한 번 제출한 하나의 생성 요청**을 Run 하나로 본다.

예:

- 요청 한 번에서 이미지 1장 반환 → Run 1개 / Result 1개
- 요청 한 번에서 이미지 4장 반환 → Run 1개 / Result 4개
- 같은 Prompt로 다시 Generate → 새 Run
- Prompt 수정 후 다시 생성 → 새 Run
- Reference image만 바꿔 다시 생성 → 새 Run
- Provider 오류로 이미지가 없음 → 실패한 Run 1개

단순 다운로드, 파일명 변경, 리사이즈, 사이트용 파생 이미지 생성은 새로운 Generation Run이 아니다.

## 3. Run ID

기본 형식:

~~~text
<CUT_ID>-R<NNN>
~~~

예:

~~~text
GEN-CREATION-01-C03-R001
GEN-CREATION-01-C03-R002
GEN-CREATION-01-C03-R003
~~~

규칙:

1. Run은 v1에서 정확히 하나의 Cut을 대상으로 한다.
2. Run ID는 Cut ID 아래에서 영구적으로 증가한다.
3. 숫자는 최소 3자리 zero-padding을 사용하되 999를 넘는 것을 금지하지 않는다.
4. 실패하거나 폐기된 Run 번호도 재사용하지 않는다.
5. Run 번호는 품질 순위나 승인 순서를 뜻하지 않는다.
6. Provider가 바뀌어도 같은 Cut의 Run sequence를 이어간다.

Library Reference용 별도 이미지 생성 Run이 필요해질 경우 v1 Cut Run 규칙을 억지로 재사용하지 않고 STEP 0-7과 함께 target 모델 확장을 검토한다.

## 4. Result ID

한 Run에서 여러 결과가 나올 수 있으므로 Result도 안정적인 local ID를 가진다.

기본 형식:

~~~text
<RUN_ID>-O<NN>
~~~

예:

~~~text
GEN-CREATION-01-C03-R007-O01
GEN-CREATION-01-C03-R007-O02
GEN-CREATION-01-C03-R007-O03
GEN-CREATION-01-C03-R007-O04
~~~

Result ID는 Generated Asset ID가 아니다.

Result는 Provider가 반환한 개별 생성 결과의 기록이고, 최종 Asset 식별 체계는 STEP 0-7에서 정한다.

## 5. Generation Run 공통 데이터

개념 예:

~~~yaml
run_id: GEN-CREATION-01-C03-R007

target:
  cut_id: GEN-CREATION-01-C03
  cut_revision: 1

source_snapshot:
  repository_commit: abc123...
  library:
    - asset_id: CHR-MOSES
      revision: 2
      profile: EXODUS
    - asset_id: CST-ANCIENT-HEBREW-MALE
      revision: 1

operation: generate

provider:
  key: openart
  model: ...
  model_version: unknown

prompt:
  observable_input_text: ...
  provider_internal_prompt: unavailable
  negative_prompt: ...

references: []

settings:
  aspect_ratio: ...
  resolution: ...
  seed: null
  provider_specific: {}

submitted_at: ...

execution_status: completed

usage:
  credits: null
  cost: null
  currency: null
~~~

필드 책임:

- run_id: 영구 Run ID
- target: 어떤 Cut revision을 렌더링하려 했는지
- source_snapshot: 당시 Canonical 기준을 복원하기 위한 저장소 / Library 상태
- operation: generate / edit / variation 등 실제 요청 유형
- provider: 실제 사용한 서비스와 모델
- prompt: 프로젝트 쪽에서 실제 확인 가능한 생성 instruction / Prompt snapshot
- references: 실제 전달된 Reference Asset
- settings: 생성 시 사용한 주요 설정
- submitted_at: 실행 시점. ISO 8601 timestamp 사용을 원칙으로 함
- execution_status: 요청 자체의 실행 결과
- usage: 알 수 있는 경우 비용·크레딧 기록

## 6. Git Source Snapshot

Run은 Cut revision만 기록해서는 충분하지 않다.

STEP 0-2에서는 **첫 승인 전의 수정이 같은 revision 안에서 진행될 수 있기 때문**이다.

~~~text
C03 revision 1 — draft A
↓ 수정
C03 revision 1 — draft B
↓ 수정
C03 revision 1 — approved C
~~~

세 상태 모두 revision 숫자는 1일 수 있다.

따라서 의미 있는 Generation Run은 당시 사용한 Canonical 정의를 재현할 수 있도록 **repository commit SHA를 source snapshot으로 기록**한다.

원칙:

1. Run에 사용되는 Cut / Storyboard / Continuity / Library의 의미 있는 정의는 가능한 한 생성 전에 GitHub Source of Truth에 반영한다.
2. Run은 해당 입력 기준의 Git commit SHA를 기록한다.
3. commit SHA는 Cut revision을 대체하지 않는다. 둘 다 기록한다.
4. Library Asset은 실제 사용한 asset revision과 local profile도 함께 추적할 수 있어야 한다.
5. Provider 실행 후 Canonical 정의가 바뀌어도 과거 Run의 source snapshot은 변경하지 않는다.

## 7. Prompt Snapshot

현재의 prompt 문서만 참조하면 과거 Run 재현성이 깨질 수 있다.

따라서 각 Run은 **프로젝트 쪽에서 실제 확인 가능한 생성 instruction / Prompt를 immutable snapshot으로 추적**해야 한다.

최소 기록 대상:

- 프로젝트에서 Provider 인터페이스에 전달한 observable generation instruction / prompt
- negative prompt가 있다면 해당 내용
- 실제로 사용된 별도 style instruction이 확인 가능하면 그 참조
- Prompt template/version을 사용했다면 식별 정보
- provider 내부에서 자동 확장된 prompt가 공개되는 경우 해당 값

원칙:

1. Run 생성 입력은 완료 후 수정하지 않는다.
2. Prompt를 고쳐 다시 생성하면 새 Run이다.
3. Provider별 Prompt 구조와 profile은 STEP 0-6에서 정한다.
4. ChatGPT처럼 내부에서 최종 생성 Prompt가 자동 구성되지만 그 값이 공개되지 않는 경우, 사용자가 제공한 생성 instruction과 확인 가능한 참조 context만 기록하고 내부 Prompt는 unavailable로 둔다.
5. Provider가 내부적으로 추가하는 비공개 Prompt는 추측해서 기록하지 않는다.
6. 실제로 확인 가능한 입력만 기록한다.

## 8. Reference Snapshot

Run에는 “어떤 Reference를 사용했는가”뿐 아니라 **어떤 역할로 전달했는가**를 기록한다.

~~~yaml
references:
  - asset_ref: ...
    role: character
    library_asset_id: CHR-MOSES
    library_revision: 2
    profile: EXODUS

  - asset_ref: ...
    role: continuity
    source_cut_id: GEN-CREATION-01-C02
~~~

role 후보:

- character
- location
- object
- costume
- environment
- style
- continuity
- composition
- edit_source
- other

Reference strength / weight 같은 Provider별 수치는 settings 또는 STEP 0-6 Provider Integration에서 다룬다.

Reference 이미지의 실제 Asset ID와 파일 저장 규칙은 STEP 0-7에서 확정한다.

## 9. Provider / Model 정보

Run은 Provider 종속 설정을 Canonical Scene과 분리해서 보존한다.

기본 기록 대상:

- provider key
- model name / identifier
- model version 또는 checkpoint가 노출되는 경우 그 값
- Provider request/job ID가 제공되는 경우 외부 식별자
- 주요 생성 설정
- 알려진 seed

원칙:

1. 알 수 없는 model/version을 추측하지 않는다.
2. UI가 내부 모델을 노출하지 않으면 unknown으로 남길 수 있다.
3. Provider external job ID는 Run ID를 대체하지 않는다.
4. Provider 설정은 확장 가능한 provider_specific 영역을 허용한다.
5. Provider별 공통 profile/binding 정의는 STEP 0-6이 Source of Truth다.

## 10. Execution Status

Run 자체의 상태는 **요청 실행 여부**만 표현한다.

v1 기본 상태:

~~~text
completed
partial
failed
cancelled
~~~

- completed: 요청이 정상 종료되고 결과가 반환됨
- partial: 일부 결과만 반환되었거나 Provider가 부분적으로 완료
- failed: 실행 오류로 유효한 결과를 얻지 못함
- cancelled: 실행 도중 명시적으로 취소됨

Run에 approved / rejected를 사용하지 않는다.

Run의 성공 여부와 결과 이미지의 품질 판단은 다른 개념이다.

## 11. Generated Result Model

각 Result는 Run으로부터 생성된 개별 출력이다.

~~~yaml
result_id: GEN-CREATION-01-C03-R007-O01
provider_output_id: ...

reviews: []
~~~

Provider output ID가 없다면 생략할 수 있다.

Result 자체는 생성 당시의 출력 identity를 보존하며, 품질 판정은 별도의 Review 기록으로 누적한다.

## 12. Result Review Record

이미지 판정을 Result 본문에 한 개의 mutable status로 덮어쓰지 않는다.

하나의 Result는 시간이 지나면서 서로 다른 Canonical 기준으로 재검토될 수 있기 때문이다.

예:

~~~text
처음 생성 당시
C03 draft 기준 → accepted

이후 Cut 정의 수정
C03 approved 기준으로 재검토 → rejected
~~~

이때 최초 판단을 삭제하지 않고 새 Review를 추가한다.

개념 예:

~~~yaml
reviews:
  - sequence: 1
    reviewed_at: ...
    reviewed_against:
      cut_revision: 1
      repository_commit: abc123...
    decision: accepted
    evaluation:
      scripture_fidelity: pass
      historical_accuracy: pass
      continuity: concern
      library_consistency: pass
      visual_quality: pass
      technical_integrity: pass
    issues: []
    notes: ...

  - sequence: 2
    reviewed_at: ...
    reviewed_against:
      cut_revision: 2
      repository_commit: def456...
    decision: rejected
    issues:
      - CONTINUITY_MISMATCH
~~~

Review decision:

~~~text
accepted
rejected
~~~

Review가 아직 하나도 없으면 Result는 unreviewed로 간주한다.

가장 최신 Canonical 기준에 대한 Review가 현재 사용 가능성을 판단하는 기준이 된다.

accepted는 Cut Definition approved와 다른 개념이며, 곧바로 “대표 최종 Asset”을 의미하지 않는다.

한 Cut에서 현재 기준에 accepted인 Result가 여러 개 존재할 수 있으며 대표 Asset 선택은 STEP 0-7에서 정한다.

## 13. 평가 축

각 Result는 최소 다음 축을 기준으로 검토할 수 있다.

- scripture_fidelity
- historical_accuracy
- continuity
- library_consistency
- visual_quality
- technical_integrity

v1에서는 가짜 정밀도를 만들 수 있는 숫자 점수를 필수화하지 않는다.

기본 판정 값:

~~~text
pass
concern
fail
not_applicable
~~~

평가하지 않은 축은 Review 자체에서 생략할 수 있다. pending을 별도 영구 판정값으로 저장하지 않는다.

의미:

- scripture_fidelity: 본문과 장면 정의에 충실한가
- historical_accuracy: 역사·지리·복식·사물 고증과 충돌하지 않는가
- continuity: Canonical Continuity를 지키는가
- library_consistency: Character / Location / Object / Costume / Style 정의와 일치하는가
- visual_quality: 프로젝트가 요구하는 시각적 완성도에 도달하는가
- technical_integrity: 비정상적인 신체·구조·해상도·렌더링 결함이 없는가

## 14. Rejection Reason

Review decision이 rejected이면 최소한 왜 폐기했는지 기록한다.

구조화된 issue category와 자유 메모를 함께 사용할 수 있다.

초기 issue category 후보:

~~~text
SCRIPTURE_MISMATCH
REQUIRED_ELEMENT_MISSING
FORBIDDEN_ELEMENT_PRESENT
PREMATURE_ELEMENT
HISTORICAL_MISMATCH
CONTINUITY_MISMATCH
CHARACTER_MISMATCH
LOCATION_MISMATCH
OBJECT_MISMATCH
COSTUME_MISMATCH
STYLE_DRIFT
COMPOSITION_ISSUE
TECHNICAL_ARTIFACT
TEXT_ARTIFACT
OTHER
~~~

실제 category registry는 제작 사례가 쌓이면서 필요한 항목만 유지·확장한다.

한 Result에 여러 issue가 있을 수 있다.

## 15. accepted / rejected 기준

Review decision은 단순히 “예쁜가”로 결정하지 않는다.

기본 원칙:

1. Scripture mismatch가 핵심 장면 의미를 훼손하면 rejected.
2. required element가 빠지면 rejected.
3. forbidden element가 들어가면 rejected.
4. 중요한 Continuity constraint를 위반하면 rejected.
5. 승인된 Library 정의와 중요한 충돌이 있으면 rejected.
6. 기술적 결함이 장면 사용을 방해하면 rejected.
7. 사소한 문제만 있고 후처리로 해결 가능한 경우의 Asset 처리 기준은 STEP 0-7에서 다룬다.
8. visual quality가 높더라도 본문·Canonical 정의와 충돌하면 품질만으로 accepted 처리하지 않는다.

## 16. Run과 Cut Definition Status의 관계

Generation Run을 만들기 위해 Cut이 반드시 approved일 필요는 없다.

탐색적 제작을 통해 장면 정의를 빠르게 검증할 필요가 있기 때문이다.

따라서 draft 또는 in_review Cut에도 Run을 만들 수 있다.

단:

1. Run은 당시 source commit과 입력 snapshot을 정확히 기록한다.
2. draft 기준 Result는 이후 Cut 정의 변경의 영향을 받을 수 있다.
3. 최종 대표 Asset을 결정할 때는 현재 approved Cut revision과 Canonical 정의에 대한 적합성을 다시 확인해야 한다.
4. 오래된 draft Run이 시각적으로 좋다는 이유만으로 현재 Canonical 정의를 역으로 바꾸지 않는다.
5. 탐색 Run을 허용하되 Canonical 정의와 생성 결과의 책임은 분리한다.

## 17. Retry / New Run 규칙

다음은 새 Run이다.

- 동일 Prompt로 재생성
- Prompt 변경 후 재생성
- seed 변경
- Reference 변경
- model 변경
- Provider 변경
- 주요 generation setting 변경
- Provider retry가 실제 새 request/job을 생성

Run operation은 최소 generate / edit / variation을 구분할 수 있어야 한다.

의미 있는 image edit 요청이 Provider에 새 작업으로 제출되면 새 Run이며, edit_source Reference를 기록한다.

다음은 새 Run이 아니다.

- 결과 파일 다운로드
- metadata 정리
- Result Review record 추가
- Review note 추가
- 동일 결과 파일명 변경
- 파생 리사이즈 / 압축본 생성

## 18. Run Record의 불변 영역

Generation Run은 완료 후 “실제로 무엇을 제출했는가”라는 역사 기록이므로 실행 입력을 덮어쓰지 않는다.

불변 영역:

- run_id
- target snapshot
- source commit
- Provider / model snapshot
- observable generation instruction / prompt snapshot
- references
- generation settings
- execution result metadata
- original Result identity

실행 이후 추가 가능한 영역:

- 새로운 Result Review record
- 이후 Asset 연결

기존 Review도 당시 판단의 이력으로 보존하는 것을 원칙으로 하며, 새로운 Canonical 기준으로 판단이 바뀌면 기존 Review를 덮어쓰지 않고 새 Review를 추가한다.

과거 Run 입력이 잘못 기록된 단순 오타 수정과 실제 실행 입력 변경을 구분해야 한다.

실제 입력이 달랐다면 과거 기록을 원하는 값으로 바꾸지 않는다.

## 19. 비용 / Credit 기록

비용 데이터는 운영 효율 분석에 유용하므로 **알 수 있을 때 기록**한다.

모든 Provider가 동일한 비용 정보를 제공하지 않으므로 필수값으로 강제하지 않는다.

기록 후보:

- credits consumed
- monetary cost
- currency
- billing unit
- estimated 여부
- usage note

원칙:

1. Provider가 정확한 비용을 제공하면 실제값을 기록한다.
2. 계산값이면 estimated임을 표시한다.
3. 알 수 없으면 null / unknown을 허용한다.
4. 비용이 없다는 뜻과 알 수 없다는 뜻을 구분한다.

## 20. 실패한 Run 보존

실패한 Run도 다음과 같은 학습 가치가 있다.

- 특정 model이 자주 만드는 오류
- Character consistency 실패
- Continuity 위반
- Prompt 패턴 문제
- Provider 안정성
- 비용 대비 성공률

따라서 실제 Provider에 제출된 의미 있는 Run은 결과가 좋지 않더라도 기본적으로 기록을 유지한다.

단순 UI 오동작이나 요청 자체가 Provider에 전달되지 않은 이벤트까지 Run으로 만들 필요는 없다.

## 21. Run 데이터와 Asset 데이터 분리

~~~text
Generation Run
  └─ Generated Result
          ↓
      Asset 등록 여부 결정
          ↓
      Asset
~~~

Run Result는 생성 과정의 원본 기록이다.

Asset은 프로젝트가 보존·사용하기 위해 등록한 이미지 자산이다.

모든 Result를 장기 Asset으로 보존해야 하는지는 STEP 0-7에서 정한다.

따라서 Run metadata와 실제 이미지 파일 보존 정책을 동일시하지 않는다.

## 22. STEP 0-5 불변 조건 후보

1. 실제 Provider 생성 요청 한 번을 Generation Run 한 개로 본다.
2. 한 Run은 v1에서 정확히 하나의 Cut을 대상으로 한다.
3. 한 Run은 0개 이상의 Generated Result를 가질 수 있다.
4. 한 요청에서 여러 이미지가 반환되면 하나의 Run 아래 여러 Result로 관리한다.
5. Run ID와 Result ID는 Provider external ID와 독립적이다.
6. Run은 target Cut revision과 Git source commit을 함께 기록한다.
7. 프로젝트에서 확인 가능한 실제 generation instruction / Prompt와 Reference input을 Run별 snapshot으로 보존한다.
8. 확인할 수 없는 Provider/model 내부 정보는 추측하지 않는다.
9. Run execution status와 Result Review decision을 분리한다.
10. Run에는 approved/rejected 상태를 사용하지 않는다.
11. Result Review는 reviewed_against Cut revision + Git commit을 기록하는 누적 이력이다.
12. Result accepted는 최종 대표 Asset 승인을 의미하지 않는다.
13. 결과 평가는 Scripture / Historical / Continuity / Library / Visual / Technical 축을 구분한다.
14. rejected Review는 가능한 한 실패 이유를 남긴다.
15. draft/in_review Cut의 탐색 Run을 허용한다.
16. 완료된 Run의 실행 입력 snapshot은 불변 역사 기록으로 취급한다.
17. Canonical 기준 변경 후 판정이 달라지면 기존 Review를 덮어쓰지 않고 새 Review를 추가한다.
18. 의미 있는 실패 Run도 기본적으로 보존한다.
19. 비용·Credit은 알 수 있을 때 기록하되 필수값으로 강제하지 않는다.
20. Run Result와 장기 보존 Asset을 구분한다.

## 23. STEP 0-5에서 의도적으로 미확정하는 항목

다음은 이후 STEP에서 정한다.

- OpenArt / Higgsfield / ChatGPT별 generation profile 스키마
- Provider Character ID / Style profile binding
- Reference strength / weight의 표준화 여부
- Provider별 Prompt adapter 구조
- Provider별 retry API 세부 처리
- Asset ID / 파일명 / 저장 위치
- accepted Result에서 대표 Asset을 선정하는 방법
- rejected 이미지 파일을 실제로 얼마나 오래 보존할지
- thumbnail / web derivative 정책
- Library Reference 이미지 생성용 Run target 확장 여부

## 24. STEP 0-5 검토 포인트

다음 항목은 사용자 승인으로 확정되었다:

- Provider 요청 1회 = Run 1개 원칙
- Run ID 형식: CUT_ID + RNNN
- Result ID 형식: RUN_ID + ONN
- Run 하나에서 여러 Result 관리
- Cut revision + Git commit SHA source snapshot
- Library revision/profile snapshot
- 프로젝트에서 확인 가능한 실제 Prompt / generation instruction snapshot 보존
- Reference 역할과 실제 사용 입력 기록, edit_source 구분
- generate / edit / variation operation 구분
- execution_status와 Result Review decision 분리
- Result Review의 reviewed_against snapshot 및 누적 이력
- accepted Result와 최종 Asset 승인 분리
- 비숫자 중심 평가 축과 issue category
- draft/in_review Cut의 탐색 Run 허용
- Run 실행 입력의 immutable history 원칙
- 의미 있는 실패 Run 보존
- 비용/Credit optional 기록
- Run Result와 Asset 분리

위 항목은 2026-10-02 사용자 승인으로 확정되었다.

**STEP 0-5 — Generation Run Model: COMPLETED / CONFIRMED**

다음 작업은 **STEP 0-6 — Provider Integration Model**이다.


---

# STEP 0-6 — Provider Integration Model v1.0

> 상태: **CONFIRMED / 2026-10-02 사용자 승인**
>
> 선행 조건:
> - **STEP 0-1 — Content Model v1.0 CONFIRMED**
> - **STEP 0-2 — Episode / Cut Model v1.0 CONFIRMED**
> - **STEP 0-3 — Continuity Model v1.0 CONFIRMED**
> - **STEP 0-4 — Library Model v1.0 CONFIRMED**
> - **STEP 0-5 — Generation Run Model v1.0 CONFIRMED**
>
> 목적: ChatGPT, OpenArt, Higgsfield 및 향후 추가될 외부 렌더링 서비스를 Canonical 제작 데이터와 분리된 Adapter Layer로 연결하고, Provider 교체·기능 변화·외부 리소스 재등록이 발생해도 프로젝트의 원본 정의가 흔들리지 않도록 한다.
>
> 실제 이미지 Asset의 저장 위치와 보존 정책은 STEP 0-7에서 확정한다.

## 1. Provider Integration의 역할

Provider Integration은 Canonical Scene이나 Library의 일부가 아니다.

Provider Integration은 **프로젝트의 Provider-independent Canonical 정의를 특정 외부 서비스가 이해할 수 있는 입력으로 변환하고 연결하는 Adapter Layer**다.

~~~text
Scripture
  ↓
Episode / Cut / Continuity / Library
  ↓
Canonical Production Definition
  ↓
Provider Integration Layer
  ├─ Provider Registry / Capability
  ├─ Provider Binding
  ├─ Generation Profile
  ├─ Prompt Adapter
  └─ Reference Delivery Mapping
  ↓
Generation Run
  ↓
External Provider
~~~

핵심 원칙:

> Provider가 프로젝트의 Canonical 구조를 결정하지 않는다. Canonical 구조가 먼저이고 Provider Integration이 그 구조를 외부 서비스에 맞게 번역한다.

## 2. Provider Key

프로젝트 내부에서는 Provider를 안정적인 내부 key로 식별한다.

초기 예:

~~~text
chatgpt
openart
higgsfield
~~~

Provider key 규칙:

1. 내부 key는 외부 서비스의 일시적인 상품명이나 특정 모델명을 사용하지 않는다.
2. 모델명이 바뀌어도 Provider key는 유지할 수 있다.
3. Provider 자체가 완전히 다른 서비스로 대체되면 새 key를 사용한다.
4. 과거 Run에서 사용된 Provider key는 삭제하거나 다른 Provider 의미로 재사용하지 않는다.
5. 새 Provider 추가가 Canonical Content / Library 구조 변경을 요구해서는 안 된다.

## 3. Provider Registry

각 Provider Integration은 최소한 다음 운영 메타데이터를 가질 수 있다.

~~~yaml
provider_key: openart
display_name: OpenArt

integration_status: active

execution_modes:
  - manual_ui
  - api

capabilities:
  generate: unknown
  edit: unknown
  variation: unknown
  image_reference: unknown
  provider_character_resource: unknown
  provider_style_resource: unknown

verified_at: null
notes: null
~~~

이 예시는 실제 Provider 기능을 확정하는 문서가 아니라 **기능을 기록하는 구조**를 보여준다.

Provider 기능은 외부 서비스 변경에 따라 달라질 수 있으므로 다음 원칙을 따른다.

1. 확인하지 않은 기능을 true로 추측하지 않는다.
2. unknown 값을 허용한다.
3. 실제 작업에 중요한 capability는 Provider 설정을 갱신할 때 확인한다.
4. Capability 정보가 바뀌어도 Canonical Scene / Library revision을 자동 변경하지 않는다.
5. 과거 Run은 당시 실제 사용한 입력 snapshot으로 보존한다.

integration_status 후보:

~~~text
experimental
active
disabled
retired
~~~

- experimental: 시험 사용 중
- active: 신규 Run에 정상 사용 가능
- disabled: 현재 신규 Run에 사용하지 않음
- retired: 역사 기록만 유지하는 Provider

## 4. Provider Integration의 세 계층

Provider별 재사용 정보를 하나의 거대한 설정 파일에 섞지 않고 세 책임으로 구분한다.

### 4.1 Provider Binding

Canonical Library Asset과 Provider 내부 리소스의 연결.

예:

~~~text
CHR-MOSES / EXODUS
        ↕
OpenArt external character resource
~~~

### 4.2 Generation Profile

Provider에서 반복해서 사용하는 모델·출력 비율·기본 설정·adapter 등 **재사용 가능한 생성 설정 묶음**.

### 4.3 Prompt Adapter

Canonical Scene / Continuity / Library 정보를 Provider가 실제로 받을 instruction / prompt 형태로 변환하는 규칙.

Generation Run은 이 세 계층을 사용하더라도 **실제 실행에 사용된 최종 resolved input을 Run snapshot으로 다시 보존**한다.

따라서 Integration 설정이 나중에 바뀌어도 과거 Run 기록은 변하지 않는다.

## 5. Provider Binding

Provider Binding은 Canonical Library Asset 또는 그 local profile과 Provider 내부 리소스의 관계를 기록한다.

개념 예:

~~~yaml
provider_key: openart
binding_key: moses-exodus-primary
binding_revision: 1

canonical_ref:
  asset_id: CHR-MOSES
  profile: EXODUS

verified_against:
  asset_revision: 2

provider_resource:
  resource_type: character
  external_id: ...
  external_name: ...

scope_alias: main

status: active
verified_at: ...
notes: null
~~~

중요:

- external_id는 Canonical Character ID가 아니다.
- binding_key도 Library Asset ID가 아니다.
- Canonical identity는 항상 CHR-MOSES 같은 Library ID가 소유한다.
- binding_key는 provider_key + scope_alias 안에서 안정적으로 유지한다.
- verified_against는 이 Binding이 마지막으로 검증된 Canonical Asset revision을 나타낸다.

## 6. Binding Scope

일부 Provider 리소스는 특정 계정·워크스페이스·프로젝트 안에서만 유효할 수 있다.

따라서 Binding은 필요 시 비밀 정보가 아닌 내부 scope alias를 기록한다.

예:

~~~text
scope_alias: main
scope_alias: experiment-a
~~~

원칙:

1. API key, access token, session cookie, 비밀번호는 GitHub에 저장하지 않는다.
2. 실제 계정 credential 대신 비밀이 아닌 내부 alias를 사용한다.
3. 외부 resource ID가 공개되어도 credential로 기능하는 값이라면 저장소에 기록하지 않는다.
4. 인증 정보는 실행 환경의 secret 관리 영역에서 별도로 제공한다.
5. 과거 Run 재현에 필요한 비밀값 자체를 Run에 snapshot하지 않는다.

## 7. Binding Status와 재등록

Provider 내부 슬롯이나 캐릭터 리소스는 삭제·변경·재등록될 수 있다.

Binding 상태 후보:

~~~text
active
needs_review
unavailable
retired
~~~

- active: 현재 사용 가능하다고 확인됨
- needs_review: Canonical 정의 또는 Provider 측 변경으로 재검토 필요
- unavailable: 외부 리소스가 현재 사용 불가능
- retired: 더 이상 신규 Run에서 사용하지 않는 과거 Binding

원칙:

1. 외부 resource가 삭제되어도 Canonical Library Asset을 삭제하지 않는다.
2. 동일 Character를 Provider에 재등록하면 기존 Library ID를 유지한다.
3. 같은 Canonical ref와 같은 목적을 위한 외부 리소스를 재등록한 경우 binding_key는 유지하고 binding_revision을 증가시킨다.
4. 같은 Canonical ref에 대해 병행 사용하려는 별도 목적/별도 외부 리소스라면 새 binding_key를 만든다.
5. 과거 Run은 당시 실제 binding revision과 external resource snapshot을 계속 보존한다.
6. Binding 변경 때문에 Cut revision을 자동 증가시키지 않는다.

## 8. Canonical Library Revision과 Binding 검증

Provider Binding은 자신이 어떤 Canonical Library revision/profile을 기준으로 생성·검증되었는지 기록한다.

예:

~~~text
CHR-MOSES rev2 / EXODUS
↓
OpenArt Binding rev1
~~~

이후 CHR-MOSES가 rev3으로 바뀌었다고 해서 Binding을 자동 폐기하지 않는다.

다만 verified_against가 rev2인 상태에서 Canonical Asset이 rev3이 되면 **검토가 필요한 후보**로 간주한다.

대신 변경 영향에 따라:

- 외형에 영향 없음 → 기존 Binding을 다시 검증하고 verified_against 갱신 가능
- 외형에 영향 있음 → needs_review
- 동일 목적 리소스의 Provider 재등록 필요 → 같은 binding_key의 binding_revision 증가
- 별도 목적의 병행 리소스 필요 → 새 binding_key

즉 Library revision과 Provider Binding revision은 서로 다른 축이다.

## 9. Generation Profile

Generation Profile은 특정 Provider에서 반복 사용할 **운영 기본값 묶음**이다.

개념 예:

~~~yaml
provider_key: openart
profile_key: biblical-wide-standard
profile_revision: 1

operation: generate

model:
  identifier: ...
  version: unknown

defaults:
  aspect_ratio: ...
  resolution: ...
  provider_specific: {}

prompt_adapter:
  key: cut-illustration
  revision: 1

reference_policy:
  character: prefer_binding
  continuity: prefer_reference_asset
  style: adapter_or_reference
~~~

Generation Profile은 Canonical Visual Style과 다르다.

~~~text
STY-BIBLICAL-HISTORICAL-REALISM
= 어떤 시각 언어를 원하는가

Provider Generation Profile
= 그 Provider에서 그 목표를 어떻게 실행할 것인가
~~~

## 10. Generation Profile 원칙

1. Provider별 profile은 Provider Integration 영역이 소유한다.
2. Canonical Library나 Cut에 Provider profile 내용을 복사하지 않는다.
3. profile에는 Provider-specific model/settings를 둘 수 있다.
4. profile 변경이 Canonical Cut revision 증가를 의미하지 않는다.
5. 생성 결과에 영향을 주는 profile 변경은 profile_revision 증가 대상으로 본다.
6. Run은 사용한 profile key/revision과 실제 resolved settings를 snapshot한다.
7. profile은 편의를 위한 기본값이며 Run의 실제 입력 기록을 대체하지 않는다.
8. 하나의 Canonical Style이 여러 Provider profile에 매핑될 수 있다.
9. 한 Provider에 여러 목적의 profile을 둘 수 있지만 실제 필요가 생기기 전 과도하게 만들지 않는다.
10. provider_key + profile_key는 같은 목적의 profile identity를 나타내며, 설정 변경은 profile_revision으로 추적한다.
11. 완전히 다른 목적의 profile은 새 profile_key를 사용한다.
12. 이미 Run에서 사용된 profile_key/revision의 과거 의미를 다른 설정으로 재사용하지 않는다.

## 11. Prompt Adapter

Prompt Adapter는 Canonical 데이터를 Provider용 observable instruction으로 변환한다.

입력 후보:

- Cut scene intent / Canonical Scene
- Storyboard scripture anchor / beat
- Continuity constraint
- Library reference / profile
- Visual Style
- Provider capability
- Generation Profile

출력 후보:

- positive instruction / prompt
- negative prompt
- Provider-specific instruction block
- 사용할 Reference 역할
- 필요한 warning / unsupported capability 정보

중요:

> Prompt Adapter의 출력은 Canonical Scene이 아니라 파생 데이터다.

Provider에 맞춰 Prompt 표현을 수정하더라도 Scripture / Cut / Library 원본 정의를 바꾸지 않는다.

## 12. Prompt Adapter Revision

Adapter 규칙이 생성 결과에 의미 있게 영향을 주도록 바뀌면 adapter revision을 증가시킨다.

예:

~~~text
cut-illustration rev1
→ 장면 설명 + style instruction

cut-illustration rev2
→ continuity constraint를 별도 강조
~~~

Run은 사용한 adapter key/revision을 기록하고, STEP 0-5 규칙대로 실제 observable input snapshot도 보존한다.

따라서 adapter revision만으로 과거 Run을 재현하려 하지 않는다.

adapter key 규칙:

1. provider_key + adapter_key는 같은 변환 목적의 adapter identity를 나타낸다.
2. 같은 목적의 의미 있는 변환 규칙 변경은 adapter_revision을 증가시킨다.
3. 완전히 다른 변환 목적은 새 adapter_key를 사용한다.
4. 이미 Run에서 사용된 adapter key/revision의 과거 의미를 다른 규칙으로 재사용하지 않는다.

## 13. Input Resolution 우선순위

Provider 요청을 만들 때 재사용 기본값과 Run별 override가 충돌하지 않도록 우선순위를 명확히 한다.

기본 개념:

~~~text
Canonical Definition
        +
Provider Binding / Reference Mapping
        +
Generation Profile defaults
        +
Run-specific explicit overrides
        ↓
Resolved Provider Input
        ↓
Generation Run snapshot
~~~

설정값의 일반 우선순위:

~~~text
Run explicit override
> Generation Profile default
> 확인 가능한 Provider default
~~~

원칙:

1. Canonical Scene / Library constraint는 “설정 기본값”이 아니므로 위 우선순위로 덮어쓰지 않는다.
2. Run override가 Canonical constraint와 충돌하면 실행 전에 mismatch로 표시한다.
3. Provider default가 명확하지 않으면 추측하지 않고 unknown / omitted로 취급한다.
4. 최종적으로 resolve된 observable prompt, reference, binding, model, settings를 STEP 0-5 Run snapshot에 기록한다.
5. Integration 정의만 보고 나중에 실제 resolved input을 역산하지 않는다.

## 14. Reference Delivery Mapping

Canonical Reference 역할과 Provider가 실제로 받는 입력 방식은 동일하지 않을 수 있다.

예:

~~~text
Canonical role: character
→ Provider character binding

Canonical role: continuity
→ image reference

Canonical role: style
→ provider style resource
또는 prompt adapter text

Canonical role: object
→ image reference
또는 prompt description
~~~

Provider Integration은 이 변환 규칙을 명시한다.

실제 Run에는 STEP 0-5에서 확정한 대로 **무엇을 어떤 역할과 설정으로 전달했는지** snapshot한다.

## 15. Reference Delivery Method

Provider별 reference 전달 method 후보:

~~~text
provider_binding
reference_asset
prompt_text
provider_profile
unsupported
~~~

Provider가 어떤 Canonical role을 직접 지원하지 않는다고 해서 Canonical 정의를 삭제하거나 단순화하지 않는다.

Binding이 needs_review / unavailable 상태이거나 capability가 unknown / unsupported인 경우 Adapter는 해당 입력을 **조용히 생략하지 않고 warning 또는 unresolved requirement로 노출**해야 한다.

처리 순서:

1. 다른 지원 방식으로 전달 가능한지 검토
2. Prompt Adapter로 명시 가능한지 검토
3. Provider capability로 충족 불가능한 핵심 constraint라면 다른 Provider 사용을 검토
4. Canonical 정의를 Provider 한계에 맞춰 조용히 훼손하지 않음

## 16. Capability Mismatch

Provider가 필요한 기능을 지원하지 않을 수 있다.

예:

~~~text
Cut requires:
  strong character consistency
  continuity reference

Provider capability:
  해당 기능을 현재 사용할 수 없음
~~~

이 경우 기본 원칙:

- Canonical Scene을 낮추지 않는다.
- unsupported 사항을 명시한다.
- 다른 전달 방법 또는 Provider를 검토한다.
- 탐색 Run이라면 한계를 명시한 상태로 실행할 수 있다.
- 최종 production candidate에는 현재 Canonical 요구사항을 다시 적용한다.
- 핵심 requirement가 unresolved인 상태를 “정상 지원”으로 기록하지 않는다.

Provider의 기능 부족이 프로젝트의 성경·고증·Continuity 기준을 변경하는 근거가 되어서는 안 된다.

## 17. Provider Selection

Canonical Episode / Cut에는 특정 Provider를 영구 기본값으로 박아두지 않는다.

Provider 선택은 운영 결정이다.

선택 시 고려 가능 항목:

- 필요한 capability
- Character consistency
- Reference 처리
- 편집 능력
- 결과 품질
- 비용 / credit
- 처리 시간
- 운영 안정성

하지만 Provider 선택 자체를 Canonical content identity로 만들지 않는다.

같은 Cut을 ChatGPT에서 탐색하고 OpenArt에서 재생성해도 Cut ID와 Canonical 정의는 동일하다.

## 18. Execution Mode

같은 Provider라도 실제 실행 방식이 다를 수 있다.

예:

~~~text
manual_ui
api
chat_native
other
~~~

Generation Profile은 지원 가능한 execution mode를 기록할 수 있고, Generation Run은 실제 사용한 execution mode를 snapshot할 수 있다.

UI를 통해 수동 생성한 기록과 API 자동 생성 기록을 동일한 Run Model 아래 관리하되, 확인 가능한 metadata 범위가 다를 수 있음을 허용한다.

## 19. Provider-specific 정보의 위치

Provider-specific 데이터는 가능한 한 Integration Layer에 격리한다.

예:

~~~text
Canonical
CHR-MOSES
STY-BIBLICAL-HISTORICAL-REALISM
GEN-CREATION-01-C03
        ↓
Integration
OpenArt binding
OpenArt generation profile
OpenArt prompt adapter
        ↓
Run
실제 OpenArt input snapshot
~~~

다음 정보를 Canonical Library/Cut에 넣지 않는다.

- Provider character slot ID
- Provider model preset ID
- Provider style profile ID
- provider-only weight
- account/project slot name
- API endpoint 세부 정보
- Provider용 최적화 Prompt

## 20. Provider Response Normalization

Provider마다 반환하는 job 구조, output ID, 이미지 개수, metadata 형식이 다를 수 있다.

Integration Layer는 이를 STEP 0-5의 공통 Run / Result 의미로 정규화한다.

~~~text
Provider-specific response
        ↓
Integration normalization
        ↓
Generation Run execution metadata
        +
Generated Result(s)
~~~

원칙:

1. Provider job/request ID는 Run의 external metadata로 보존한다.
2. Provider output/image ID는 가능한 경우 Result의 external metadata로 보존한다.
3. 한 Provider 응답에서 여러 이미지가 반환되면 각 이미지를 별도 Result로 매핑한다.
4. Provider가 일부 결과만 반환하면 execution_status를 partial로 표현할 수 있다.
5. Provider 고유 metadata를 공통 필드에 억지로 끼워 맞추지 않고 provider_specific raw metadata 영역을 허용한다.
6. raw metadata를 보존하더라도 인증 토큰이나 민감한 요청 헤더는 저장하지 않는다.
7. 대용량 binary/image payload 자체를 raw metadata에 중복 저장하지 않는다. 실제 Asset 저장은 STEP 0-7 정책을 따른다.
8. Integration normalization이 Canonical Scene이나 Result 평가를 자동으로 변경하지 않는다.

## 21. Provider Integration 변경과 Canonical Revision

다음 변화는 일반적으로 Canonical revision을 요구하지 않는다.

- Provider 모델 교체
- Binding external ID 변경
- Generation Profile 변경
- Prompt Adapter 개선
- Reference weight 조정
- Provider API/UI 변화

단, Provider 작업 중 Canonical 정의 자체가 잘못되었음을 발견했다면 STEP 0-2~0-4의 정식 revision 절차를 따른다.

즉 **Integration 변경과 Canonical 변경을 구분한다.**

## 22. Provider 폐기 / 교체

Provider를 더 이상 사용하지 않아도 과거 기록은 유지한다.

~~~text
OpenArt integration retired
↓
과거 Binding / Profile / Run 기록 유지

새 Provider 추가
↓
동일 Canonical Library / Cut에 새 Integration 연결
~~~

원칙:

1. Provider 폐기는 Canonical Asset 폐기를 의미하지 않는다.
2. 과거 Run의 provider snapshot을 변경하지 않는다.
3. 새 Provider를 추가할 때 기존 Canonical ID를 재사용한다.
4. Provider migration을 이유로 Episode/Cut/Library ID를 다시 만들지 않는다.
5. 필요한 경우 새 Binding/Profile/Adapter만 추가한다.

## 23. Secrets / Credential 규칙

Provider Integration 문서와 Run 기록에는 다음을 저장하지 않는다.

- API key
- access token
- refresh token
- password
- session cookie
- secret webhook token
- 기타 인증 비밀값

저장 가능한 것은 비밀이 아닌 식별 정보와 내부 alias, 설정 메타데이터다.

Credential은 저장소 밖의 안전한 실행 환경에서 관리한다.

## 24. Provider Integration과 Generation Run의 관계

Integration은 **재사용 가능한 운영 정의**, Run은 **실제로 실행된 immutable snapshot**이다.

~~~text
Provider Registry
Provider Binding
Generation Profile
Prompt Adapter
        ↓ resolve
Generation Run Snapshot
        ↓
External Provider
~~~

따라서:

1. Integration 설정을 바꾸어도 과거 Run은 바뀌지 않는다.
2. Run에는 실제 사용한 Binding/Profile/Adapter revision을 기록할 수 있어야 한다.
3. Run에는 최종 observable prompt/reference/settings도 별도로 기록한다.
4. Integration 정의만 보고 과거 Run 입력을 추정하지 않는다.

## 25. 초기 Provider별 적용 원칙

### ChatGPT

ChatGPT를 사용할 때도 동일한 Provider Adapter 원칙을 적용한다.

내부적으로 확인할 수 없는 model detail, hidden prompt, seed 등을 추측하지 않는다.

사용자가 제공한 생성 instruction, 전달한 이미지 reference, 확인 가능한 실행 정보만 Run snapshot에 남긴다.

### OpenArt

OpenArt에 Canonical Character / Style과 대응되는 외부 리소스가 존재하는 경우 Provider Binding으로 연결한다.

외부 리소스 자체를 Canonical Character/Style로 취급하지 않는다.

### Higgsfield

Higgsfield 역시 Canonical Library/Cut을 직접 소유하지 않는다.

Provider에서 재사용 가능한 외부 리소스나 profile이 필요해질 경우 동일한 Binding/Profile 원칙으로 연결한다.

세 Provider의 구체적인 기능·필드명은 실제 Integration을 구현할 때 당시 지원 상태를 확인하여 작성한다.

## 26. 과도한 Provider 추상화 금지

Provider 독립성을 확보하되 모든 Provider 기능을 억지로 하나의 완벽한 공통 스키마로 만들지 않는다.

원칙:

1. Canonical 입력 의미와 공통 운영 필드만 표준화한다.
2. Provider 고유 기능은 provider_specific 영역에 둘 수 있다.
3. 실제로 존재하지 않는 기능을 미래 대비 목적으로 미리 추상화하지 않는다.
4. 공통화가 Canonical 의미 손실을 만들면 공통화하지 않는다.
5. 새 Provider가 들어올 때 필요한 최소 확장만 한다.

## 27. STEP 0-6 불변 조건 후보

1. Provider Integration은 Canonical 정의와 외부 서비스를 연결하는 Adapter Layer다.
2. Provider 내부 리소스는 Canonical Library identity가 아니다.
3. Provider key는 모델명과 분리된 안정적인 내부 식별자다.
4. Capability는 확인 가능한 범위에서 기록하며 unknown을 허용한다.
5. Provider Binding은 Canonical Library ref와 외부 resource를 연결한다.
6. Binding은 account/project credential과 분리되고 비밀값을 저장하지 않는다.
7. 외부 resource 재등록은 Canonical Library ID 변경을 요구하지 않는다.
8. Generation Profile은 Provider-specific 재사용 기본값이며 Canonical Style과 다르다.
9. Prompt Adapter 출력은 Canonical 데이터의 파생물이다.
10. Generation Profile / Prompt Adapter는 안정적인 key와 revision으로 추적한다.
11. Reference role과 Provider delivery method를 분리한다.
12. Input resolution은 Run override > Profile default > 확인 가능한 Provider default 순으로 하되 Canonical constraint를 덮어쓰지 않는다.
13. Provider가 기능을 지원하지 않아도 Canonical 정의를 자동으로 축소하지 않는다.
14. unsupported / unknown capability나 비활성 Binding을 조용히 누락하지 않고 unresolved requirement로 드러낸다.
15. Provider 선택은 운영 결정이며 Canonical Content identity가 아니다.
16. Integration 변경은 일반적으로 Cut/Library revision을 요구하지 않는다.
17. Provider 응답은 STEP 0-5의 공통 Run / Result 의미로 정규화하되 Provider 고유 metadata를 필요 시 보존한다.
18. Provider 폐기·교체 후에도 과거 Binding/Profile/Run 기록을 유지한다.
19. Run은 사용한 Integration revision과 실제 resolved input snapshot을 함께 보존한다.
20. 인증 비밀값을 GitHub Source of Truth에 저장하지 않는다.
21. Provider-specific 기능은 필요 시 격리하되 과도한 공통 추상화를 만들지 않는다.

## 28. STEP 0-6에서 의도적으로 미확정하는 항목

다음은 이후 구현 또는 STEP 0-7에서 정한다.

- 각 Provider의 실제 API endpoint / SDK 사용법
- 현재 지원 모델의 실제 목록
- 실제 Character Builder / Style resource 생성 절차
- external resource ID의 구체적인 값
- API credential 저장 솔루션
- 실제 Reference Asset 파일 위치
- Provider API 자동화 코드 구조
- retry/backoff 구현
- provider별 비용 계산 자동화
- 이미지 Asset ID / 저장 위치
- 웹사이트 전달용 Asset pipeline

## 29. STEP 0-6 검토 포인트

다음 항목은 사용자 승인으로 확정되었다:

- Provider Integration을 Adapter Layer로 두는 원칙
- Provider Registry / Capability 구조
- Provider Binding / Generation Profile / Prompt Adapter 3계층 분리
- Binding revision과 Canonical Library revision 분리
- scope alias와 credential 분리
- Generation Profile과 Canonical Visual Style 분리
- Generation Profile / Prompt Adapter key와 revision 수명 주기
- Prompt Adapter 출력은 파생 데이터라는 원칙
- Input resolution 우선순위와 Canonical constraint 비덮어쓰기 원칙
- Canonical Reference role과 Provider delivery method 분리
- Capability mismatch / inactive Binding을 조용히 누락하지 않는 원칙
- Capability mismatch 시 Canonical 정의를 낮추지 않는 원칙
- Provider 선택을 운영 결정으로 두는 원칙
- manual_ui / api / chat_native execution mode
- Provider 변경이 Canonical revision을 자동 유발하지 않는 원칙
- Provider 응답을 공통 Run/Result로 정규화하는 원칙
- Provider 폐기·교체 후 과거 기록 유지
- secrets / credential Git 저장 금지
- Run에 Integration revision + 실제 resolved input을 함께 기록
- 과도한 Provider 공통 추상화를 피하는 원칙

위 항목은 2026-10-02 사용자 승인으로 확정되었다.

**STEP 0-6 — Provider Integration Model: COMPLETED / CONFIRMED**

다음 작업은 **STEP 0-7 — Image / Asset Storage Policy**다.
