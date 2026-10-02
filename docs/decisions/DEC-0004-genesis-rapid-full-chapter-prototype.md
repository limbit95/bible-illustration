# DEC-0004 — Genesis Creation rapid full-chapter prototype before Asset promotion

> 상태: accepted
> 날짜: 2026-10-02
> 대체: none
> 대체됨: none

## Context

Genesis Creation 초반 Cut을 실제 생성하면서 개별 장면의 미세 완성도보다
인접 이미지 사이의 변화량과 연결 연출이 전체 체감 품질에 더 큰 영향을 준다는 점이 확인되었다.

특히 사용자가 선택한 00→06 working reference sequence에서는
각 장면이 독립된 새 그림처럼 바뀌는 것보다 이전 장면의 구조·질감·색조를 유지한 채
본문이 요구하는 변화만 점진적으로 발생할 때 가장 자연스럽게 느껴졌다.

초반 Cut을 production complete로 만드는 작업을 매번 선행하면
창세기 1장 전체 흐름을 보기 전에 한 구간에 과도하게 오래 머물게 되는 문제도 확인되었다.

## Decision

Genesis Creation의 다음 실전 제작은 **전체 장을 먼저 빠르게 완주하는 prototype pass**로 진행한다.

1. 창세기 1장 전체 시퀀스를 먼저 끝까지 생성한다.
2. 인접 Cut의 continuity와 변화량을 개별 이미지의 미세 polish보다 우선한다.
3. 이미지 생성·수정은 빠르게 반복하되, production master 승격과 Git LFS ingest는 최종 선별 뒤로 미룬다.
4. Prototype 동안 생성 Run / 사용자 판단의 핵심 이력은 보존하되, binary Asset promotion이 다음 Cut 진행을 막지 않는다.
5. 전체 시퀀스를 한 번 본 뒤 representative 후보와 production master를 선별한다.
6. 선별된 결과만 기존 Asset / Git LFS 정책에 따라 장기 보존한다.
7. 본문·나레이션이 합성된 테스트본은 presentation derivative이며 Production Master를 대체하지 않는다.

기존 C05/C06 accepted Result / pending_ingest Asset metadata는 historical production-validation 기록으로 유지한다.
새 prototype pass의 최종 결과로 자동 간주하지 않는다.

## Continuity operating principle

인접 Cut은 기본적으로 다음 질문으로 제작한다.

> **이전 이미지에서 그대로 남아야 하는 것은 무엇이고, 이번 본문 때문에 새로 변해야 하는 것은 정확히 무엇인가?**

변화하지 않는 요소는 가능한 한 고정한다.

- 시점 / 카메라 높이 / 광각감
- 주요 물결 또는 환경 덩어리의 방향성
- 팔레트와 재질
- 광원의 위치와 진행 방향
- 큰 공간 구조

변화 요소는 본문이 요구하는 최소 변화부터 시작한다.

예:
- 흑암 → 아주 약한 빛 → 빛의 증가
- 하나의 원초적 물 세계 → 작은 분리 징후 → 분리된 공간
- 물만 있는 상태 → 물이 모임 → 낮고 젖은 뭍이 먼저 드러남

갑자기 완성된 다음 상태를 만들어 시간적 단계를 건너뛰지 않는다.

## Alternatives Considered

### Cut마다 production complete 후 다음 Cut 진행

정밀도는 높지만 전체 Episode 흐름을 보기 전에 초반부에 지나치게 오래 머무는 문제가 있었다.

### 모든 prototype binary를 즉시 Git LFS에 보존

재현성은 높지만 선별 전 대량 후보가 Canonical Asset 영역에 누적되고 제작 속도가 떨어진다.

## Consequences

- 전체 Episode의 리듬과 Continuity를 더 일찍 검증할 수 있다.
- 사용자는 시퀀스 전체를 본 뒤 선호 기준을 더 정확히 결정할 수 있다.
- prototype binary는 선별 전까지 Canonical 장기 보존본이 아니므로 소실 위험을 감수해야 한다.
- 최종 선별 후 Run/Review/Asset/LFS 정리가 별도 consolidation 단계로 필요하다.

## Related Sources

- `docs/rules/CONTINUITY_RULES.md`
- `docs/rules/VISUAL_RULES.md`
- `docs/rules/TEXT_AND_COPYRIGHT.md`
- `docs/architecture/ASSET_STORAGE_POLICY.md`
- `docs/progress/GENESIS_CREATION_RAPID_PROTOTYPE.md`
