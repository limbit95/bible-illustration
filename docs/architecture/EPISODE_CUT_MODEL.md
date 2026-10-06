# Episode / Cut Model v1.1

> 상태: **CONFIRMED / 2026-10-06 pre-production hardening**
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
episode_id: BOOK-STORY-01
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
cut_id: BOOK-STORY-01-C01
episode_id: BOOK-STORY-01

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

다음 시각 정보는 필요할 수 있지만 v1에서는 모두를 고정 필드로 강제하지 않는다. Library Entity reference, Continuity constraint, `scene.summary / required_elements / forbidden_elements` 조합으로 필요한 만큼만 표현한다.

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

Cut 자체의 상세 Scene과 Continuity / Library Entity reference 경계는 `CONTINUITY_MODEL.md`와 `LIBRARY_MODEL.md`를 따른다.

`scene.summary / required_elements / forbidden_elements`는 Cut의 최소 핵심 Canonical Scene 데이터다.

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

### 4.1 Storyboard Production Planning

Storyboard는 단순 순서표가 아니라 Cut 정의 전에 전체 제작 흐름을 점검하는 planning surface로도 사용한다.

다만 이 역할은 **새 Canonical 필드를 추가하는 것**과 다르다.

현재 최소 필드만으로 다음을 검토한다.

- Episode / Work Unit의 Scripture coverage
- Cut boundary와 beat granularity
- 인접 Cut의 high-level Scene Relation
- 전체 시퀀스의 visual repetition 가능성
- Episode-level rhythm
- 필요 시 Key Scripture / explanatory presentation intent

이 검토 결과가 구체적인 장면 요구사항으로 이어지면 Cut이 소유하고,
인접 Cut 사이의 retain / change / reset 요구사항으로 이어지면 Continuity가 소유한다.

### 4.2 `transition_note`의 책임

`transition_note`는 **직전 active Storyboard Cut → 현재 entry의 Cut**으로 들어오는 서사적·연출적 전환 의도를 짧게 기록한다.

첫 active Cut의 `transition_note`는 기본적으로 `null`이다. Cross-Episode incoming boundary의 Canonical 상세는 `continuity.yaml`이 소유한다.

적절한 예:

- 같은 사건의 직접적인 다음 단계이므로 연결감을 우선한다.
- 새로운 사건이 시작되므로 visual reset을 허용한다.
- Story chronology는 이어지지만 새로운 scale의 장면으로 전환한다.

다음 상세 데이터는 `transition_note`에 Canonical constraint로 중복 저장하지 않는다.

- 정확한 continuity mode
- retain path
- change path와 값
- reset path
- Segment baseline

이 값들은 Continuity Model이 Source of Truth다.

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
6. Storyboard Production Preflight에서 coverage / granularity / relation / visual repetition / Episode rhythm의 blocking concern이 해결되었다.

7. 모든 인접 active Cut 쌍의 Continuity mode가 결정되어 있다.
8. Cross-Episode Continuity가 필요한 경우 incoming episode boundary가 정의되어 있다.

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

6. 첫 active Cut이 아닌 경우 직전 active Cut에서 들어오는 유효한 Continuity transition이 정의되어 있다.

## 7. 승인 후 수정과 Revision

Episode와 Cut의 ID는 identity이고 revision은 정의의 버전이다.

예:

```text
BOOK-STORY-01-C03
revision: 1
```

승인 후 의미 있는 Canonical Scene 변경이 필요하면 같은 Cut ID 아래 revision을 증가시킨다.

```text
BOOK-STORY-01-C03
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

v1에서는 별도 revision 파일을 병렬 보관하지 않는다. 현재 YAML의 `revision`이 current canonical revision을 나타내고 과거 내용은 Git history와 Run source snapshot이 보존한다.

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
3. 이미지 생성 진행률과 Run 성공/실패는 `GENERATION_RUN_MODEL.md`가 책임진다.
4. 대표 승인 이미지와 Asset 상태는 `ASSET_STORAGE_POLICY.md`가 책임진다.
5. 필요하다면 UI에서 `production_status`를 도출해 보여줄 수 있지만 Episode/Cut Canonical 데이터에 이를 중복 저장하지 않는다.
6. 따라서 설계는 승인됐지만 이미지가 아직 없는 Cut도 정상적인 상태다.
7. 새 Provider로 이미지를 다시 생성하더라도 Canonical Scene 정의가 바뀌지 않았다면 Cut revision과 Definition Status는 그대로 유지할 수 있다.

### Production Complete의 개념

이미지 제작 완료 여부는 Definition Status와 별도로 판단한다.

초기 개념상 Cut이 production complete가 되려면 현재 승인된 Cut revision을 기준으로 **대표 승인 Asset이 최소 하나 존재**해야 한다.

Episode의 production complete는 active Cut 전체가 production complete일 때 도출할 수 있다.

production complete는 `ASSET_STORAGE_POLICY.md`의 대표 Asset / availability / current canonical revision 조건으로 도출한다.

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

Generation Run과 Image Asset의 상세 식별 체계는 `GENERATION_RUN_MODEL.md`와 `ASSET_STORAGE_POLICY.md`를 따른다.

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

물리 삭제 또는 retire로 current tree에서 사라지는 Episode / Cut ID는 `content/identity-tombstones.yaml`에 등록해 재사용을 막는다.

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
19. Storyboard Production Review는 새 필드를 요구하지 않으며, 구체 Scene Specification과 Continuity constraint를 Storyboard에 중복 저장하지 않는다.

## 15. STEP 0-2 후속 책임의 현재 해소 상태

STEP 0-2 당시 후속 단계로 넘긴 항목은 현재 다음 문서에서 해소되었다.

- Continuity field / reset → `CONTINUITY_MODEL.md`
- Library Entity reference → `LIBRARY_MODEL.md`
- Provider Prompt → `PROVIDER_INTEGRATION_MODEL.md`
- Run ID / Result Review → `GENERATION_RUN_MODEL.md`
- Image Asset ID / storage / representative → `ASSET_STORAGE_POLICY.md`
- physical YAML layout → `REPOSITORY_STRUCTURE.md`, `templates/`
- Scripture direct quote / copyright → `TEXT_AND_COPYRIGHT.md`
- revision history → current revision field + Git history + Run source snapshot

현재 의도적으로 별도 schema를 만들지 않는 항목:

- 사이트별 presentation variant
- production complete mutable status field

production complete는 Image Asset / representative relation으로 도출한다.

## 16. 현재 확정 상태

- Episode는 제작 범위/목적의 Canonical Source
- Cut은 장면 정의의 Canonical Source
- Storyboard는 active Cut order / scripture_anchor / beat의 Source of Truth
- `transition_note`는 previous active Cut → current Cut의 incoming high-level intent
- Episode/Cut Definition Approval과 image production complete 분리
- 모든 active Cut 승인 + Continuity relation 확정 후 Episode 승인
- 고정 Cut 수 없음
- revision은 같은 identity의 정의 발전
- 다른 identity로 대체될 때만 superseded
- retired ID는 tombstone registry에 등록

**Episode / Cut Model v1.1 — COMPLETED / CONFIRMED**
