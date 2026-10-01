# Project Milestones

> 상태: **CONFIRMED / 2026-10-02 사용자 승인**
>
> 목적: Bible Illustration 프로젝트에서 장기적으로 의미가 있는 완료 기준점을 간결하게 기록한다.
>
> 현재 작업 위치는 `CURRENT.md`가 Source of Truth이며, 이 문서는 과거의 주요 완료 이력을 보존한다.

## 기록 원칙

1. 모든 커밋이나 세부 작업을 기록하지 않는다.
2. 이후 작업의 기준점이 되는 완료 사건만 기록한다.
3. 이미 기록된 milestone의 의미를 나중에 조용히 바꾸지 않는다.
4. 잘못된 기록을 수정해야 하면 변경 이유가 Git history에 남아야 한다.
5. 진행 중 상태는 이 문서가 아니라 `CURRENT.md`에 기록한다.
6. 세부 설계 근거는 Architecture / Rules / Decision 문서에 둔다.

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

- 최종 논리 디렉터리 구조 CONFIRMED
- `docs / content / library / integrations / templates` 책임 분리
- Story Arc 중간 entity 미사용
- Canonical `prompt.md` 미사용
- 빈 미래 디렉터리 대량 생성 금지

### 2026-10-02 — 정식 Architecture 이관 완료

- `docs/architecture/` 정식 문서 생성
- STEP 0-1~0-7 CONFIRMED 본문 이관
- 원본 Working Record와 exact-match 검증 통과
- 현재 Architecture Source of Truth를 정식 문서로 전환

### 2026-10-02 — Production Rules v1.0 확정

- Master / Scripture / Historical / Visual / Continuity / Generation / Text & Copyright Rules 확정
- legacy 8-cut fixed rule을 전역 규칙으로 계승하지 않음
- Architecture와 제작 Rules의 책임 경계 확정

### 2026-10-02 — Templates v1.0 확정

- 핵심 YAML Template 11개 확정
- Architecture 필수 필드와 enum 교차 검증 통과
- 신규 Episode / Cut / Run / Asset / Library / Provider 설정의 반복 생성 기준 마련

### 2026-10-02 — Progress / Decision 기록 체계 v1.0 확정

- `CURRENT.md` = 현재 작업 위치 복원
- `MILESTONES.md` = 주요 완료 기준점 이력
- `docs/decisions/DEC-*.md` = 중요한 선택의 이유·대안·영향 기록
- STEP 0 세부 결정을 불필요하게 소급 중복 기록하지 않는 원칙 확정

## 다음 Milestone 후보

다음 항목은 실제 완료될 때만 이 문서에 추가한다.

- Git LFS / ignore 정책 확정
- Genesis Creation CUT 1–4 migration 완료
- Genesis Creation migration 감사 완료
- Genesis Creation CUT 5 제작 재개
- 특정 Episode production complete
