# Bible Illustration

성경의 내용을 역사적 흐름과 본문 맥락에 따라 고품질 일러스트로 제작하고, 장기적으로 일관된 인물·장소·사물·환경·연속성을 관리하기 위한 프로젝트다.

이 저장소는 단순 이미지 보관소가 아니라 **성경 일러스트 제작 시스템의 Source of Truth**다.

## 핵심 원칙

- 성경 본문과 제작 Episode 구조를 분리한다.
- Cut은 장면의 Canonical 정의이며 생성된 이미지와 동일하지 않다.
- 반복 인물·장소·사물·의복·환경·시각 스타일은 Library에서 관리한다.
- Cut 사이 연속성은 Continuity 데이터로 명시한다.
- ChatGPT, OpenArt, Higgsfield 등은 교체 가능한 rendering provider로 취급한다.
- 실제 생성 시도는 Generation Run으로 기록한다.
- 장기 보존할 이미지에만 Asset ID를 부여하고 Git LFS로 관리한다.
- 사이트용 성경 본문·내레이션·UI text는 기본적으로 이미지 binary와 분리한다.

## 프로젝트 구조

~~~text
docs/          Architecture / Rules / Decisions / Progress
content/       Episode / Storyboard / Cut / Continuity / Run
library/       Character / Location / Object / Costume / Environment / Visual Style
integrations/  Provider Registry / Binding / Generation Profile / Prompt Adapter
templates/     반복 제작 데이터 템플릿
~~~

전체 구조는 `docs/architecture/REPOSITORY_STRUCTURE.md`를 따른다.

## 문서 읽기 순서

작업을 이어갈 때는 다음 순서가 기준이다.

1. `AGENTS.md`
2. `docs/progress/CURRENT.md`
3. `docs/architecture/OVERVIEW.md`
4. 필요한 세부 Architecture / Rules 문서
5. 현재 Episode / Cut / Library / Integration 데이터

## Architecture

- `docs/architecture/OVERVIEW.md`
- `docs/architecture/CONTENT_MODEL.md`
- `docs/architecture/EPISODE_CUT_MODEL.md`
- `docs/architecture/CONTINUITY_MODEL.md`
- `docs/architecture/LIBRARY_MODEL.md`
- `docs/architecture/GENERATION_RUN_MODEL.md`
- `docs/architecture/PROVIDER_INTEGRATION_MODEL.md`
- `docs/architecture/ASSET_STORAGE_POLICY.md`
- `docs/architecture/REPOSITORY_STRUCTURE.md`

## 현재 상태

STEP 0 — Architecture Definition, Repository Structure v1.0, Production Rules v1.0, Templates v1.0은 완료·확정되었다.

Storyboard Production Rules refinement와 Architecture Completion Audit까지 완료되었다.

현재 Architecture blocker는 없으며 이전 Genesis production/test iteration은 current tree에서 retired 상태다. active Episode / Cut / Asset은 없다.

새 production은 Scripture부터 Storyboard를 새로 설계한 뒤 시작한다.

최신 진행 위치는 반드시 `docs/progress/CURRENT.md`에서 확인한다.

## Image Asset

장기 보존 대상으로 승격된 production/reference 이미지 binary는 Git LFS를 Canonical 저장 방식으로 사용한다.

모든 생성 결과를 Git LFS에 저장하지 않으며, Generated Result와 장기 Asset을 구분한다.

## Historical Records

루트의 다음 문서는 설계 과정의 historical working record다.

- `architecture_v0_draft.md`
- `repository_structure_v1_draft.md`

현재 운영 기준은 정식 `docs/architecture/` 문서를 우선한다.
