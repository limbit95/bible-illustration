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

### 2026-10-02 — Git LFS / ignore 정책 확정

- `DEC-0001-git-lfs-and-ignore-policy.md` accepted
- Canonical `content/**/assets/` 및 `library/**/assets/` 이미지에만 Git LFS 적용
- 일반 이미지 binary 기본 ignore
- local working/cache/export 및 credential 제외 규칙 적용
- 루트 `.gitattributes` / `.gitignore` 생성

### 2026-10-02 — Genesis Creation C01–C04 Canonical definitions 승인

- `GEN-CREATION-01` Canonical definition migration audit 25/25 통과
- C01–C04 `approved / revision 1` 전환
- Episode는 C05 이후 정의가 미완료이므로 `draft / revision 1` 유지
- Asset migration은 별도 잔여 작업으로 유지

### 2026-10-02 — Genesis Creation C01–C04 migration 완료

- Canonical definition migration 완료
- migration audit 25/25 통과
- C01–C04 `approved / revision 1`
- legacy C02–C04 binary는 미확보 상태를 historical provenance로 보존
- DEC-0003에 따라 legacy binary 복구 대신 새 Canonical 기준 재생성으로 전환
- 이후 작업은 migration이 아니라 C01–C04 Canonical Production Validation으로 진행

### 2026-10-02 — Genesis Creation C01–C04 Production Validation 완료

- C01 deterministic pure-black Production Master Git LFS ingest 및 pointer 검증 완료
- C01 representative Asset 선정 — revision 1
- C02–C04 Git LFS ingest / checksum 검증 / representative 선정 완료
- C02–C04 revision 2 기준 Result re-review accepted
- C01–C04 모두 현재 approved revision 기준 production complete
- 실제 제작 feedback이 기존 Architecture / Rules / Asset model 안에서 처리 가능함을 검증
- 다음 제작 대상은 C05 — Genesis 1:6–8

### 2026-10-02 — Genesis Creation rapid full-chapter prototype workflow 확정

- DEC-0004 accepted
- 사용자 선택 00→06 working reference에서 인접 Cut의 단계적 연결을 핵심 제작 기준으로 확정
- retain / delta / forbidden leap 원칙을 Continuity Rules에 반영
- 이전 Cut을 continuity anchor로 사용하는 생성 규칙 반영
- 개별 Cut production complete보다 창세기 1장 전체 1차 완주를 먼저 수행
- Asset promotion / Git LFS ingest는 전체 시퀀스 선별 이후로 연기
- 본문·내레이션 합성본은 Presentation derivative로 별도 테스트

### 2026-10-03 — Genesis Creation visual / presentation reference 확정

- 사용자 최종 선택 00→06 working reference sequence의 단계별 연결 흐름 문서화
- 개별 이미지 복제보다 인접 장면의 retain + delta 연출을 우선하는 기준 재확인
- widescreen, blue-black → cool white → warm gold-white, 원초적 유체 질감을 **Genesis Creation 전용 profile**로 한정
- 해당 palette / texture / lighting progression을 성경 전체 Visual Style로 일반화하지 않도록 규칙화
- Presentation text는 작고 절제된 cinematic sans-serif reference로 확정
- Key Scripture Frame은 직접 인용 우선, narration은 설명 / 전환 Frame에서만 사용하는 원칙 확정
- Production Master와 presentation derivative 분리 원칙 유지
- 정확한 working visual binary는 아직 Canonical Asset이 아니므로 새 채팅에서 필요 시 재첨부

## 다음 Milestone 후보

다음 항목은 실제 완료될 때만 이 문서에 추가한다.

- Genesis Creation CUT 5 제작 재개
- 특정 Episode production complete
