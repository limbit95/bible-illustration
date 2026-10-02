# DEC-0005 — Scripture-unit sequential generation with flexible scene decomposition

> 상태: accepted
> 날짜: 2026-10-03
> 대체: none
> 대체됨: none
> 관련: DEC-0004

## Context

Genesis Creation prototype을 실제로 반복 생성하면서, 한 번의 명령으로 여러 이미지를 연속 생성하는 방식은
전체 제작 속도를 높이기보다 인접 장면의 연결 단절, 본문 구간 누락, 과도한 장면 압축 때문에
재생성과 수정 횟수를 늘리는 경우가 많았다.

반대로 사용자가 개역한글 본문을 한 구간씩 작업 입력으로 제공하고,
그 본문이 요구하는 장면 수를 먼저 판단한 뒤
직전 승인 이미지와의 Continuity를 유지하면서 한 장면씩 진행했을 때
본문 누락을 줄이고 변화량을 더 정밀하게 통제할 수 있었다.

또한 한 구절이 항상 한 장면에 대응하지는 않는다.
분리, 등장, 이동, 변화, 시간 진행처럼 시각적 단계가 중요한 본문은
한 구절 또는 한 본문 단위를 여러 Cut으로 나누는 편이 더 자연스러울 수 있다.

## Decision

성경 일러스트 제작의 기본 실전 입력 단위를 **사용자가 제시한 Scripture work unit**으로 둔다.

1. 사용자는 개역한글 본문 한 구절 또는 의미 있는 본문 구간을 작업 입력으로 제공한다.
2. Assistant는 생성 전에 해당 본문이:
   - 한 장면으로 충분한지
   - 여러 장면으로 분리해야 자연스러운지
   판단한다.
3. 한 장면으로 충분하면 바로 해당 Cut 제작으로 진행한다.
4. 여러 장면이 필요하면 이미지 생성 전에 짧은 Storyboard / Cut 분해안을 제안하고 사용자와 조정한다.
5. 한 구절은 필요에 따라 여러 Cut으로 분해할 수 있으며, 반대로 서로 강하게 연결된 여러 절은 하나의 장면으로 묶을 수 있다.
6. 실제 이미지 생성은 **기본적으로 한 장면씩 순차적으로** 진행한다.
7. 다음 장면 생성 시 직전 accepted 또는 사용자 승인 working image를 primary visual continuity reference로 사용한다.
8. 단, 이전 이미지의 우연한 요소가 Canonical 사실이 되는 것은 아니며, Scripture / Cut / Continuity 정의가 항상 우선한다.
9. 한 번의 요청으로 여러 이미지를 일괄 생성하는 방식은 기본값이 아니다. 사용자가 명시적으로 원하거나 Continuity 위험이 낮은 경우에만 사용한다.
10. 이 운영 방식은 DEC-0004의 “전체 Chapter를 먼저 prototype으로 완주하고 Asset/LFS 정리는 나중에 한다”는 원칙과 함께 적용한다.
11. 따라서 여기서 말하는 rapid prototype의 “빠름”은 대량 일괄 생성이 아니라:
    - Production Master 확정 지연
    - Asset promotion 지연
    - Git LFS ingest 지연
    - 과도한 polish 지연
    을 통해 확보한다.

## Storyboard advisory rule

사용자가 장면 분할을 직접 설계하기 어렵거나,
한 본문 안에 시각적 단계가 여러 개 있다고 판단되면
Assistant가 먼저 최소 Storyboard를 제안한다.

Storyboard 제안은 다음만 간결하게 포함한다.

- Cut 수
- 각 Cut의 Scripture Anchor
- 각 Cut의 핵심 visual beat
- 이전 장면에서 유지할 RETAIN
- 이번 장면에서 바뀔 DELTA
- 아직 나오면 안 되는 FORBIDDEN LEAP

사용자가 승인하거나 수정한 뒤 생성한다.

## Approved-image anchor rule

직전 사용자 승인 이미지 또는 accepted Result는
다음 장면의 **primary visual continuity anchor**로 사용할 수 있다.

그러나 우선순위는 다음과 같다.

1. Scripture direct content
2. Canonical Cut / Storyboard / Continuity
3. approved working / accepted image as visual anchor
4. Provider prompt / generated incidental detail

따라서 이전 이미지가 본문 또는 Canonical 정의와 충돌하면
그 충돌 요소는 다음 장면에 그대로 계승하지 않는다.

## Alternatives Considered

### 한 명령으로 Chapter 전체 또는 여러 Cut을 일괄 생성

초기 속도는 빨라 보이지만,
본문 누락과 Continuity drift가 발생했을 때 여러 이미지를 다시 만들어야 하므로
실제 수정 비용이 커졌다.

### 한 구절 = 한 이미지 고정

운영은 단순하지만
분리·등장·변화 같은 단계적 사건을 한 장면에 과도하게 압축하게 되고,
반대로 여러 절이 하나의 자연스러운 beat인 경우 불필요하게 쪼개질 수 있다.

## Consequences

- 사용자가 본문 누락 여부를 직접 통제하기 쉬워진다.
- Cut 수는 본문과 시각적 변화량에 맞게 유연하게 결정된다.
- 인접 장면 Continuity를 매 단계 확인할 수 있다.
- 여러 장을 일괄 생성하는 체감 속도는 줄지만 재작업 비용은 낮아질 수 있다.
- Storyboard 판단이 필요한 순간에는 생성 전에 짧은 설계 대화가 추가된다.
- Asset / LFS 정리는 계속 전체 prototype 선별 이후로 미룰 수 있다.

## Related Sources

- `docs/rules/MASTER_RULES.md`
- `docs/rules/CONTINUITY_RULES.md`
- `docs/rules/GENERATION_RULES.md`
- `docs/progress/GENESIS_CREATION_RAPID_PROTOTYPE.md`
- `docs/decisions/DEC-0004-genesis-rapid-full-chapter-prototype.md`
