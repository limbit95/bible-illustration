# DEC-0011 — Represent Episode visual direction through existing owners instead of a new profile entity

> 상태: accepted
> 날짜: 2026-10-06
> 대체: none
> 대체됨: none

## Context

Visual Rules에는 Episode-specific Visual Profile 개념이 있었지만 Canonical owner가 명확하지 않았다.

특히 "Canonical Continuity 또는 approved working guide / reference record"에 기록한다는 표현은 정식 schema가 없는 working guide를 사실상 Source of Truth처럼 사용할 위험이 있었다.

## Decision

v1에서는 별도 `Episode Visual Profile` entity나 새 YAML schema를 추가하지 않는다.

Episode-level visual direction을 책임별로 분해한다.

### Reusable visual identity

여러 Episode에서 재사용할 가치가 있는 시각 언어는 **Library Visual Style Entity**가 소유한다.

### Episode / Segment-wide visual state

한 Episode 또는 연속 구간에서 유지·진행되는 팔레트, 질감, 조명 상태, 공간 방향 등은 **Continuity Segment baseline의 `visual` 영역**이 소유한다.

### Cut-specific visual direction

특정 장면의 카메라, 구도, dominant subject, 장면별 강조는 **Cut Canonical Scene Specification**이 소유한다.

### Provider execution configuration

모델, 해상도, Provider-specific setting, reference weight, 실행용 aspect ratio 같은 값은 **Generation Profile / Run snapshot**이 소유한다.

단, 어떤 화면 비율 자체가 프로젝트의 작품적 요구사항으로 정식 Rules에 승격된 경우에는 Provider 편의 설정이 아니라 상위 Visual Rule로 다룬다.

### Working guide

working note나 대화는 임시 설계 보조물일 수 있지만 Canonical Source of Truth가 아니다.

장기 유지할 visual decision은 위 정식 owner 중 하나에 반영한다.

## Consequences

- 새 Episode-specific profile schema가 필요하지 않다.
- Continuity와 Cut, Provider Profile의 책임 중복을 줄인다.
- 임시 working guide가 Canonical 규칙처럼 남는 문제를 막는다.
- 향후 반복적으로 독립 관리해야 하는 Episode visual metadata가 실제로 생기면 새 entity 도입을 다시 검토할 수 있다.

## Related Sources

- `docs/rules/VISUAL_RULES.md`
- `docs/architecture/LIBRARY_MODEL.md`
- `docs/architecture/CONTINUITY_MODEL.md`
- `docs/architecture/PROVIDER_INTEGRATION_MODEL.md`
- `docs/architecture/EPISODE_CUT_MODEL.md`
