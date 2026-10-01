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
- 컷 수가 8개라는 현재 기본값을 어떻게 취급할지
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

> **STEP 0-1 — Content Model 정의**

목표는 바로 파일이나 디렉터리를 생성하는 것이 아니라, 먼저 성경 본문 → Episode → Cut으로 이어지는 콘텐츠 모델을 충분히 검토하여 확정하는 것이다.

STEP 0 진행 중 중요한 설계 결정은 대화에만 남기지 말고 이 저장소에 기록한다.
