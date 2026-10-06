# DEC-0006 — Scene relation policy: continuity when story progression requires it, transition when a new visual treatment is stronger

> 상태: accepted
> 날짜: 2026-10-06
> 대체: none
> 대체됨: none
> 관련: DEC-0005

## Context

모든 인접 이미지를 직전 이미지와 강하게 연결하면 새로운 사건이나 핵심 visual subject가 시작될 때 시각적 연출이 불필요하게 제한될 수 있다.

반대로 모든 장면을 독립적인 새 이미지로 만들면 동일 사건의 진행 단계가 끊기고 Story Continuity가 약해질 수 있다.

따라서 인접 Cut마다 연결이 중요한지, 전환이 더 적절한지를 먼저 판단하는 운영 규칙이 필요하다.

## Decision

Assistant는 새 Cut을 생성하기 전에 직전 Cut과의 **Scene Relation**을 판단한다.

### Continuity-oriented relation

다음과 같은 경우 시각적 연결을 우선한다.

- 같은 사건의 직접적인 다음 단계
- 같은 공간에서 상태가 점진적으로 변화
- 본문의 핵심이 분리, 등장, 이동, 성장, 감소처럼 연속 변화에 있음
- 중간 단계를 건너뛰면 사건 이해가 왜곡됨

이 경우 기존 Continuity Model의 `continue` 또는 필요한 경우 `partial_reset`을 사용한다.

핵심은 Canonical Continuity에서 유지할 상태와 실제 변화량을 분명히 하는 것이다.

### Transition-oriented relation

다음과 같은 경우 새로운 시각 연출로 전환할 수 있다.

- 새로운 사건이 시작됨
- 본문의 핵심 visual subject가 달라짐
- 이전 구도와 분위기를 유지하면 장면 의미가 약해짐
- 반복적 이미지가 이어져 새로운 관점, 스케일, 구도, 분위기가 본문 전달에 더 적합함
- Story 흐름은 이어지지만 시각적으로 새 beat처럼 보여주는 편이 효과적임

이 경우 기존 Continuity Model의 `partial_reset` 또는 `reset`을 사용할 수 있다.

Transition은 Story Continuity를 버리는 것이 아니다.

Scripture의 시간·사건·인과 관계는 유지하면서 카메라, 구도, 팔레트, 스케일, 조명, 분위기 같은 시각 축을 새롭게 설계할 수 있다는 뜻이다.

## Assistant default judgment

사용자가 별도 지시하지 않아도 Assistant가 우선 판단한다.

판단 순서:

1. 직전 Cut과 동일 사건의 직접적 다음 순간인가?
2. 이전 상태를 보존해야 이번 본문이 자연스럽게 이해되는가?
3. 강한 시각 연결이 오히려 새 본문의 핵심을 약화시키는가?
4. 같은 구도 / 분위기의 반복 때문에 시각적 의미가 중복되는가?
5. 새로운 visual treatment가 본문을 더 정확하고 효과적으로 전달하는가?

사용자가 명시적으로 연결 또는 전환을 요청하면 Scripture / Architecture와 충돌하지 않는 한 사용자 지시를 우선한다.

## Architecture mapping

새 enum이나 별도 Canonical relation 필드를 추가하지 않는다.

기존 Architecture의 Transition Mode를 사용한다.

- `continue`
- `partial_reset`
- `reset`

Scene Relation은 제작 판단 레이어이며 Canonical 저장 시 기존 Continuity transition mode와 Storyboard의 `transition_note`를 사용한다.

대략적인 매핑:

- Continuity-oriented → `continue` 또는 `partial_reset`
- Transition-oriented → `partial_reset` 또는 `reset`

Storyboard에는 high-level transition intent만 기록하고 실제 retain / change / reset constraint는 Continuity가 소유한다.

## Consequences

- 모든 이미지를 강제로 이어 붙이는 문제를 줄인다.
- 동일 사건의 단계적 변화는 자연스럽게 유지한다.
- 새로운 사건에서는 구도·분위기·스케일을 새롭게 설계할 수 있다.
- Storyboard 단계에서 인접 Cut의 관계 판단이 중요해진다.
- 사용자는 필요할 때 Assistant 판단을 수정할 수 있다.

## Related Sources

- `docs/architecture/CONTINUITY_MODEL.md`
- `docs/architecture/EPISODE_CUT_MODEL.md`
- `docs/rules/MASTER_RULES.md`
- `docs/rules/CONTINUITY_RULES.md`
- `docs/rules/GENERATION_RULES.md`
- `docs/decisions/DEC-0005-scripture-unit-sequential-generation.md`
