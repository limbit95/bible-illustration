# Generation Run Model v1.1

> 상태: **CONFIRMED / 2026-10-06 pre-production hardening**
>
> 선행 조건:
> - **STEP 0-1 — Content Model v1.0 CONFIRMED**
> - **STEP 0-2 — Episode / Cut Model v1.0 CONFIRMED**
> - **STEP 0-3 — Continuity Model v1.0 CONFIRMED**
> - **STEP 0-4 — Library Model v1.0 CONFIRMED**
>
> 목적: 하나의 Cut을 실제 이미지로 렌더링하기 위해 Provider에 제출한 각 생성 시도를 재현·비교·평가할 수 있도록 Generation Run과 그 결과를 정의한다.
>
> Provider Integration은 `PROVIDER_INTEGRATION_MODEL.md`, 이미지 Asset 저장·승격은 `ASSET_STORAGE_POLICY.md`가 현재 Source of Truth다.

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
4. Library Entity은 실제 사용한 asset revision과 local profile도 함께 추적할 수 있어야 한다.
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
    library_id: CHR-MOSES
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

Reference strength / weight 같은 Provider별 수치는 settings 또는 Provider Integration에서 다룬다.

Project-owned binary를 actual Run reference로 제출할 때는 반드시 available Canonical Asset ID를 `asset_ref`로 사용한다.
accepted Result나 사용자 승인 working image가 reference 후보가 될 수는 있지만, 실제 downstream request에 사용하기 전에 DEC-0009에 따라 Asset Promotion을 완료한다.

권리 때문에 저장할 수 없는 외부 reference만 External Reference Record + Run snapshot 방식의 예외를 사용한다.

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

Asset Promotion linkage는 Run Result에 mutable reverse link를 추가하지 않는다.
Image Asset metadata의 `source.result_id`가 Canonical provenance relation이며 Result→Asset 조회는 이를 기준으로 역산한다.

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

## 23. STEP 0-5 후속 책임의 현재 해소 상태

STEP 0-5 당시 후속 단계로 넘긴 항목은 현재 다음 문서에서 해소되었다.

- Provider Binding / Generation Profile / Prompt Adapter → `PROVIDER_INTEGRATION_MODEL.md`
- Asset ID / 파일명 / 저장 위치 / 대표 선정 → `ASSET_STORAGE_POLICY.md`
- 실제 Template → `templates/generation-run.yaml`

v1에서 의도적으로 열어두는 항목:

- Provider별 retry/backoff 구현
- Library Reference 자체를 생성하기 위한 non-Cut Run target 확장
- Provider가 제공하지 않는 비용/모델 metadata

현재 Run target은 Cut으로 제한한다. Library Reference용 독립 생성 흐름이 실제로 필요해질 때 target model 확장을 별도 결정한다.

## 24. 현재 확정 상태

- Provider request 1회 = Run 1개
- Run은 v1에서 정확히 하나의 Cut을 target
- Run / Result ID는 과거 tombstone을 포함해 재사용 금지
- Cut revision + Git commit SHA snapshot
- Library Entity revision/profile snapshot
- observable Prompt / Reference / settings snapshot
- execution status와 Result Review 분리
- accepted Result와 representative Asset 분리
- project-owned actual reference는 available Asset ID 사용
- completed Run 입력은 immutable
- Result Review만 누적 가능
- Asset Promotion linkage는 Asset metadata의 `source.result_id`가 소유

**Generation Run Model v1.1 — COMPLETED / CONFIRMED**
