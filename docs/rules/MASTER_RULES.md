# Master Production Rules v1.0 Working Draft

> 상태: **REVIEW READY**
>
> 목적: Bible Illustration 제작 전반에서 공통으로 적용할 상위 판단 규칙과 세부 Rules 문서의 우선순위를 정의한다.
>
> 이 문서는 Architecture를 대체하지 않는다. 데이터 구조와 Source of Truth 책임은 `docs/architecture/`가 소유하고, 이 문서는 실제 제작 판단을 규정한다.

## 1. 규칙 충돌 판단

모든 정보를 억지로 하나의 단일 순위표로 정렬하지 않는다.

충돌의 종류에 따라 Source of Truth를 구분한다.

### 1.1 데이터 구조와 책임 경계

파일 위치, ID, revision, 상태, Source of Truth 소유권처럼 **프로젝트 구조에 관한 문제**는 정식 `docs/architecture/` 문서가 최우선 기준이다.

Rules, Episode/Cut 데이터, Provider 설정이 Architecture의 책임 경계를 조용히 변경할 수 없다.

### 1.2 성경 내용과 장면 사실

사건·인물·대사·순서 등 **성경 내용에 관한 문제**는 해당 Scripture Anchor의 본문 직접 내용이 최우선 근거다.

승인된 Cut이나 Library 정의라도 본문 직접 내용과 중요한 충돌이 발견되면 기존 승인을 근거로 충돌을 유지하지 않고 revision 검토 대상으로 돌린다.

### 1.3 제작 행동

이미지 생성·고증·Continuity·텍스트·Review 같은 **제작 행동**은 이 `MASTER_RULES.md`와 해당 세부 Rules 문서를 따른다.

Episode / Cut / Library의 구체 정의는 이 범위 안에서 장면별 세부 조건을 구체화한다.

### 1.4 Provider와 생성 결과

Provider Integration, Prompt Adapter, Generation Profile, 개별 Run, 생성 결과 이미지는 상위 Canonical 정의와 Rules를 구현하는 하위 계층이다.

Provider의 한계나 생성 결과의 우연한 요소가 상위 정의를 조용히 덮어쓰지 않는다.

중요한 충돌이 발견되면 임의로 합리화하지 않고 기록한 뒤 사용자 검토를 받는다.

## 2. 최상위 제작 원칙

### 2.1 본문 우선

성경 본문에 명시된 내용이 제작의 최우선 근거다.

그림을 위해 필요한 시각적 재구성은 허용하지만, 본문에 없는 내용을 성경이 직접 말한 사실처럼 다루지 않는다.

### 2.2 최소 구현

모든 구절을 구체적인 인물·풍경·사물로 억지로 채우지 않는다.

본문이 시각적으로 특정하기 어려운 경우 표현을 줄이는 것이 더 정직하다면 검은 화면, 여백, 분위기, 텍스트 중심 presentation 등을 사용할 수 있다.

### 2.3 후대 요소 선행 묘사 금지

현재 Cut의 Scripture Anchor 시점에 아직 등장·형성·확정되지 않은 요소를 후대 상태 그대로 미리 그리지 않는다.

### 2.4 역사적 정직성

본문에 없는 세부를 정해야 할 때는 해당 시대·지역의 역사·지리·고고학적 맥락과 충돌하지 않도록 한다.

근거가 불확실한 reconstruction은 불확실성을 숨기지 않는다.

### 2.5 Continuity

연속 사건의 인접 Cut은 같은 세계의 다음 순간처럼 이어져야 한다.

유지해야 할 요소와 의도적으로 변하는 요소는 기억이나 Prompt가 아니라 Canonical Continuity로 관리한다.

### 2.6 Canonical Definition과 Rendering 분리

Cut / Library / Continuity가 먼저이며 Provider Prompt와 생성 이미지는 파생 결과다.

ChatGPT, OpenArt, Higgsfield 등 특정 Provider의 한계나 우연한 출력이 Canonical 정의를 역으로 결정하지 않는다.

### 2.7 원본 이미지와 텍스트 분리

성경 직접 인용, 장·절, 역본명, 내레이션, UI text는 기본적으로 Production Master 이미지에 영구 합성하지 않는다.

### 2.8 기록 우선

장기적으로 다시 필요할 제작 규칙, 판단 근거, Run, Review, Asset 선택과 진행 상태는 대화에만 남기지 않는다.

## 3. 제작 판단의 세 범주

본문에 없는 세부를 다룰 때 최소한 다음을 구분한다.

### A. 본문 직접 근거

본문에 직접 등장하거나 명시된 사건·인물·장소·행동·대사·순서.

### B. 역사·지리·시대 근거를 둔 시각적 재구성

그림 제작상 결정해야 하지만 본문에 직접 적히지 않은 외형·재료·식생·건축·환경 등의 재구성.

### C. 해석적 또는 연출적 추정

숨은 동기, 명시되지 않은 감정, 상징 해석, 구체화되지 않은 초자연 구조 등.

C는 최소화하며 필요한 경우 사실이 아니라 해석/연출임을 명확히 인식하고 기록한다.

## 4. 장면 분할 원칙

- Episode와 Cut 수를 고정 숫자에 맞추지 않는다.
- 본문의 자연스러운 사건·시간·장소·중심인물·목적 변화에 따라 분할한다.
- 중요 사건을 지나치게 압축하지 않는다.
- 한 Cut에는 가능한 한 하나의 주요 사건 또는 하나의 시각적 초점을 둔다.
- 하나의 사건을 여러 Cut으로 나누는 것은 허용한다.
- 실제 표시 순서는 Storyboard가 Source of Truth다.

## 5. 제작 루프

기본 제작 루프는 다음 흐름을 따른다.

1. 본문 범위와 Scripture Anchor 확인
2. Episode / Storyboard / Cut의 역할 확인
3. 필요한 Library / Historical Research 확인
4. 이전·다음 Cut과 Continuity 확인
5. Canonical Scene의 required / forbidden 요소 확인
6. Provider Adapter / Profile을 통해 생성 입력 준비
7. Generation Run 실행
8. Scripture / Historical / Continuity / Library / Visual / Technical 검토
9. Result Review 기록
10. 필요 시 새 Run
11. 장기 보존 가치가 있는 결과만 Asset Promotion
12. 대표 Asset 선정
13. Progress 기록 후 다음 Cut 진행

## 6. 과도한 사전 설계 금지

설계를 생략하지 않되, 모든 세부를 이미지 생성 전에 완벽히 확정하려고 제작을 불필요하게 지연하지 않는다.

Architecture가 허용하는 범위에서 draft / in_review Cut의 탐색 Run을 사용할 수 있다.

단:

- 탐색 Result는 Canonical 정의를 자동 변경하지 않는다.
- 현재 approved 기준의 대표 Asset이 되려면 다시 검토해야 한다.
- 방향이 명확하지 않은 상태에서 무작정 대량 생성하지 않는다.

## 7. 세부 Rules 문서

- `SCRIPTURE_RULES.md` — 본문 충실도와 해석 경계
- `HISTORICAL_RULES.md` — 역사·지리·복식·건축·도구 고증
- `VISUAL_RULES.md` — 시각 언어와 장면 표현
- `CONTINUITY_RULES.md` — Cut 간 유지·변화·reset 판단
- `GENERATION_RULES.md` — Provider 생성·Run·Review·Asset Promotion
- `TEXT_AND_COPYRIGHT.md` — 직접 인용·내레이션·텍스트 레이어·권리

## 8. 이전 테스트 규칙에서 계승하지 않는 항목

과거 테스트 자료의 **“한 Chapter를 기본 8컷으로 구성”** 규칙은 현재 Architecture와 충돌하므로 정식 규칙으로 계승하지 않는다.

현재는 본문 분량과 사건 흐름에 따라 Episode와 Cut 수를 유연하게 결정한다.

Genesis Creation의 과거 CUT 1–4와 당시 8개 흐름 계획은 향후 migration 시 **기존 작업 데이터**로 검토하며, 전역 제작 규칙으로 일반화하지 않는다.
