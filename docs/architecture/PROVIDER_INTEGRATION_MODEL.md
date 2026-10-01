# Provider Integration Model v1.0

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


---
