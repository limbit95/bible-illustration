# Architecture Refinement Handoff — Storyboard Production Rules

> 상태: **COMPLETED / 2026-10-06**
>
> 목적: Genesis production 테스트에서 발견된 Storyboard 설계 문제를 Architecture / Rules에 환류하기 위한 작업 기록이다.

## 완료 결과

Storyboard Production Rules refinement는 완료되었다.

정식 반영 범위:

- Scripture coverage
- Cut boundary / beat granularity
- Scene Relation planning
- visual repetition prevention
- Key Scripture / explanatory frame 책임 경계
- Episode-level rhythm
- Storyboard / Cut / Continuity 책임 분리
- generation entry preflight

## Schema 결정

Storyboard schema는 확장하지 않는다.

`templates/storyboard.yaml`은 다음 필드를 유지한다.

- order
- cut_id
- scripture_anchor
- beat
- transition_note

상세 장면은 Cut, 실제 continuity constraint는 Continuity가 소유한다.

근거 Decision:

- `docs/decisions/DEC-0007-storyboard-production-rules-without-schema-expansion.md`

## Production 상태

이전 Genesis production iteration은 current tree에서 retired 상태다.

새 production은 이전 이미지나 Cut 정의에서 복원하지 않는다.

## 후속 작업

Architecture refinement 후속 상태는 다음 문서를 기준으로 한다.

- `docs/progress/CURRENT.md`
- `docs/progress/ARCHITECTURE_COMPLETION_AUDIT.md`

이 문서는 더 이상 active handoff가 아니다.
