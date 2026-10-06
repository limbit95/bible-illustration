# Current Project State

> 마지막 갱신: 2026-10-06
> 상태: **PRE-PRODUCTION ARCHITECTURE HARDENING COMPLETE / READY FOR NEW PRODUCTION**

## 현재 Phase

- STEP 0 — Architecture Definition: **COMPLETED**
- STEP 0-1 ~ STEP 0-7: **ALL CONFIRMED / CURRENT-TENSE HARDENED**
- Repository Structure v1.1: **CONFIRMED**
- Production Rules hardening: **COMPLETED**
- Storyboard Production Rules refinement: **COMPLETED**
- Templates v1.1: **CONFIRMED — 11 YAML templates**
- Progress / Decision 기록 체계: **CONFIRMED**
- Git LFS / ignore 정책: **CONFIRMED — DEC-0001**
- DEC-0005 — Scripture Work Unit / sequential generation: **ACCEPTED**
- DEC-0006 — Scene Relation continuity vs transition: **ACCEPTED**
- DEC-0007 — Storyboard Rules without schema expansion: **ACCEPTED**
- DEC-0008 — Library Entity vs Image Asset identity: **ACCEPTED**
- DEC-0009 — downstream reference requires prior Asset Promotion: **ACCEPTED**
- DEC-0010 — retired identity tombstone registry: **ACCEPTED**
- DEC-0011 — Episode visual direction ownership: **ACCEPTED**
- Synthetic lifecycle dry-run: **PASSED**
- Pre-Production Hardening Audit: **PASSED**

## Hardening 완료 내용

- stale STEP 0 handoff / unresolved 문구 정리
- Continuity `reset: []` 물리 schema 정합성 확보
- accepted / working Result → downstream actual reference lifecycle 확정
- Library Entity `library_id`와 Image Asset `asset_id` 분리
- Episode visual direction의 Canonical owner 정리
- retired production identity tombstone registry 추가
- Storyboard `transition_note` 방향을 previous active Cut → current Cut으로 확정
- Result ↔ Asset provenance를 Asset metadata `source.result_id`로 단일화
- removed Asset metadata tombstone 구조 추가
- ChatGPT 최소 Provider / Profile / Prompt Adapter 구성
- synthetic 2-Cut end-to-end dry-run PASS

## Genesis Production Reset

이전 Genesis production/test iteration은 current tree에서 retired 상태다.

- active 이전 Episode / Storyboard / Continuity: 없음
- active 이전 C01–C06 Cut / Run / Asset: 없음
- 이전 Git LFS image pointer: 없음
- active Genesis production data: 없음
- retired identity 36개: `content/identity-tombstones.yaml`

상세 과거 production은 Git history에서만 추적한다.

**기존 `GEN-CREATION-01`과 C01–C06 계열 ID를 새 production에 재사용하지 않는다.**

## 현재 Production 위치

- current_episode: **none**
- current_cut: **none**
- active_storyboard: **none**
- active_continuity: **none**
- active_genesis_assets: **none**

## 현재 Source of Truth

- 작업 진입점: `AGENTS.md`
- 현재 진행 복원: `docs/progress/CURRENT.md`
- Architecture: `docs/architecture/*.md`
- Production Rules: `docs/rules/*.md`
- Templates: `templates/*.yaml`
- retired identity: `content/identity-tombstones.yaml`
- latest production readiness audit: `docs/progress/PREPRODUCTION_HARDENING_AUDIT.md`
- dry-run evidence: `docs/progress/PREPRODUCTION_HARDENING_DRY_RUN.md`
- Milestones: `docs/progress/MILESTONES.md`
- Decisions: `docs/decisions/DEC-*.md`
- ChatGPT Integration: `integrations/chatgpt/`

## 다음 작업

다음 단계는 **새 Genesis production 설계**다.

1. 사용자가 제시하는 개역한글 Scripture Work Unit 확인
2. current tree + identity tombstone에서 ID 사용 이력 확인
3. 새 Episode identity 발급
4. 새 Storyboard 설계
5. Multi-Cut이면 Storyboard Production Preflight
6. Cut / Continuity 신규 정의
7. 필요한 Library Entity / Historical Reference 준비
8. 사용자 검토
9. 이후 한 장면씩 Generation
10. downstream actual reference가 되는 working/accepted Result는 다음 Run 전에 Asset Promotion

## Blocker

현재 Architecture blocker 없음.

이미지 생성은 사용자가 새 production 시작을 지시하기 전까지 진행하지 않는다.
