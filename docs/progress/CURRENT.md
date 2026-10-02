# Current Project State

> 마지막 갱신: 2026-10-02
> 상태: **POST STEP 0 — Rules / Templates / Migration Preparation**

## 현재 Phase

- STEP 0 — Architecture Definition: **COMPLETED**
- STEP 0-1 ~ STEP 0-7: **ALL CONFIRMED**
- Repository Structure v1.0: **CONFIRMED**
- 최소 필수 저장소 구조 생성: **COMPLETED**
- 루트 `AGENTS.md` 정식화: **COMPLETED**
- 루트 `README.md` 생성: **COMPLETED**
- 정식 Architecture 문서 분리: **COMPLETED**
- Architecture 이관 검증: **PASSED — STEP 0-1~0-7 본문 exact match**
- Production Rules v1.0: **CONFIRMED**
- Templates v1.0: **CONFIRMED**
- Template 필수 필드 교차 검증: **PASSED**
- Progress / Decision 기록 체계 v1.0: **CONFIRMED**
- Git LFS / ignore 정책: **CONFIRMED — DEC-0001 accepted**
- `.gitattributes` / `.gitignore`: **IMPLEMENTED**
- Genesis Creation CUT 1–4 Canonical definition migration: **COMPLETED**
- Genesis Creation definition migration audit: **PASSED — 25/25**
- DEC-0002 Presentation-only Cut Output Mode: **REJECTED — existing Asset model retained**
- CUT 1 handling: **actual black-screen illustration Asset**
- C02–C04 legacy image binary: **NOT LOCATED**
- C01 Asset metadata: **pending_ingest — Git LFS binary not yet available**
- C02–C04 legacy image binary: **NOT LOCATED**
- C01–C04 Canonical definition: **APPROVED / revision 1**
- 현재 작업: **Genesis Creation Asset migration 방침 정리**

## 현재 Production 위치

새 Canonical 구조에 Genesis Creation C01–C04 definition을 이관했다.

- current_episode: GEN-CREATION-01
- current_cut: GEN-CREATION-01-C04 (migration review boundary)
- Genesis Creation CUT 1–4 definition migration: **COMPLETED / AUDIT PASSED**
- CUT 5 production: **PAUSED UNTIL MIGRATION**

## 마지막 완료 항목

1. STEP 0-1 — Content Model 확정
2. STEP 0-2 — Episode / Cut Model 확정
3. STEP 0-3 — Continuity Model 확정
4. STEP 0-4 — Library Model 확정
5. STEP 0-5 — Generation Run Model 확정
6. STEP 0-6 — Provider Integration Model 확정
7. STEP 0-7 — Image / Asset Storage Policy 확정
8. Repository Structure v1.0 확정
9. `docs/architecture/REPOSITORY_STRUCTURE.md` 정식 생성
10. `docs/progress/CURRENT.md` 진행 복원 체계 시작
11. `content/episode-sequence.yaml` 초기화
12. `integrations/registry.yaml` 초기화
13. 루트 `AGENTS.md` 정식화
14. 루트 `README.md` 생성
15. `docs/architecture/OVERVIEW.md` 생성
16. STEP 0-1~0-7 정식 Architecture 문서 분리
17. Architecture 이관 검증 통과
18. `docs/rules/` 7개 정식화 초안 생성
19. 과거 8컷 고정 규칙을 현재 Architecture와 충돌하는 legacy rule로 분리
20. Rules 상호 충돌 및 Architecture 경계 자체 점검 완료
21. Production Rules v1.0 사용자 승인 및 7개 문서 CONFIRMED 전환
22. `templates/` 핵심 YAML 템플릿 11개 생성
23. Architecture 기준 필수 필드 및 enum 교차 검증 통과
24. Template 단계 물리 스키마 구체화: `cut.library_refs`, `generation-run.integration`, `asset-selection.cut_id`
25. Templates v1.0 사용자 승인 및 11개 YAML CONFIRMED 전환
26. `docs/progress/MILESTONES.md` 생성
27. `docs/decisions/README.md` Decision Record 정책 생성
28. CURRENT / MILESTONES / Decision 책임 분리 및 자체 점검 완료
29. Progress / Decision 기록 체계 v1.0 사용자 승인 및 CONFIRMED 전환
30. `DEC-0001-git-lfs-and-ignore-policy.md` 제안 생성
31. Canonical Asset 경로 기반 LFS 추적 + 일반 이미지 기본 ignore 정책 자체 점검 완료
32. DEC-0001 사용자 승인 및 accepted 전환
33. 루트 `.gitattributes` / `.gitignore` 생성
34. Genesis Creation CUT 1–4 historical source 복원
35. `GENESIS_CREATION_MIGRATION.md` readiness 문서 생성
36. DEC-0002 Presentation-only Cut Output Mode 제안 생성
37. C02–C04 legacy image binary 현재 source에서 미확인
38. DEC-0002 제안 rejected — presentation-only mode 미도입
39. CUT 1을 실제 black-screen Cut-owned Asset으로 관리하는 방향 확정
40. GEN-CREATION-01 Episode / Storyboard / Continuity 생성
41. C01–C04 Canonical cut.yaml 생성
42. Canonical Episode Sequence에 GEN-CREATION-01 등록
43. C01 Asset metadata 생성 — pending_ingest
44. Genesis Creation definition migration audit 25/25 통과
45. C01–C04 Canonical definition 사용자 승인 및 approved 전환

## 현재 Source of Truth

- 작업 진입점: `AGENTS.md`
- 현재 진행 복원: `docs/progress/CURRENT.md`
- 확정 Repository Structure: `docs/architecture/REPOSITORY_STRUCTURE.md`
- Architecture 진입점: `docs/architecture/OVERVIEW.md`
- 세부 Architecture: `docs/architecture/*.md`
- Milestone 이력: `docs/progress/MILESTONES.md`
- Decision 정책/인덱스: `docs/decisions/README.md`
- Historical working record: `architecture_v0_draft.md`, `repository_structure_v1_draft.md`

현재 Architecture 판단에서는 정식 `docs/architecture/` 문서를 historical working record보다 우선한다.

## 관련 결정 / 문서

- `docs/progress/MILESTONES.md` — 주요 완료 기준점
- `docs/decisions/README.md` — Decision Record 생성·상태·번호 규칙
- `DEC-0001-git-lfs-and-ignore-policy.md`: **accepted**
- `DEC-0002-presentation-only-cut-output-mode.md`: **rejected — 기존 illustration/Asset 모델 유지**
- `docs/progress/GENESIS_CREATION_MIGRATION.md`: migration readiness / blockers

## 다음 작업

1. CUT 1 black-screen Git LFS binary ingest 및 representative 등록
2. C02–C04 legacy image binary 복구 또는 새 Canonical 기준 재생성 방침 결정
3. Asset migration / representative Review
4. migration 최종 감사 및 완료 처리
5. CUT 5 제작 재개

## Blocker

현재 blocker:

1. C01 black-screen PNG의 Git LFS object ingest를 현재 GitHub connector로 수행할 수 없음
2. C02–C04의 legacy 확정 이미지 binary를 현재 저장소/Project Library에서 찾지 못함

두 blocker 모두 Canonical definition audit에는 영향을 주지 않으며 Asset migration / production complete에만 영향을 준다.
