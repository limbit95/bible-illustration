# Episode Orchestration Architecture Audit — 2026-10-06

> 상태: **PASSED / READY FOR AUTONOMOUS EPISODE RETEST**
>
> 목적: 기존 Cut 중심 Architecture 위에 Episode-level autonomous orchestration이 일관되게 추가되었는지 감사한다.
>
> 이 문서는 production automation readiness에 대해 `PREPRODUCTION_HARDENING_AUDIT.md`의 판정을 확장한다.

## 1. 추가된 정식 구조

Architecture:

- `docs/architecture/EPISODE_ORCHESTRATION_MODEL.md`

Rules:

- `docs/rules/ORCHESTRATION_RULES.md`

Templates:

- `templates/production-session.yaml`
- `templates/presentation-plan.yaml`

Decision:

- `DEC-0013 — Episode-level autonomous orchestration`

## 2. 책임 경계

### Storyboard

- active Cut order
- scripture anchor
- beat
- incoming high-level transition intent

### Cut

- current scene Canonical definition

### Continuity

- retain / change / reset

### Production Session

- 전체 위임의 execution mode / policy / cursor / blocker

### Presentation Plan

- key_scripture / explanatory / visual_only
- quote / verse / translation
- narration
- presentation status

### Generation Run

- 한 Cut에 대한 실제 Provider request 1회

중복 Source of Truth 없음.

## 3. Autonomous invariant

“전체를 알아서 만들어”는 Architecture skip이 아니라 Architecture self-execution을 뜻한다.

사용자는 Cut별 명령을 반복할 필요가 없지만
Assistant는 모든 Cut cycle을 순서대로 수행해야 한다.

## 4. Batch / Collage 감사

다음은 production Cut generation 대체물로 사용할 수 없다.

- collage
- contact sheet
- montage
- multi-Cut one-shot prompt
- 여러 다른 Cut을 한 Run target으로 묶기

Storyboard preview/contact sheet는 별도 planning artifact로 만들 수 있으나
Production Master count에 포함하지 않는다.

## 5. Presentation 감사

기존 Text Rules의 개념을 물리 Source of Truth로 연결했다.

- Production Master: text-free
- Presentation Plan: display text planning
- Key Scripture: exact quote / verse / translation required
- Explanatory: narration 분리
- Visual-only: derivative 불필요 가능

따라서:

- 본문을 모든 Master에 bake-in
- 반대로 Key Scripture를 전부 누락
- 요약문을 Scripture quote처럼 삽입

하는 세 오류를 모두 구조적으로 차단할 수 있다.

## 6. Generation Entry Gate 확장

Multi-Cut autonomous request에서는:

- `orchestration_ready = true`
- `presentation_plan_ready = true`

가 추가로 필요하다.

single-Cut assisted request에서는 not_applicable 가능.

## 7. Synthetic dry-run

`docs/progress/EPISODE_ORCHESTRATION_DRY_RUN.md`

검증:

- high-level autonomous request
- planning completeness
- Production Session
- Presentation Plan
- 3 Cut sequential cycle
- Continuity-oriented relation
- Transition-oriented relation
- Key Scripture Presentation Gate
- Explanatory Presentation Gate
- collage/batch shortcut rejection
- automatic advance / blocker behavior

결과:

**PASS**

## 8. 구조 수

이번 변경 후:

- Architecture docs: 10
- Rules docs: 8
- YAML Templates: 13

기존 Storyboard / Cut / Continuity schema 책임은 유지된다.

## 9. 판정

**PASS — EPISODE ORCHESTRATION LAYER COMPLETE**

현재 Architecture는:

- 한 Cut 수동 제작
- Multi-Cut assisted 제작
- Episode-level autonomous sequential 제작

세 흐름을 모두 표현할 수 있다.

다음 단계는 실제 이미지 생성이 포함된 Genesis 1 autonomous test다.

테스트에서도 각 Cut을 개별 cycle로 실행하고,
Presentation Plan을 실제 결과까지 적용해야 한다.
