# DEC-0005 — Scripture-unit sequential generation with flexible scene decomposition

> 상태: accepted
> 날짜: 2026-10-03
> 대체: none
> 대체됨: none

## Context

실제 제작 검증에서 여러 이미지를 한 번에 생성하면 본문 구간 누락, 장면 과도 압축, 인접 장면의 continuity drift가 한 번에 누적되어 재작업 비용이 커질 수 있음이 확인되었다.

또한 성경의 절 경계와 시각적 장면 경계는 항상 일치하지 않는다.

한 절 안에서도 시간·상태·행동의 단계가 시각적으로 중요할 수 있고, 반대로 여러 절이 하나의 자연스러운 visual beat를 구성할 수도 있다.

## Decision

성경 일러스트 제작의 기본 실전 입력 단위를 **사용자가 제시한 Scripture Work Unit**으로 둔다.

1. 사용자는 개역한글 본문 한 구절 또는 의미 있는 본문 구간을 작업 입력으로 제공한다.
2. Assistant는 생성 전에 해당 본문이 한 장면으로 충분한지, 여러 장면으로 분리해야 자연스러운지 판단한다.
3. 한 장면으로 충분하면 해당 Cut 제작으로 진행한다.
4. 여러 장면이 필요하면 이미지 생성 전에 짧은 Storyboard / Cut 분해안을 제안하고 사용자와 조정한다.
5. 한 구절은 필요에 따라 여러 Cut으로 분해할 수 있으며, 서로 강하게 연결된 여러 절은 하나의 장면으로 묶을 수 있다.
6. 실제 이미지 생성은 기본적으로 한 장면씩 순차적으로 진행한다.
7. 다음 장면 생성 시 직전 accepted 또는 사용자 승인 working image를 visual continuity reference로 사용할 수 있다.
8. 이전 이미지의 우연한 요소가 Canonical 사실이 되는 것은 아니며 Scripture / Cut / Continuity 정의가 항상 우선한다.
9. 여러 Cut의 batch generation은 사용자가 명시적으로 원하거나 Continuity 위험이 낮을 때만 사용한다.
10. 빠른 제작은 대량 batch보다 과도한 polish, Asset promotion, Git LFS ingest, representative 확정을 뒤로 미루는 방식으로 확보한다.

## Storyboard advisory rule

한 본문 안에 시각적 단계가 여러 개 있다고 판단되면 Assistant가 먼저 최소 Storyboard를 제안한다.

Storyboard 제안은 필요한 만큼만 다음을 포함한다.

- Cut 수
- 각 Cut의 Scripture Anchor
- 각 Cut의 핵심 visual beat
- Scene Relation의 high-level 판단
- 제작 전 확인이 필요한 continuity concern

사용자가 승인하거나 수정한 뒤 생성한다.

세부 retain / change / reset constraint의 Canonical Source of Truth는 Storyboard가 아니라 Continuity다.

## Approved-image anchor rule

직전 사용자 승인 이미지 또는 accepted Result는 다음 장면의 visual continuity anchor로 사용할 수 있다.

우선순위:

1. Scripture direct content
2. Canonical Cut / Storyboard / Continuity
3. approved working / accepted image as visual anchor
4. Provider output의 incidental detail

따라서 이전 이미지가 본문 또는 Canonical 정의와 충돌하면 해당 요소는 다음 장면에 계승하지 않는다.

## Alternatives Considered

### 여러 Cut을 기본적으로 일괄 생성

초기 출력 속도는 빠르지만 본문 누락이나 Continuity 오류가 여러 이미지에 동시에 확산될 수 있어 기본값으로 채택하지 않는다.

### 한 구절 = 한 이미지 고정

운영은 단순하지만 시각적 단계가 중요한 본문을 과도하게 압축하거나 하나의 자연스러운 beat를 불필요하게 쪼갤 수 있어 채택하지 않는다.

## Consequences

- 본문 누락 여부를 더 쉽게 통제할 수 있다.
- Cut 수를 본문과 시각적 변화량에 맞게 유연하게 결정한다.
- 인접 장면 Continuity를 매 단계 검토할 수 있다.
- Storyboard 판단이 필요한 경우 생성 전에 짧은 설계 단계가 추가된다.
- Asset / LFS 정리는 production 흐름을 불필요하게 막지 않도록 적절한 시점까지 미룰 수 있다.

## Related Sources

- `docs/rules/MASTER_RULES.md`
- `docs/rules/CONTINUITY_RULES.md`
- `docs/rules/GENERATION_RULES.md`
- `docs/architecture/EPISODE_CUT_MODEL.md`
