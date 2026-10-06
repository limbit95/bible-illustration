# ChatGPT Prompt Adapter — canonical-cut rev1

> Provider: `chatgpt`
>
> 역할: Canonical production data를 ChatGPT의 observable image-generation instruction으로 변환한다.
>
> 이 문서는 Canonical Scene 자체가 아니다.

## Input priority

항상 다음 우선순위를 유지한다.

1. Scripture direct content
2. Storyboard scripture anchor / beat
3. Cut Canonical Scene
4. Continuity baseline / retain / change / reset
5. Library Entity definition / profile
6. project Visual / Historical Rules
7. approved Canonical Reference Asset
8. Provider-specific execution preference

하위 입력이 상위 Canonical 정의와 충돌하면 상위 정의를 우선한다.

## Instruction composition

필요한 항목만 observable instruction에 포함한다.

- 현재 Cut의 scene intent
- required elements
- forbidden elements
- 이번 Cut에서 유지할 continuity constraint
- 이번 Cut에서 실제로 변하는 delta
- 아직 등장하면 안 되는 premature element
- 필요한 Library Entity identity / profile
- 필요한 역사·환경·시각 규칙
- 장면 목적에 필요한 camera / composition 지시
- 실제 전달하는 Reference Asset의 역할

불필요하게 모든 Canonical metadata를 자연어로 반복하지 않는다.

## Continuity reference

Project-owned image binary를 실제 reference로 전달하는 경우:

- available Canonical Asset만 사용한다.
- Run snapshot에 `asset_ref`와 역할을 기록한다.
- accepted Result / working image가 reference 후보라면 먼저 DEC-0009에 따라 Asset Promotion한다.
- reference image의 우연한 detail을 Canonical 사실로 취급하지 않는다.

## Transition-oriented Cut

Transition-oriented relation에서는 직전 이미지 복제를 목표로 하지 않는다.

Scripture chronology와 필요한 identity/event state는 유지하되
새 Cut의 핵심 visual subject에 맞춰 camera / composition / scale / lighting을 새로 설계할 수 있다.

## Provider observability

- 내부 hidden prompt를 추측하지 않는다.
- 정확한 내부 model/version이 노출되지 않으면 unknown으로 기록한다.
- 실제 사용자/Assistant가 확인 가능한 instruction과 reference context만 Run snapshot에 남긴다.
