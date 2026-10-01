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

현재 프로젝트는 **STEP 0 — Architecture Definition** 단계다.

아직 최종 디렉터리 구조와 제작 데이터 모델이 확정되지 않았다.

따라서 다음 원칙을 따른다.

- 미래에 필요할 것이라는 이유만으로 디렉터리와 파일을 대량 생성하지 않는다.
- `architecture_v0_draft.md`의 제안 구조를 확정 구조로 간주하지 않는다.
- STEP 0의 각 설계 영역을 사용자와 검토한 뒤 필요한 구조만 단계적으로 생성한다.
- **STEP 0-1 — Content Model은 CONFIRMED 상태다.**
- **STEP 0-2 — Episode / Cut Model은 CONFIRMED 상태다.**
- **STEP 0-3 — Continuity Model은 CONFIRMED 상태다.**
- 현재 최우선 작업은 **STEP 0-4 — Library Model 정의**다.

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

STEP 0에서 최종 기록 체계를 확정하기 전까지는 필요한 최소 문서만 생성한다.

## 5. 외부 생성 서비스

ChatGPT 이미지 생성, OpenArt, Higgsfield 등은 외부 rendering provider로 취급한다.

특정 provider의 내부 캐릭터, 모델, prompt, 프로젝트 구조를 이 저장소의 원본 데이터로 간주하지 않는다.

Canonical Scene, Character, Location, Object, Continuity 등 장기적으로 유지되어야 하는 정의는 GitHub에 독립적으로 보존하는 방향으로 설계한다.

## 6. 다음 작업

`architecture_v0_draft.md`를 읽고 다음 항목부터 진행한다.

**STEP 0-4 — Library Model**

확정된 Content / Episode-Cut / Continuity Model을 전제로 반복 등장하는 Character, Location, Object, Costume, Environment, Visual Style의 Canonical 자산 식별자와 메타데이터, Episode/Cut에서의 참조 방식을 설계하고 검토한다.
