# Architecture Overview

> 상태: **CONFIRMED ARCHITECTURE INDEX**
>
> 기준: STEP 0-1~0-7의 사용자 승인 모델과 Repository Structure v1.0

이 문서는 Bible Illustration 프로젝트의 정식 Architecture 진입점이다.

## 1. Source of Truth 흐름

~~~text
Scripture
  ↓
Episode / Storyboard / Cut
  ↓
Episode Orchestration + Presentation Plan
  ↓
Library + Continuity
  ↓
Provider Integration
  ↓
Generation Run / Generated Result / Review
  ↓
Asset Promotion
  ↓
Representative Asset / Library Reference Asset
~~~

각 계층은 서로 다른 책임을 가진다.

- Scripture는 장면의 본문 근거다.
- Episode / Storyboard / Cut은 제작 구조와 장면 정의를 관리한다.
- Library는 반복 사용되는 Canonical identity와 visual definition을 관리한다.
- Continuity는 장면 사이에서 유지·변경·reset되는 상태를 관리한다.
- Provider Integration은 Canonical 정의를 외부 생성 서비스에 연결하는 Adapter Layer다.
- Generation Run은 실제 Provider 요청 한 번의 실행 입력과 결과 이력을 기록한다.
- Asset은 장기 보존 가치가 있어 승격된 실제 이미지 binary identity다.

## 2. 정식 Architecture 문서

- `CONTENT_MODEL.md` — Scripture / Episode / Cut / Storyboard와 ID 체계
- `EPISODE_CUT_MODEL.md` — Episode·Cut 필수 데이터, 상태, revision, approval
- `CONTINUITY_MODEL.md` — Cut 사이의 유지·변화·reset
- `LIBRARY_MODEL.md` — Character / Location / Object / Costume / Environment / Visual Style
- `GENERATION_RUN_MODEL.md` — 실제 생성 요청, Result, Review 이력
- `EPISODE_ORCHESTRATION_MODEL.md` — Multi-Cut / Episode-level 위임의 순차 실행 상태 머신
- `PROVIDER_INTEGRATION_MODEL.md` — Provider Registry / Binding / Generation Profile / Prompt Adapter
- `ASSET_STORAGE_POLICY.md` — Asset Promotion, Git LFS, 대표 이미지와 Reference 보존
- `REPOSITORY_STRUCTURE.md` — 위 모델을 실제 저장소 경로로 배치하는 규칙

## 3. 핵심 경계

### Canonical Definition vs Rendering

Canonical Scene / Library / Continuity가 먼저다.

ChatGPT, OpenArt, Higgsfield 등은 이 정의를 렌더링하는 외부 Provider이며 Canonical identity를 소유하지 않는다.

### Cut Definition vs Image Selection

`cut.yaml`은 장면 정의를 소유한다.

대표 이미지 선택은 별도 `asset-selection.yaml` 관계가 소유하므로 이미지 교체만으로 Cut revision을 증가시키지 않는다.

### Generated Result vs Asset

Provider가 반환한 모든 Result를 장기 보존하지 않는다.

장기 가치가 있는 Result만 Asset Promotion을 거쳐 Canonical Asset으로 등록한다.

### Storyboard vs Cut vs Continuity

Storyboard는 **무엇을 어떤 순서와 의미 단위로 보여줄지 계획**한다. 각 entry의 `transition_note`는 직전 active Cut에서 현재 Cut으로 들어오는 high-level intent를 기록한다.

- 표시 순서
- Cut ID
- Scripture Anchor
- 짧은 beat
- 필요 시 high-level transition intent

Cut은 각 장면의 상세 Canonical Scene Specification을 소유한다.

Continuity는 인접 장면 사이의 실제 `continue / partial_reset / reset` 및 retain / change / reset constraint를 소유한다.

Storyboard Production Review에서 coverage / granularity / relation / visual repetition / Episode rhythm을 검토할 수 있지만,
그 판단을 이유로 Cut 상세값이나 Continuity constraint를 Storyboard에 중복 저장하지 않는다.

### Library vs Continuity

Library Entity는 “무엇인가”를 정의한다. 실제 이미지 binary Asset과 identity를 분리한다.

Continuity는 “이전 장면에서 무엇이 유지되고 무엇이 변하는가”를 정의한다.

### Episode Orchestration vs Cut Production

Episode Orchestration은 사용자가 Multi-Cut 범위를 통째로 위임했을 때 **기존 Cut 생산 루프를 어떤 순서로 끝까지 호출할지**를 관리한다.

- 전체 범위를 Storyboard active Cut 순서로 실행
- one-Cut-at-a-time generation cycle
- Result Review 통과 후 다음 Cut으로 advance
- Presentation Plan에 따른 Key Scripture / explanatory / visual-only 처리
- blocker가 없으면 autonomous mode에서 사용자 추가 명령 없이 다음 Cut으로 진행

Orchestration은 Storyboard/Cut/Continuity 정의를 복제하지 않는다.

### Provider Integration vs Run

Integration은 재사용 가능한 Adapter 설정이다.

Run은 실제 실행 순간의 resolved input snapshot을 보존하는 역사 기록이다.

## 4. 현재 작업 위치

현재 진행 상태는 `../progress/CURRENT.md`를 확인한다.

작업을 시작할 때는 루트 `AGENTS.md` → `../progress/CURRENT.md` → 이 문서 → 필요한 세부 Architecture/Rules 순서로 읽는다.

## 5. Historical Working Record

`architecture_v0_draft.md`는 STEP 0의 설계 과정과 승인 이력을 담은 historical working record로 보존한다.

정식 Architecture의 현재 기준은 이 디렉터리의 문서들이다.
