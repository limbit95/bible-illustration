# DEC-0013 — Add Episode-level autonomous orchestration above the Cut production loop

> 상태: accepted
> 날짜: 2026-10-06
> 관련: DEC-0005, DEC-0006, DEC-0007, DEC-0012

## Context

기존 Architecture는 Cut 한 장을 정확히 제작하는 데 강했지만,
“창세기 1장 전체를 알아서 만들어” 같은 Episode-level 위임을 끝까지 실행하는 상위 control flow가 명확하지 않았다.

그 결과 자동화 테스트에서:

- 전체 Storyboard를 실제 Cut cycle로 실행하지 않고 collage로 축약
- continuity를 실제 순차 reference에 반영하지 않음
- Production Master와 Presentation output 혼동

이 발생했다.

## Decision

Cut-level Architecture 위에 **Episode Orchestration Layer**를 추가한다.

새 정식 구성:

- `docs/architecture/EPISODE_ORCHESTRATION_MODEL.md`
- `docs/rules/ORCHESTRATION_RULES.md`
- `templates/production-session.yaml`
- `templates/presentation-plan.yaml`

Multi-Cut autonomous request는 첫 Provider image request 전에
Production Session과 Presentation Plan을 포함한 planning completeness를 갖춰야 한다.

## Execution invariant

autonomous Episode production은:

~~~text
Storyboard order
→ Cut 1 cycle
→ Review
→ Presentation
→ Cut 2 cycle
→ Review
→ Presentation
→ ...
→ Episode scope complete
~~~

로 실행한다.

여러 Cut을 한 collage/contact sheet/batch request로 대체하지 않는다.

## Presentation

Production Master는 text-free로 유지한다.

최종 사용자-facing text는 `presentation-plan.yaml`을 통해:

- key_scripture
- explanatory
- visual_only

중 하나로 계획한다.

Key Scripture는 정확한 quote / verse / translation이 준비되어야 Presentation Gate를 통과한다.

## Why new templates are justified

`production-session.yaml`은 Storyboard/Run이 소유하지 않는 Episode-level execution control을 기록한다.

`presentation-plan.yaml`은 기존 Text Rules가 요구했지만 물리 Source of Truth가 없던 presentation intent와 display text를 기록한다.

둘 다 기존 Storyboard/Cut/Continuity/Run 데이터를 복제하지 않는다.

## Consequences

- 사용자는 “전체를 알아서 만들어”라고만 해도 된다.
- autonomous mode가 batch shortcut을 의미하지 않게 된다.
- Storyboard 계획과 실제 Cut 생성 사이의 연결이 강제된다.
- Production Master와 Presentation derivative가 명확히 분리된다.
- Key Scripture 누락이나 임의 요약 caption 삽입을 Presentation Gate에서 차단할 수 있다.
