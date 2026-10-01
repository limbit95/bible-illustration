# AGENTS.md

이 저장소는 **성경 역사 일러스트 제작 및 관리 프로젝트**의 Source of Truth다.

## 1. 작업 시작 전 필수 확인

새로운 채팅이나 새로운 작업을 시작할 때 기억이나 이전 대화 요약만으로 진행하지 않는다.

반드시 다음 순서로 현재 저장소 상태를 확인한다.

1. 이 `AGENTS.md`
2. `architecture_v0_draft.md`
3. 이후 STEP 0에서 확정될 Architecture / Rules / Progress 문서
4. 현재 작업 중인 Episode / Cut 문서

채팅 내용과 저장소 기록이 충돌할 경우 최신 저장소 상태를 우선 확인하고, 중요한 충돌은 임의로 해결하지 말고 사용자에게 알린다.

## 2. 현재 단계

**STEP 0 — Architecture Definition은 완료되었다.**

Content / Episode-Cut / Continuity / Library / Generation Run / Provider Integration / Image-Asset Storage의 7개 설계 영역은 모두 사용자 승인으로 확정되었다.

다만 실제 최종 디렉터리 구조와 정식 문서 분리는 아직 생성하지 않았다.

따라서 다음 원칙을 따른다.

- 미래에 필요할 것이라는 이유만으로 디렉터리와 파일을 대량 생성하지 않는다.
- `architecture_v0_draft.md`에 기록된 STEP 0-1~0-7의 **CONFIRMED 결정은 현재 아키텍처 기준**으로 사용한다.
- 문서 상단의 초기 제안 디렉터리 트리는 그대로 확정 구조로 간주하지 않고, CONFIRMED 결정 전체를 반영해 최종 디렉터리 구조를 별도로 확정한다.
- **STEP 0-1 — Content Model은 CONFIRMED 상태다.**
- **STEP 0-2 — Episode / Cut Model은 CONFIRMED 상태다.**
- **STEP 0-3 — Continuity Model은 CONFIRMED 상태다.**
- **STEP 0-4 — Library Model은 CONFIRMED 상태다.**
- **STEP 0-5 — Generation Run Model은 CONFIRMED 상태다.**
- **STEP 0-6 — Provider Integration Model은 CONFIRMED 상태다.**
- **STEP 0-7 — Image / Asset Storage Policy는 CONFIRMED 상태다.**
- **STEP 0 전체는 COMPLETED 상태다.**
- **Repository Structure v1.0은 CONFIRMED 상태다.**
- 현재 최우선 작업은 **확정 구조를 기준으로 실제 저장소의 최소 필수 구조를 생성하고 루트 `AGENTS.md`를 정식화하는 것**이다.
- `repository_structure_v1_draft.md`는 현재 승인된 Repository Structure v1.0의 기록이며, 이후 정식 `docs/architecture/REPOSITORY_STRUCTURE.md`로 이관할 때까지 기준 문서로 사용한다.

## 3. 저장소 범위

성경 일러스트 프로젝트의 지속적인 소스 작업은 이 `bible-illustration` 저장소에서만 수행한다.

별도의 명시적 요청이 없는 한 다른 GitHub 저장소, 특히 다른 프로젝트 저장소에는 접근하거나 변경하지 않는다.

## 4. 기록 원칙

장기적으로 유지해야 하는 다음 정보는 대화에만 남기지 않는다.

- 아키텍처 결정
- 제작 규칙
- 스토리보드
- Cut 설계
- Continuity 정보
- Provider 연동 규칙
- Prompt / Generation Run 기록
- 승인 / 수정 / 폐기 상태
- 현재 진행 위치

STEP 0은 완료되었다. 다음 작업에서 확정된 아키텍처를 기준으로 최종 기록 체계와 디렉터리 구조를 단계적으로 생성한다.

## 5. 외부 생성 서비스

ChatGPT 이미지 생성, OpenArt, Higgsfield 등은 외부 rendering provider로 취급한다.

특정 provider의 내부 캐릭터, 모델, prompt, 프로젝트 구조를 이 저장소의 원본 데이터로 간주하지 않는다.

Canonical Scene, Character, Location, Object, Continuity 등 장기적으로 유지되어야 하는 정의는 GitHub에 독립적으로 보존하는 방향으로 설계한다.

## 6. 다음 작업

`architecture_v0_draft.md`의 STEP 0-1~0-7 CONFIRMED 결정과 `repository_structure_v1_draft.md`의 Repository Structure v1.0을 읽고 다음 항목부터 진행한다.

**POST STEP 0 — 실제 저장소 구조 생성 및 AGENTS.md 정식화**

확정된 Repository Structure v1.0에 따라 빈 미래 디렉터리를 대량 생성하지 않고 현재 필요한 최소 구조부터 만든다. 이어서 루트 `AGENTS.md`, `README.md`, Architecture 문서를 정식화한 뒤 Rules / Templates / Progress / Decision 체계를 생성하고 Genesis Creation CUT 1–4를 이관한다.
