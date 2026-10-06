# Repository Structure v1.1

> 상태: **CONFIRMED / 2026-10-06 pre-production hardening**
>
> 기준: `architecture_v0_draft.md`의 STEP 0-1~0-7 CONFIRMED 결정과 Repository Structure 승인 기록
>
> 목적: 확정된 Content / Episode-Cut / Continuity / Library / Generation Run / Provider Integration / Asset Storage 모델을 실제 `bible-illustration` 저장소의 물리 구조로 변환한다.
>
> 이 문서는 STEP 0과 pre-production hardening 결정을 실제 저장소 구조로 변환한 **확정 Repository Structure v1.1**이다.
>
> 논리 구조는 확정되었지만, 빈 미래 디렉터리를 대량 생성하지 않는 원칙에 따라 실제 디렉터리와 파일은 필요한 순서대로 단계적으로 생성한다.

---

## 1. 구조 설계 원칙

최종 저장소 구조는 다음 원칙을 따른다.

1. **Canonical 정의와 생성 결과를 분리한다.**
2. **Provider-independent 데이터와 Provider-specific 데이터를 분리한다.**
3. **장면 정의와 대표 이미지 선택 기록을 분리한다.**
4. **Library 정의와 Cut의 일시 상태를 분리한다.**
5. **Storyboard가 Cut 순서의 유일한 Source of Truth다.**
6. **고해상도 Canonical 이미지 binary는 Git LFS를 사용한다.**
7. **문서 설명은 Markdown, 구조화된 제작 데이터는 YAML을 기본으로 한다.**
8. **미래 사용 가능성만으로 빈 디렉터리와 파일을 대량 생성하지 않는다.**
9. **ID가 identity이며 파일 경로는 identity가 아니다.**
10. **기존 STEP 0 작업 기록은 정식 문서 이관이 끝날 때까지 보존한다.**

---

## 2. 최종 목표 구조

아래 트리는 **논리적 최종 구조**다.

실제 저장소에는 현재 필요한 디렉터리와 파일부터 단계적으로 생성한다.

~~~text
bible-illustration/
├─ AGENTS.md
├─ README.md
├─ .gitattributes
├─ .gitignore
│
├─ docs/
│  ├─ architecture/
│  │  ├─ OVERVIEW.md
│  │  ├─ CONTENT_MODEL.md
│  │  ├─ EPISODE_CUT_MODEL.md
│  │  ├─ CONTINUITY_MODEL.md
│  │  ├─ LIBRARY_MODEL.md
│  │  ├─ GENERATION_RUN_MODEL.md
│  │  ├─ PROVIDER_INTEGRATION_MODEL.md
│  │  ├─ ASSET_STORAGE_POLICY.md
│  │  └─ REPOSITORY_STRUCTURE.md
│  │
│  ├─ rules/
│  │  ├─ MASTER_RULES.md
│  │  ├─ SCRIPTURE_RULES.md
│  │  ├─ HISTORICAL_RULES.md
│  │  ├─ VISUAL_RULES.md
│  │  ├─ CONTINUITY_RULES.md
│  │  ├─ GENERATION_RULES.md
│  │  └─ TEXT_AND_COPYRIGHT.md
│  │
│  ├─ decisions/
│  │  ├─ README.md
│  │  └─ DEC-0001-....md
│  │
│  └─ progress/
│     ├─ CURRENT.md
│     └─ MILESTONES.md
│
├─ content/
│  ├─ episode-sequence.yaml
│  ├─ identity-tombstones.yaml
│  │
│  ├─ old-testament/
│  │  └─ genesis/
│  │     └─ BOOK-STORY-01/
│  │        ├─ episode.yaml
│  │        ├─ storyboard.yaml
│  │        ├─ continuity.yaml
│  │        ├─ references.yaml          # 필요할 때만
│  │        │
│  │        └─ cuts/
│  │           ├─ C01/
│  │           │  ├─ cut.yaml
│  │           │  ├─ asset-selection.yaml   # 대표 Asset이 생길 때
│  │           │  ├─ references.yaml        # 필요할 때만
│  │           │  ├─ runs/
│  │           │  │  └─ BOOK-STORY-01-C01-R001.yaml
│  │           │  └─ assets/
│  │           │     ├─ BOOK-STORY-01-C01-A001.asset.yaml
│  │           │     └─ BOOK-STORY-01-C01-A001.<ext>
│  │           └─ C02/
│  │
│  └─ new-testament/
│
├─ library/
│  ├─ characters/
│  │  └─ CHR-MOSES/
│  │     ├─ definition.yaml
│  │     ├─ references.yaml             # 필요할 때만
│  │     └─ assets/
│  │        ├─ CHR-MOSES-A001.asset.yaml
│  │        └─ CHR-MOSES-A001.<ext>
│  │
│  ├─ locations/
│  ├─ objects/
│  ├─ costumes/
│  ├─ environments/
│  └─ visual-styles/
│
├─ integrations/
│  ├─ registry.yaml
│  │
│  ├─ chatgpt/
│  │  ├─ provider.yaml
│  │  ├─ bindings/                     # 실제 Binding이 있을 때
│  │  ├─ generation-profiles/
│  │  │  └─ chat-native-standard.yaml
│  │  └─ prompt-adapters/
│  │     └─ canonical-cut-v1.md
│  │
│  ├─ openart/
│  │  ├─ provider.yaml
│  │  ├─ bindings/
│  │  ├─ generation-profiles/
│  │  └─ prompt-adapters/
│  │
│  └─ higgsfield/
│     ├─ provider.yaml
│     ├─ bindings/
│     ├─ generation-profiles/
│     └─ prompt-adapters/
│
└─ templates/
   ├─ episode.yaml
   ├─ storyboard.yaml
   ├─ cut.yaml
   ├─ continuity.yaml
   ├─ generation-run.yaml
   ├─ asset-metadata.yaml
   ├─ asset-selection.yaml
   ├─ external-references.yaml
   ├─ library-definition.yaml
   ├─ provider-binding.yaml
   └─ generation-profile.yaml
~~~

---

## 3. 루트 파일

### AGENTS.md

AI 작업의 최초 진입점이다.

책임:

- 저장소 목적
- Source of Truth
- 필수 탐색 순서
- 현재 진행 위치
- 주요 규칙 문서 위치
- 다른 저장소 접근 금지
- 작업 기록 원칙

세부 제작 규칙을 모두 중복 작성하지 않고 관련 문서로 라우팅한다.

### README.md

사람이 저장소를 처음 열었을 때 보는 프로젝트 소개다.

책임:

- 프로젝트 목적
- 저장소 구조 요약
- 핵심 제작 흐름
- 주요 문서 링크
- Asset / Git LFS 기본 안내

AGENTS.md와 달리 AI 실행 규칙보다 사람 중심의 설명을 우선한다.

### architecture_v0_draft.md

STEP 0에서 실제 설계 논의를 진행한 **역사적 Working Record**로 보존한다.

정식 Architecture 문서 분리가 완료된 이후에는 현재 규칙의 진입점으로 사용하지 않는다.

삭제하지 않고 STEP 0 결정이 만들어진 배경을 추적하는 기록으로 남긴다.

---

## 4. docs/architecture

STEP 0에서 확정한 모델을 정식 Architecture 문서로 분리한다.

~~~text
OVERVIEW.md
CONTENT_MODEL.md
EPISODE_CUT_MODEL.md
CONTINUITY_MODEL.md
LIBRARY_MODEL.md
GENERATION_RUN_MODEL.md
PROVIDER_INTEGRATION_MODEL.md
ASSET_STORAGE_POLICY.md
REPOSITORY_STRUCTURE.md
~~~

### OVERVIEW.md

전체 모델의 관계와 Source of Truth 계층을 요약한다.

~~~text
Scripture
↓
Episode / Storyboard / Cut
↓
Library + Continuity
↓
Provider Integration
↓
Generation Run / Result Review
↓
Asset Promotion
↓
Representative / Reference Asset
~~~

나머지 문서는 STEP 0-1~0-7의 CONFIRMED 내용을 각각 책임진다.

---

## 5. docs/rules

Architecture가 **데이터와 책임 구조**를 정의한다면 Rules는 **실제 제작 시 지켜야 할 판단 규칙**을 정의한다.

### MASTER_RULES.md

모든 제작 규칙의 상위 인덱스와 충돌 우선순위를 관리한다.

### SCRIPTURE_RULES.md

본문 충실도, Scripture Anchor, 본문에 없는 요소, 사건 시점 등의 규칙.

### HISTORICAL_RULES.md

고고학·지리·복식·건축·도구 등 역사 고증 원칙.

### VISUAL_RULES.md

프로젝트 시각 언어, 구도와 표현의 상위 원칙.

### CONTINUITY_RULES.md

Cut 사이 연속성 검토와 reset/change 기준.

### GENERATION_RULES.md

Run 생성, Prompt/Reference 사용, Review, 실패 기록, Asset Promotion 전 기본 절차.

### TEXT_AND_COPYRIGHT.md

성경 번역문, 이미지 내 텍스트, 외부 Reference 권리와 저작권 관련 규칙.

이전 채팅 인수인계 자료에서 발견한 실전 제작 규칙은 이 단계에서 현재 CONFIRMED Architecture와 충돌하지 않는 범위로 이관한다.

---

## 6. content/

실제 성경 일러스트 제작 데이터가 위치한다.

### 6.1 episode-sequence.yaml

Canonical Episode Sequence의 Source of Truth다.

Episode 자체에 previous / next 값을 중복 저장하지 않는다.

v1에서는 단일 sequence 파일로 시작한다.

규모가 실제로 커져 유지가 어려워질 때만 분할을 검토한다.

### 6.2 identity-tombstones.yaml

Retired Episode / Cut / Run / Result / Asset ID의 재사용 방지 Source of Truth다.

- active production data가 아니다.
- 과거 장면 정의나 Prompt를 보존하지 않는다.
- 새 ID 발급 전 current tree와 함께 확인한다.
- 상세 과거 기록은 Git history가 소유한다.

### 6.3 Testament / Book 디렉터리

~~~text
content/
└─ old-testament/
   └─ genesis/
      └─ BOOK-STORY-01/
~~~

성경 책 구조는 **탐색 경로**이며 Episode identity는 항상 Episode ID가 소유한다.

STORY_KEY를 별도 Story Arc entity 디렉터리로 만들지 않는다.

따라서 초기 초안처럼 다음 구조는 사용하지 않는다.

~~~text
genesis/
└─ creation/
   └─ BOOK-STORY-01/
~~~

대신:

~~~text
genesis/
└─ BOOK-STORY-01/
~~~

로 둔다.

이는 STEP 0-1에서 STORY_KEY를 naming token으로만 확정한 결정과 일치한다.

---

## 7. Episode 디렉터리

예:

~~~text
BOOK-STORY-01/
├─ episode.yaml
├─ storyboard.yaml
├─ continuity.yaml
├─ references.yaml
└─ cuts/
~~~

### episode.yaml

Episode Canonical 정의:

- episode_id
- title
- primary_scripture
- supporting_scripture
- production_intent
- definition_status
- revision

Cut 수를 별도 저장하지 않는다.

### storyboard.yaml

Cut 순서의 Source of Truth.

- order
- cut_id
- scripture_anchor
- beat
- transition note — previous active Cut → current Cut의 incoming high-level intent

### continuity.yaml

Episode 내부 Continuity Segment / transition과 incoming Cross-Episode boundary를 관리한다.

### references.yaml

필요한 Episode-level External Reference Record가 있을 때만 생성한다.

빈 파일을 미리 만들지 않는다.

---

## 8. Cut 디렉터리

~~~text
cuts/
└─ C01/
   ├─ cut.yaml
   ├─ asset-selection.yaml
   ├─ references.yaml
   ├─ runs/
   └─ assets/
~~~

폴더명은 Episode 내부에서 이미 scope가 명확하므로 짧은 `C01`을 사용한다.

Canonical identity는 폴더명이 아니라 cut.yaml의 전체 Cut ID다.

### cut.yaml

Cut의 Canonical Scene Definition만 관리한다.

- cut_id
- episode_id
- scene_intent
- scene.summary
- required_elements
- forbidden_elements
- definition_status
- revision
- Library reference / current scene assignment

Storyboard의 scripture_anchor / beat를 물리적으로 중복 저장하지 않는다.

### asset-selection.yaml

이미지 선택 상태를 Cut definition과 분리한다.

책임:

- 현재 representative_asset
- selected_against
- representative history

대표 이미지 교체만으로 cut.yaml revision을 변경하지 않는다.

대표 Asset이 생기기 전에는 파일을 만들지 않아도 된다.

### references.yaml

Cut-level External Reference Record를 저장할 필요가 있을 때만 생성한다.

---

## 9. runs/

STEP 0-5의 Generation Run을 저장한다.

~~~text
runs/
├─ BOOK-STORY-01-C01-R001.yaml
├─ BOOK-STORY-01-C01-R002.yaml
└─ ...
~~~

각 Run 파일이 다음을 함께 가진다.

- target Cut revision
- Git source commit
- 사용한 Library revision/profile
- Provider / model
- Integration profile / adapter revision
- observable prompt/instruction snapshot
- Reference input
- settings
- execution status
- Generated Result metadata
- Result Review history
- 비용 / credit 정보

별도 `prompt.md`를 Canonical Cut 파일로 두지 않는다.

Prompt는 Provider Adapter가 생성하는 파생 데이터이며 실제 실행 Prompt snapshot은 각 Run이 소유한다.

---

## 10. assets/

Cut 또는 Library의 장기 보존 Asset을 저장한다.

~~~text
assets/
├─ BOOK-STORY-01-C01-A001.asset.yaml
└─ BOOK-STORY-01-C01-A001.png
~~~

원칙:

- metadata는 일반 Git
- image binary는 Git LFS
- binary filename = Asset ID + 실제 확장자
- 같은 Asset ID의 binary overwrite 금지
- Derivative는 필요 시 배포 파이프라인에서 생성
- 현재 역할은 Asset metadata가 아닌 소비자 관계 데이터가 Source of Truth

Asset이 하나도 없다면 빈 assets 디렉터리를 반드시 만들 필요는 없다.

---

## 11. library/

Library 종류는 STEP 0-4에서 확정한 여섯 종류만 둔다.

~~~text
characters
locations
objects
costumes
environments
visual-styles
~~~

각 entity의 기본 구조:

~~~text
CHR-MOSES/
├─ definition.yaml
├─ references.yaml
└─ assets/
~~~

### definition.yaml

Canonical Asset 정의와 local profile/variant를 관리한다.

### references.yaml

- Reference Asset 관계
- External Reference Record

를 관리한다.

Reference가 없다면 생성하지 않아도 된다.

### assets/

해당 Library entity가 primary registration owner인 장기 보존 이미지 Asset.

Library entity와 Reference image를 동일한 것으로 취급하지 않는다.

---

## 12. integrations/

Provider-specific 정보는 모두 이 영역에 격리한다.

~~~text
integrations/
├─ registry.yaml
└─ <provider_key>/
   ├─ provider.yaml
   ├─ bindings/
   ├─ generation-profiles/
   └─ prompt-adapters/
~~~

### registry.yaml

Provider key와 integration status의 전역 registry.

### provider.yaml

해당 Provider의 capability / execution mode / 검증 시점.

### bindings/

Canonical Library Entity과 Provider external resource 연결.

파일은 실제 Binding이 생길 때 생성한다.

권장 파일명:

~~~text
<binding_key>.yaml
~~~

### generation-profiles/

재사용 가능한 Provider generation profile.

~~~text
<profile_key>.yaml
~~~

### prompt-adapters/

Provider별 Prompt 변환 규칙.

Prompt Adapter가 주로 텍스트 규칙이면 Markdown을 사용할 수 있고, 기계적 설정 중심이면 YAML을 사용할 수 있다.

현재 ChatGPT의 첫 정식 Adapter는 `integrations/chatgpt/prompt-adapters/canonical-cut-v1.md`의 Markdown 형식을 사용한다. 다른 Provider는 실제 요구에 따라 최소 형태를 선택한다.

### Secrets

API key, token, cookie 등 credential은 이 디렉터리에 저장하지 않는다.

---

## 13. templates/

새 Episode/Cut/Run/Asset을 일관되게 생성하기 위한 템플릿이다.

v1 기본 템플릿 후보:

~~~text
episode.yaml
storyboard.yaml
cut.yaml
continuity.yaml
generation-run.yaml
asset-metadata.yaml
asset-selection.yaml
external-references.yaml
library-definition.yaml
provider-binding.yaml
generation-profile.yaml
~~~

실제로 반복 생성되는 데이터만 Template로 만든다.

모든 Library 종류마다 거의 동일한 템플릿을 복제하지 않고 공통 `library-definition.yaml`에서 시작해 필요한 type 필드를 확장한다.

---

## 14. docs/progress

### CURRENT.md

새 채팅에서 가장 먼저 현재 작업 위치를 복원하는 문서.

최소 내용:

- 현재 Phase
- 현재 Episode
- 현재 Cut
- 마지막 완료 항목
- 현재 진행 작업
- 다음 작업
- blocker
- 관련 결정 / 문서

### MILESTONES.md

주요 단계 완료 기록.

예:

- STEP 0 Architecture Definition 완료
- Repository structure 확정
- 특정 Architecture migration 완료
- 특정 Episode production complete

---

## 15. docs/decisions

장기적으로 다시 논의될 가능성이 높은 중요한 선택을 결정 기록으로 남긴다.

~~~text
DEC-0001-<slug>.md
DEC-0002-<slug>.md
~~~

Decision 문서는 단순 작업 로그가 아니다.

다음 같은 경우에 사용한다.

- 대안이 둘 이상 있었음
- 나중에 왜 이렇게 설계했는지 설명할 필요가 있음
- 변경 비용이 큼
- 여러 영역에 영향을 줌

STEP 0의 모든 세부 항목을 각각 Decision 파일로 소급 생성하지 않는다.

정식 Architecture 문서가 이미 결정의 Source of Truth가 되므로, 실제로 가치가 높은 횡단 결정만 Decision으로 남긴다.

---

## 16. Markdown vs YAML 기준

기본 원칙:

### Markdown

사람이 읽고 판단하는 설명형 문서.

예:

- Architecture
- Rules
- Decisions
- Progress
- README
- Prompt Adapter의 설명 중심 규칙

### YAML

ID, 상태, 참조, revision, sequence, generation input/output 등 구조화 데이터.

예:

- Episode
- Storyboard
- Cut
- Continuity
- Library definition
- Generation Run
- Asset metadata
- Provider Registry / Binding / Profile

JSON은 외부 Provider raw metadata 등 특별한 필요가 생길 때만 사용한다.

---

## 17. 의도적으로 만들지 않는 구조

현재 v1에서는 다음을 만들지 않는다.

### 전역 prompts/ 디렉터리

Cut-specific Prompt는 Canonical Source가 아니므로 별도 전역 Prompt 저장소를 만들지 않는다.

Provider Adapter는 integrations/, 실제 Prompt snapshot은 Run에 저장한다.

### Story Arc entity

STORY_KEY는 ID naming token일 뿐이므로 별도 Story Arc 데이터 모델이나 디렉터리를 만들지 않는다.

### 모든 Result binary 보관 폴더

Run metadata는 유지하지만 모든 Provider output binary를 저장소에 자동 적재하지 않는다.

보존 대상만 Asset Promotion한다.

### 전역 assets/ 창고

Asset은 Cut 또는 Library primary owner 아래에 등록한다.

### Provider별 Canonical Character 복사본

OpenArt/Higgsfield/ChatGPT 구조가 Library 원본을 대체하지 않는다.

### 빈 미래 디렉터리 대량 생성

논리 구조에 존재하더라도 실제 사용 시점에 생성한다.

---

## 18. Git LFS 적용 위치

STEP 0-7에 따라 장기 보존 이미지 binary만 Git LFS로 추적한다.

최종 구조 생성 단계에서는 최소한 다음 image 확장자 패턴을 검토한다.

~~~text
*.png
*.jpg
*.jpeg
*.webp
*.avif
~~~

다만 실제 .gitattributes에는 **Production Master로 허용할 포맷 정책을 먼저 정한 후 필요한 패턴만 추가**한다.

모든 이미지 확장자를 무조건 LFS로 등록하는 것은 피한다.

metadata YAML/Markdown은 LFS 대상이 아니다.

---

## 19. 초기 생성 범위

구조 승인 직후에도 전체 트리를 한 번에 빈 폴더로 만들지 않는다.

첫 실제 구조 생성에서는 다음만 생성하는 것을 권장한다.

~~~text
README.md

docs/
  architecture/
  rules/
  decisions/
  progress/

content/
  episode-sequence.yaml

library/
  필요한 category만

integrations/
  registry.yaml
  실제 사용할 Provider만

templates/
  실제 바로 사용할 핵심 template
~~~

실제 Episode 제작을 시작할 때 처음으로 필요한 Episode / Cut / Run / Asset 디렉터리를 만든다.

이렇게 하면 구조는 확정하되 저장소에는 실제 데이터가 있는 폴더만 존재하게 된다. 폐기된 production iteration의 디렉터리를 구조 유지를 이유로 빈 껍데기 형태로 복원하지 않는다.

---

## 20. 기존 STEP 0 Working Record 처리

`architecture_v0_draft.md`는 즉시 삭제하거나 덮어쓰지 않는다.

진행 순서:

1. 이 Repository Structure 승인
2. 정식 Architecture 문서 생성
3. STEP 0 CONFIRMED 내용을 정식 문서로 이관
4. 이관 검증
5. AGENTS.md의 기본 탐색 순서를 정식 문서 기준으로 변경
6. `architecture_v0_draft.md`를 historical working record로 명시

필요성이 생기면 이후 `docs/archive/`로 옮길 수 있지만, 현재 단계에서 archive 디렉터리를 미리 만들지는 않는다.

---

## 21. 구조 승인 후 운영 원칙

Repository Structure v1.0이 확정된 뒤에는 특정 Episode를 전제로 한 고정 실행 순서를 두지 않는다.

1. `AGENTS.md`와 `CURRENT.md`에서 현재 작업 위치를 확인한다.
2. 필요한 Architecture / Rules / Template을 확인한다.
3. 새 production entity가 실제로 필요할 때만 해당 Episode / Cut / Run / Asset 경로를 생성한다.
4. 폐기된 production iteration의 디렉터리나 파일은 새 작업의 출발점으로 복원하지 않는다.
5. production identity는 current tree와 `content/identity-tombstones.yaml`을 모두 확인한 뒤 기존 ID 불변 조건을 지켜 새로 발급한다.

---

## 22. 확정 항목

다음 핵심 항목은 사용자 승인으로 확정되었다:

- docs / content / library / integrations / templates의 최상위 책임 분리
- Architecture 8개 문서 + Repository Structure 문서 구성
- Rules 문서 7종 구성
- 전역 `content/episode-sequence.yaml`
- retired identity용 `content/identity-tombstones.yaml`
- 성경 Book 아래에 STORY_KEY 중간 폴더를 두지 않는 구조
- Episode의 episode / storyboard / continuity 분리
- Cut의 cut.yaml과 asset-selection.yaml 분리
- Cut별 `runs/`와 `assets/`
- 별도 canonical prompt.md를 만들지 않는 원칙
- Library entity의 definition / references / assets 구조
- Provider Registry / Binding / Generation Profile / Prompt Adapter 격리
- Markdown은 설명, YAML은 구조화된 Canonical/Production 데이터라는 기본 기준
- Generated Result binary 전량 보관 폴더를 만들지 않는 원칙
- Asset은 Cut 또는 Library primary owner 아래 등록
- 빈 미래 디렉터리를 대량 생성하지 않는 원칙
- STEP 0 Working Record를 정식 문서 이관 완료 전까지 보존하는 원칙

위 항목은 2026-10-02 사용자 승인으로 확정되었다.

**Repository Structure v1.0: CONFIRMED**

현재 Repository Structure v1.0은 확정 상태이며 실제 production 데이터는 필요 시점에 단계적으로 생성한다.
