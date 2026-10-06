# Generation Rules v1.0

> 상태: **CONFIRMED / 2026-10-02 사용자 승인**
>
> 목적: Canonical Scene을 실제 Provider 생성 요청으로 변환하고 Result를 검토·기록·보존하는 운영 규칙을 정의한다.

## 1. Canonical First

생성 전에 다음을 먼저 확인한다.

- Cut scene intent
- required / forbidden elements
- Storyboard Scripture Anchor / beat
- 관련 Library definition / profile
- Continuity constraint
- 필요한 Historical / External Reference

Prompt가 이 데이터를 대체하지 않는다.

## 2. Provider는 Adapter다

ChatGPT, OpenArt, Higgsfield 등은 Rendering Provider다.

- Provider의 character slot이 Character identity가 아니다.
- Provider preset이 Canonical Style이 아니다.
- Provider가 기능을 지원하지 않아도 Canonical requirement를 자동 삭제하지 않는다.
- capability mismatch는 unresolved requirement로 드러낸다.

## 3. Prompt / Instruction

실제 observable generation instruction은 Provider Adapter / Profile을 통해 준비한다.

Prompt는 파생 데이터다.

- 별도 Canonical `prompt.md`를 만들지 않는다.
- Run에는 실제 확인 가능한 Prompt / instruction snapshot을 보존한다.
- Provider 내부 hidden prompt를 추측하지 않는다.
- Prompt를 수정하고 다시 생성하면 새 Run이다.

## 4. 탐색 Run

draft / in_review Cut에서도 탐색 Run을 허용한다.

목적:

- 구도 가능성 확인
- 복잡한 장면의 표현 가능성 확인
- Reference 효과 확인
- 장면 정의의 문제 조기 발견

단:

- 탐색 Result는 Canonical 정의를 자동 변경하지 않는다.
- 오래된 draft Result가 예쁘다는 이유로 현재 Canonical 기준을 낮추지 않는다.
- 대표 Asset 선정 전 현재 approved 기준으로 다시 검토한다.

## 5. 한 Run의 경계

Provider에 실제 제출한 요청 1회를 Run 1개로 본다.

- 동일 Prompt 재생성 → 새 Run
- Prompt 변경 → 새 Run
- Reference 변경 → 새 Run
- model / Provider / 주요 setting 변경 → 새 Run
- 실제 새 Provider request/job을 만든 retry → 새 Run
- 다운로드 / 파일명 변경 / Review 추가 → 같은 Run

한 요청에서 여러 이미지가 반환되면 하나의 Run 아래 여러 Result로 관리한다.

## 6. Reference 사용

Reference는 역할을 명시한다.

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

특정 이미지 하나를 모든 목적의 Reference로 사용하지 않는다.

프로젝트가 통제하는 중요한 Reference binary는 가능한 한 Run 전에 Asset으로 등록한다.

## 7. 인접 Cut 생성 입력

같은 사건의 연속 Cut에서는 가능하면 바로 이전 Cut의 accepted / working image를 continuity reference로 사용한다.

생성 instruction은 장면 전체를 새로 설명하기보다:

- 이전 Cut에서 유지할 요소
- 이번 Cut에서 바뀔 delta
- 아직 등장하면 안 되는 미래 요소

를 중심으로 작성한다.

이전 Cut의 이미지 전체를 Canonical 사실로 승격하는 것은 아니며,
Reference는 Canonical Continuity를 전달하기 위한 보조 입력이다.

## 8. 빠른 생성-검토 루프

설계를 지나치게 오래 끌지 않는다.

방향과 핵심 constraint가 충분히 명확해지면 소수의 탐색 Result를 빠르게 확인하고, 결과를 바탕으로 필요한 부분만 수정한다.

그러나 방향이 불명확한 상태에서 대량 생성으로 문제를 해결하려 하지 않는다.

## 9. Result Review

최소 평가 축:

- scripture_fidelity
- historical_accuracy
- continuity
- library_consistency
- visual_quality
- technical_integrity

판정 값:

- pass
- concern
- fail
- not_applicable

Review decision:

- accepted
- rejected

숫자 점수는 필수화하지 않는다.

## 10. Rejection 우선 조건

다음은 대표적인 rejection 사유다.

- SCRIPTURE_MISMATCH
- REQUIRED_ELEMENT_MISSING
- FORBIDDEN_ELEMENT_PRESENT
- PREMATURE_ELEMENT
- HISTORICAL_MISMATCH
- CONTINUITY_MISMATCH
- CHARACTER_MISMATCH
- LOCATION_MISMATCH
- OBJECT_MISMATCH
- COSTUME_MISMATCH
- STYLE_DRIFT
- COMPOSITION_ISSUE
- TECHNICAL_ARTIFACT
- TEXT_ARTIFACT

시각적으로 예쁘더라도 핵심 Scripture / Canonical requirement에 실패하면 통과시키지 않는다.

## 11. Review 이력

Review를 한 개의 mutable status로 덮어쓰지 않는다.

Canonical Cut revision이나 기준 commit이 달라져 판정이 달라지면 새 Review를 추가한다.

과거 accepted가 현재도 자동 accepted라는 뜻은 아니다.

## 12. 실패 Run

실제로 Provider에 제출된 의미 있는 실패 Run은 보존한다.

실패 Run은 다음 학습에 사용될 수 있다.

- 특정 model의 반복 오류
- Character drift
- Continuity mismatch
- Prompt 패턴 문제
- Provider 안정성
- 비용 대비 결과

요청 자체가 Provider에 전달되지 않은 단순 UI 실패까지 Run으로 만들 필요는 없다.

## 13. Asset Promotion

모든 Result를 Asset으로 승격하지 않는다.

장기 보존 가치가 있는 Result만:

1. Asset ID 발급
2. source Result 연결
3. 원본 binary 보존
4. checksum / provenance / rights 기록
5. Cut / Library / Continuity 관계 연결

을 거쳐 Asset으로 등록한다.

대표 Asset 선정과 Result accepted는 같은 개념이 아니다.

## 14. 이미지 수정

생성형 edit가 실제 Provider 요청으로 제출되면 새 Run이다.

수정 원본은 `edit_source` Reference로 기록한다.

의미 있는 pixel 변경 결과를 Asset으로 보존할 경우 기존 Asset binary를 덮어쓰지 않고 새 Asset ID를 사용한다.

## 15. 비용과 보안

가능하면 cost / credit 정보를 기록하지만 필수값은 아니다.

다음은 저장하지 않는다.

- API key
- access token
- password
- session cookie
- secret token

알 수 없는 model/version/hidden setting은 추측하지 않는다.


## 16. Scripture-unit sequential generation

기본 실전 생성 흐름은 DEC-0005를 따른다.

### 16.1 입력 단위

사용자가 개역한글 본문 한 구절 또는 의미 있는 본문 구간을 제시하면
이를 현재 작업의 Scripture Work Unit으로 취급한다.

사용자가 직접 제공한 본문 문구는 임의로 현대화, 축약, 교정하지 않는다.
직접 인용 표기는 `TEXT_AND_COPYRIGHT.md`를 따른다.

### 16.2 장면 수 판단

생성 전에 다음을 판단한다.

- 한 장면으로 의미가 충분히 전달되는가
- 시각적 변화가 여러 단계인가
- 한 화면에 압축하면 Continuity가 급격히 뛰는가
- 인접 본문과의 경계를 별도 Cut으로 나누는 편이 자연스러운가

여러 장면이 필요하면 먼저 짧은 Storyboard를 제안하고 사용자와 조정한다.

### 16.3 한 장면씩 생성

기본적으로 실제 이미지는 한 장면씩 순차 생성한다.

다음 장면 생성 전에는 바로 이전 장면에 대해:

- 사용자 승인 여부
- accepted / working status
- 유지해야 할 RETAIN
- 새로 바뀔 DELTA
- 계승하면 안 되는 오류 또는 우연한 detail

을 확인한다.

### 16.4 승인 이미지의 역할

직전 사용자 승인 이미지 또는 accepted Result는
다음 장면의 primary visual continuity reference로 사용할 수 있다.

그러나 우선순위는 다음과 같다.

1. Scripture direct content
2. Canonical Cut / Storyboard / Continuity
3. approved working / accepted image reference
4. Provider output의 우연한 detail

따라서 승인 이미지라도 Canonical 정의와 충돌하는 요소는 자동 계승하지 않는다.

### 16.5 Batch generation

여러 Cut을 한 번에 생성하는 것은 opt-in 방식이다.

- 사용자가 명시적으로 요청했거나
- 각 Cut의 장면 정의가 충분히 독립적이며
- Continuity 위험이 낮을 때

사용할 수 있다.

Batch 결과에서 인접 장면 연결이 깨지면 batch 효율을 이유로 유지하지 않고
순차 생성 방식으로 되돌린다.


## 17. Scene Relation preflight

이미지 생성 전에 직전 Cut과의 Scene Relation을 확인한다.

판단 결과는 두 제작 방향 중 하나다.

- `Continuity`: 이전 장면의 시각 상태를 적극적으로 계승
- `Transition`: Story는 이어가되 새로운 visual treatment 허용

### Continuity 생성 입력

- 직전 승인 이미지를 primary continuity reference로 사용
- RETAIN 명시
- DELTA 명시
- FORBIDDEN LEAP 명시
- 필요 없는 새 구도·팔레트·카메라 변화 최소화

### Transition 생성 입력

- 직전 이미지 복제를 목표로 하지 않는다.
- 새 본문의 핵심 subject와 visual intent를 중심으로 새 구도를 설계한다.
- 필요한 identity / 사건 상태만 유지한다.
- 카메라, 스케일, 조명, 분위기, 팔레트는 새롭게 구성할 수 있다.
- 단 Scripture chronology와 Canonical fact는 reset하지 않는다.

사용자가 별도 Relation Mode를 지정하지 않으면 Assistant가 먼저 판단한다.
판단이 애매하거나 장면 수에도 영향을 주면 이미지 생성 전에 짧은 Storyboard 제안을 한다.
