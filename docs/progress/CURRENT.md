# Current Project State

> 마지막 갱신: 2026-10-06
> 상태: **EPISODE ORCHESTRATION LAYER COMPLETE / READY FOR AUTONOMOUS RETEST**

## 현재 Phase

- STEP 0 Architecture Definition: **COMPLETED**
- STEP 0-1 ~ STEP 0-7: **CONFIRMED / HARDENED**
- Storyboard Production Rules refinement: **COMPLETED**
- Pre-Production Architecture Hardening: **COMPLETED**
- Enforced Generation Entry Gate: **ACTIVE — DEC-0012**
- Episode Orchestration Model v1.0: **CONFIRMED — DEC-0013**
- Orchestration Rules v1.0: **CONFIRMED**
- Repository Structure v1.2: **CONFIRMED**
- YAML Templates: **13**
- Episode Orchestration synthetic dry-run: **PASSED**
- Episode Orchestration Audit: **PASSED**

## 이번 보완의 핵심

기존 Architecture는 Cut 한 장의 제작 규칙은 강했지만,
“창세기 1장 전체를 알아서 만들어” 같은 high-level Multi-Cut 위임의 control flow가 약했다.

현재는 다음 상위 실행계층을 추가했다.

~~~text
User full-scope request
→ Episode Production Session
→ Episode / Storyboard / Cut / Continuity
→ Presentation Plan
→ Storyboard Preflight
→ for each active Cut in Storyboard order
     Generation Entry Gate
     → Production Master
     → Result Review
     → Presentation Gate
     → next Cut
→ Episode scope complete
~~~

## Autonomous 의미

autonomous는 Architecture를 생략하거나 여러 Cut을 한 번에 생성한다는 뜻이 아니다.

**사용자가 매 Cut마다 “다음”이라고 말하지 않아도 Assistant가 기존 Cut cycle을 스스로 반복 수행한다는 뜻**이다.

금지:

- 여러 active Cut을 한 collage로 만들어 대체
- 서로 다른 Cut을 하나의 Generation Run으로 batch 처리
- Storyboard contact sheet를 Production Master로 간주
- rejected Cut을 건너뛰고 다음 Cut을 확정
- Production Master와 Presentation output 혼동

## Presentation 구조

Production Master는 계속 text-free다.

사용자-facing output은 `presentation-plan.yaml`에서 Cut별로:

- `key_scripture`
- `explanatory`
- `visual_only`

중 하나로 계획한다.

Key Scripture는 정확한 quote / verse / translation이 준비되어야 Presentation Gate를 통과한다.

## 현재 Production 위치

main에는 active Genesis production이 없다.

- current_episode: none
- current_cut: none
- active_storyboard: none
- active_continuity: none
- active_production_session: none
- active_presentation_plan: none
- active_genesis_assets: none

이전 Genesis production identity 36개는 `content/identity-tombstones.yaml`에서 재사용을 금지한다.

## 현재 Source of Truth

- Entry: `AGENTS.md`
- Current: `docs/progress/CURRENT.md`
- Architecture: `docs/architecture/*.md`
- Episode Orchestration: `docs/architecture/EPISODE_ORCHESTRATION_MODEL.md`
- Production Rules: `docs/rules/*.md`
- Orchestration Rules: `docs/rules/ORCHESTRATION_RULES.md`
- Templates: `templates/*.yaml`
- Latest autonomous readiness audit: `docs/progress/EPISODE_ORCHESTRATION_AUDIT.md`
- Dry-run evidence: `docs/progress/EPISODE_ORCHESTRATION_DRY_RUN.md`
- Decisions: `docs/decisions/DEC-*.md`

## 다음 작업

다음 단계는 **Genesis 1 전체 autonomous automation 재테스트**다.

테스트는 main이 아닌 별도 non-production branch에서 수행한다.

이번 재테스트의 성공 조건:

1. 창세기 1장 전체를 충분한 visual beat로 Storyboard화
2. Production Session + Presentation Plan 생성
3. 첫 이미지 생성 전에 planning completeness 확인
4. 한 Cut씩 개별 Production Master 생성
5. Continuity-oriented 구간에서 실제 직전 image reference 활용
6. Transition-oriented 구간에서는 필요한 world state 유지 + 허용 축만 reset
7. 매 Cut Review 후에만 다음 Cut 진행
8. Key Scripture는 정확한 개역한글 text로 presentation derivative 제작
9. explanatory / visual-only 장면은 계획대로 처리
10. collage/batch shortcut 없음

## Blocker

현재 Architecture blocker 없음.
