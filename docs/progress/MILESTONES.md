# Project Milestones

> 상태: **CONFIRMED**
>
> 목적: Bible Illustration 프로젝트에서 장기적으로 의미가 있는 완료 기준점을 간결하게 기록한다.
>
> 현재 작업 위치는 `CURRENT.md`가 Source of Truth이며, 이 문서는 주요 완료 이력만 보존한다.

## 기록 원칙

1. 모든 커밋이나 세부 작업을 기록하지 않는다.
2. 이후 작업의 기준점이 되는 완료 사건만 기록한다.
3. 이미 기록된 milestone의 의미를 나중에 조용히 바꾸지 않는다.
4. 진행 중 상태는 이 문서가 아니라 `CURRENT.md`에 기록한다.
5. 세부 설계 근거는 Architecture / Rules / Decision 문서에 둔다.
6. retired production iteration의 상세 산출물은 이 문서에 복제하지 않고 Git history로 보존한다.

## Milestones

### 2026-10-02 — STEP 0 Architecture Definition 완료

- STEP 0-1 Content Model: CONFIRMED
- STEP 0-2 Episode / Cut Model: CONFIRMED
- STEP 0-3 Continuity Model: CONFIRMED
- STEP 0-4 Library Model: CONFIRMED
- STEP 0-5 Generation Run Model: CONFIRMED
- STEP 0-6 Provider Integration Model: CONFIRMED
- STEP 0-7 Image / Asset Storage Policy: CONFIRMED

결과:

- 프로젝트의 Canonical production data 경계 확정
- Provider와 Canonical 정의 분리
- Generated Result와 Asset 분리
- 장기 이미지 보존 정책 확정

### 2026-10-02 — Repository Structure v1.0 확정

- `docs / content / library / integrations / templates` 책임 분리
- Storyboard / Cut / Continuity 책임 분리
- Canonical `prompt.md` 미사용
- 빈 미래 디렉터리 대량 생성 금지

### 2026-10-02 — 정식 Architecture / Rules / Templates 체계 확정

- `docs/architecture/` 정식 문서 생성
- Production Rules v1.0 확정
- 핵심 YAML Template 11개 확정
- Progress / Decision 기록 체계 v1.0 확정
- Git LFS / ignore 정책 확정

### 2026-10-03 — Scripture-unit sequential production workflow 확정

- DEC-0005 accepted
- Scripture Work Unit 기반 작업
- 한 구절 = 한 이미지 고정 금지
- 필요 시 1:N Cut 분해 / N:1 Cut 통합
- 다중 장면이면 생성 전 Storyboard 제안
- 실제 이미지는 기본적으로 한 장면씩 순차 생성
- batch generation은 기본값으로 사용하지 않음

### 2026-10-06 — Scene Relation continuity / transition policy 확정

- DEC-0006 accepted
- 동일 사건의 단계적 변화는 Continuity-oriented relation 우선
- 새로운 사건 / 핵심 visual subject는 Transition-oriented relation 허용
- Transition에서도 Scripture와 Story chronology 유지
- Canonical 저장은 기존 `continue / partial_reset / reset` 사용
- 새 relation enum을 추가하지 않음

### 2026-10-06 — 이전 Genesis production iteration retired

- 이전 Genesis production/test iteration의 active production data를 현재 tree에서 제거
- Episode / Storyboard / Cut / Continuity / Run / Asset / representative selection 제거
- 현재 tree의 Git LFS image pointer 제거
- production-specific progress/decision 자료 제거
- 장면별 시각 micro-rule을 현재 Rules에서 제거
- Git commit history는 유지
- 일반화되어 Architecture / Rules / DEC-0005 / DEC-0006에 승격된 원칙은 유지
- 다음 Genesis production은 Architecture refinement 완료 후 Scripture부터 새로 설계

### 2026-10-06 — Storyboard Production Rules refinement 완료

- DEC-0007 accepted
- Scripture coverage / beat granularity 규칙 강화
- Storyboard 단계 Scene Relation planning 명문화
- visual repetition prevention / Episode-level rhythm review 추가
- Key Scripture / explanatory frame의 Storyboard 책임 경계 정리
- Storyboard / Cut / Continuity Source of Truth 경계 재확인
- `templates/storyboard.yaml` schema 변경 없음
- Architecture Completion Audit 통과
- Architecture blocker 없이 새 production 시작 가능한 상태 확정

### 2026-10-06 — Pre-Production Architecture Hardening 완료

- DEC-0008 accepted — Library Entity / Image Asset identity 분리
- DEC-0009 accepted — downstream actual reference 전 Asset Promotion
- DEC-0010 accepted — retired identity tombstone registry
- DEC-0011 accepted — Episode visual direction owner 분해
- Repository Structure v1.1
- Templates v1.1 / 11개 유지
- Continuity reset schema 정합성 확보
- Storyboard transition_note incoming 방향 확정
- 이전 Genesis production identity 36개 tombstone 등록
- ChatGPT 최소 Integration 구성
- synthetic end-to-end production lifecycle dry-run PASS
- Pre-Production Hardening Audit PASS
- active Genesis production 없이 새 제작 준비 완료

## 다음 Milestone 후보

다음 항목은 실제 완료될 때만 추가한다.

- Storyboard Production Rules 강화 완료
- 새로운 Episode production 시작
- 특정 Episode production complete
