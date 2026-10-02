# AGENTS.md

이 저장소는 **성경 역사 일러스트 제작 및 관리 프로젝트**의 Source of Truth다.

## 1. 작업 시작 전 필수 확인

새로운 채팅이나 작업을 시작할 때 기억이나 이전 대화 요약만으로 진행하지 않는다.

반드시 현재 저장소를 먼저 읽고 다음 순서로 상태를 복원한다.

1. 이 `AGENTS.md`
2. `docs/progress/CURRENT.md`
3. `docs/architecture/OVERVIEW.md`
4. 필요한 세부 Architecture / Rules 문서
5. 현재 작업 중인 Episode / Cut / Library / Integration 문서

Repository 물리 구조가 필요한 작업은 `docs/architecture/REPOSITORY_STRUCTURE.md`도 함께 확인한다.

채팅 내용, 기억, 과거 요약과 저장소 기록이 충돌할 경우 최신 저장소 상태를 우선한다. 중요한 충돌은 임의로 해결하지 말고 사용자에게 알린다.

## 2. 확정된 기준

다음은 현재 확정 상태다.

- STEP 0 — Architecture Definition: **COMPLETED**
- STEP 0-1 — Content Model: **CONFIRMED**
- STEP 0-2 — Episode / Cut Model: **CONFIRMED**
- STEP 0-3 — Continuity Model: **CONFIRMED**
- STEP 0-4 — Library Model: **CONFIRMED**
- STEP 0-5 — Generation Run Model: **CONFIRMED**
- STEP 0-6 — Provider Integration Model: **CONFIRMED**
- STEP 0-7 — Image / Asset Storage Policy: **CONFIRMED**
- Repository Structure v1.0: **CONFIRMED**
- Production Rules v1.0: **CONFIRMED**
- Templates v1.0: **CONFIRMED**
- Progress / Decision 기록 체계 v1.0: **CONFIRMED**
- DEC-0001 — Git LFS and Ignore Policy: **ACCEPTED**
- DEC-0002 — Presentation-only Cut Output Mode: **REJECTED** — CUT 1은 기존 illustration/Asset 모델 유지

확정된 Repository Structure는 `docs/architecture/REPOSITORY_STRUCTURE.md`를 따른다.

## 3. 현재 단계

현재는 **POST STEP 0 — Rules / Templates / Migration Preparation** 단계다.

진행 위치와 다음 작업은 `docs/progress/CURRENT.md`가 Source of Truth다.

현재 원칙:

- 빈 미래 디렉터리와 파일을 필요 이상으로 대량 생성하지 않는다.
- 실제 데이터나 정식 문서가 필요한 시점에만 구조를 생성한다.
- Genesis Creation CUT 1–4 Canonical definition은 새 구조로 이관되어 audit를 통과했고, 사용자 승인으로 `approved / revision 1` 상태다.
- migration이 완료되기 전에는 CUT 5 제작을 재개하지 않는다.

## 4. 저장소 범위

성경 일러스트 프로젝트의 지속적인 소스 작업은 **`limbit95/bible-illustration` 저장소에서만** 수행한다.

별도의 명시적 요청이 없는 한 다른 GitHub 저장소, 특히 다른 프로젝트 저장소에는 접근하거나 변경하지 않는다.

## 5. Source of Truth 책임

### Canonical Production Data

- Episode / Storyboard / Cut
- Continuity
- Library
- Generation Run / Result Review metadata
- Provider Integration metadata
- Asset metadata / selection

은 확정된 Repository Structure에 따라 GitHub에 기록한다.

### Image Binary

장기 보존 대상으로 승격된 production/reference 이미지 binary는 Git LFS를 Canonical 저장 방식으로 사용한다.

모든 Provider output binary를 자동으로 장기 보존하지 않는다.

### External Provider

ChatGPT 이미지 생성, OpenArt, Higgsfield 등은 rendering provider다.

Provider 내부의 character, model, prompt, slot, project structure는 Canonical Source of Truth가 아니다.

## 6. 기록 원칙

다음 정보는 대화에만 남기지 않는다.

- 아키텍처 결정
- 제작 규칙
- 진행 상태
- Storyboard / Cut 정의
- Continuity
- Library 정의
- Provider binding / profile
- Prompt / Generation Run 기록
- Result Review
- Asset selection / promotion
- 승인 / 수정 / 폐기 상태

현재 진행 위치가 바뀌면 `docs/progress/CURRENT.md`를 함께 갱신한다.

## 7. 구조 변경 원칙

- ID가 identity이며 파일 경로는 identity가 아니다.
- 기존 파일을 확인하지 않고 덮어쓰지 않는다.
- 구조 변경 전 관련 Architecture / Rules / CURRENT 문서를 확인한다.
- Canonical 정의와 Provider-specific 파생 데이터를 섞지 않는다.
- Cut 순서는 Storyboard가 유일한 Source of Truth다.
- 별도 전역 prompts 디렉터리를 만들지 않는다.
- STORY_KEY를 독립 Story Arc entity로 승격하지 않는다.
- Asset은 Cut 또는 Library primary owner 아래 등록한다.
- 의미 있는 이미지 binary 변경은 기존 Asset을 overwrite하지 않고 새 Asset ID를 발급한다.

## 8. Architecture Source of Truth

현재 Architecture의 정식 Source of Truth는 `docs/architecture/` 아래 문서다.

- `OVERVIEW.md`
- `CONTENT_MODEL.md`
- `EPISODE_CUT_MODEL.md`
- `CONTINUITY_MODEL.md`
- `LIBRARY_MODEL.md`
- `GENERATION_RUN_MODEL.md`
- `PROVIDER_INTEGRATION_MODEL.md`
- `ASSET_STORAGE_POLICY.md`
- `REPOSITORY_STRUCTURE.md`

다음 루트 문서는 설계 과정의 historical working record로 보존한다.

- `architecture_v0_draft.md`
- `repository_structure_v1_draft.md`

현재 규칙을 판단할 때 historical working record보다 정식 Architecture 문서를 우선한다.

## 9. Rules Source of Truth

현재 제작 규칙의 정식 Source of Truth는 `docs/rules/` 아래 문서다.

- `MASTER_RULES.md`
- `SCRIPTURE_RULES.md`
- `HISTORICAL_RULES.md`
- `VISUAL_RULES.md`
- `CONTINUITY_RULES.md`
- `GENERATION_RULES.md`
- `TEXT_AND_COPYRIGHT.md`

과거 테스트 기준서나 채팅 인수인계 자료보다 정식 Rules 문서를 우선한다.

## 10. Templates Source of Truth

현재 반복 제작 데이터의 정식 Template Source of Truth는 `templates/` 아래 승인된 YAML 11개다.

Template은 Architecture와 Rules를 구현하기 위한 최소 물리 스키마다. 새로운 필드를 추가하거나 기존 책임을 바꾸기 전에 관련 Architecture / Rules를 먼저 확인한다.

## 11. Progress / Decision Source of Truth

- `docs/progress/CURRENT.md` — 현재 작업 위치와 다음 작업
- `docs/progress/MILESTONES.md` — 주요 완료 기준점
- `docs/decisions/README.md` — Decision Record 생성·상태·번호 규칙
- `docs/decisions/DEC-*.md` — 중요한 선택의 이유와 대안 기록

CURRENT / MILESTONES / Decision의 책임을 장문으로 중복하지 않는다.

## 12. Git LFS / Ignore 운영 원칙

- Canonical image binary만 `content/**/assets/` 또는 `library/**/assets/` 아래 등록한다.
- 해당 Canonical Asset binary는 `.gitattributes`에 따라 Git LFS로 추적한다.
- 일반 이미지 binary는 `.gitignore`로 기본 제외한다.
- Asset Promotion 없이 Provider Result를 `assets/`에 넣지 않는다.
- credential과 local working/cache/export 파일은 Git에 저장하지 않는다.
- 세부 근거는 `docs/decisions/DEC-0001-git-lfs-and-ignore-policy.md`를 따른다.

## 13. 다음 작업

`docs/progress/CURRENT.md`를 기준으로 이어서 진행한다.

현재 다음 작업은:

**Genesis Creation Asset migration → migration 최종 감사**

이다.
