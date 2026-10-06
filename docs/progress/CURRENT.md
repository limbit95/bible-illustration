# Current Project State

> 마지막 갱신: 2026-10-06
> 상태: **ARCHITECTURE REFINEMENT — STORYBOARD RULES / PRIOR GENESIS PRODUCTION RETIRED**

## 현재 Phase

- STEP 0 — Architecture Definition: **COMPLETED**
- STEP 0-1 ~ STEP 0-7: **ALL CONFIRMED**
- Repository Structure v1.0: **CONFIRMED**
- Production Rules v1.0: **CONFIRMED**
- Templates v1.0: **CONFIRMED**
- Progress / Decision 기록 체계 v1.0: **CONFIRMED**
- Git LFS / ignore 정책: **CONFIRMED — DEC-0001 accepted**
- DEC-0005: **ACCEPTED — Scripture Work Unit / sequential generation / flexible Cut decomposition**
- DEC-0006: **ACCEPTED — Scene Relation / Continuity vs Transition**
- 현재 작업: **Storyboard Production Rules 강화**

## Genesis Production Reset

이전 Genesis Creation production/test iteration은 현재 Canonical production tree에서 폐기했다.

현재 tree에서 제거 대상:

- 이전 Genesis Episode / Storyboard / Continuity 데이터
- 이전 C01–C06 Cut 정의
- 이전 Generation Run / Result Review 기록
- 이전 Asset metadata / representative selection
- 이전 Git LFS image pointer
- 이전 Genesis 제작 전용 migration / production-validation / rapid-prototype 문서
- 이전 이미지에 종속된 팔레트 / 광원 / 카메라 / 구도 / 질감 / progression 등 production-specific 규칙

보존 대상:

- Repository / Architecture / Rules / Templates의 구조 자체
- Git history
- 이전 실험에서 일반화되어 현재 Architecture / Rules / accepted Decision으로 승격된 원칙

과거 production 상세를 현재 tree 안에 별도 archive로 복제하지 않는다. 필요 시 Git history로 확인한다.

## 현재 Production 위치

- current_episode: **none**
- current_cut: **none**
- active storyboard: **none**
- active continuity: **none**
- active Genesis production assets: **none**

Genesis Creation을 다시 시작할 경우 이전 production data나 이미지를 기준으로 복원하지 않는다.

Architecture refinement 완료 후 Scripture Work Unit에서 Storyboard를 새로 설계하고, 기존 ID 불변 조건을 지키면서 새 production identity를 결정한다.

## 현재 Source of Truth

- 작업 진입점: `AGENTS.md`
- 현재 진행 복원: `docs/progress/CURRENT.md`
- Architecture 진입점: `docs/architecture/OVERVIEW.md`
- 세부 Architecture: `docs/architecture/*.md`
- Production Rules: `docs/rules/*.md`
- Templates: `templates/*.yaml`
- Architecture refinement handoff: `docs/progress/ARCHITECTURE_REFINEMENT_HANDOFF.md`
- Milestone 이력: `docs/progress/MILESTONES.md`
- Decision 정책: `docs/decisions/README.md`

## 다음 작업

1. Storyboard Production Rules 강화안 확정
2. Scripture coverage 검증 규칙 명문화
3. Cut boundary / beat granularity 규칙 명문화
4. Storyboard 단계 Scene Relation planning 규칙 명문화
5. visual repetition prevention / Episode-level rhythm 규칙 명문화
6. Key Scripture / explanatory frame 판단 책임 정리
7. Storyboard / Cut / Continuity 책임 중복 감사
8. `templates/storyboard.yaml` 변경 필요 여부 최종 판단
9. 사용자 승인 후 새로운 Genesis production 시작 여부 결정

## Blocker

현재 production blocker 없음.

이미지 생성은 Architecture refinement가 끝날 때까지 재개하지 않는다.
