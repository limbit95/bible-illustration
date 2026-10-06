# Master Production Rules v1.2

> 상태: **CONFIRMED / 2026-10-06 pre-production hardening**
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

1. 본문 범위와 Scripture Work Unit 확인
2. 새 identity가 필요하면 current tree와 `content/identity-tombstones.yaml`에서 ID 재사용 여부 확인
3. 필요한 경우 Storyboard / Cut 분해안 작성
4. Multi-Cut이면 Storyboard Production Preflight 수행
5. Episode / Storyboard / Cut의 책임과 현재 정의 확인
6. 필요한 Library Entity / Historical Research 확인
7. 이전·다음 Cut과 Canonical Continuity 확인
8. Canonical Scene의 required / forbidden 요소 확인
9. Provider Adapter / Profile을 통해 생성 입력 준비
10. Generation Run 실행
11. Scripture / Historical / Continuity / Library / Visual / Technical 검토
12. Result Review 기록
13. 필요 시 새 Run
14. 장기 보존 가치가 있는 결과만 Asset Promotion
15. downstream actual reference가 되는 working Result는 다음 Run 전에 Asset Promotion
16. 대표 Asset 선정
17. Progress 기록 후 다음 Cut 진행

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

## 8. 폐기된 Production Iteration의 취급

과거 prototype이나 production iteration에서 사용한 Cut 구성, 장면별 색감·광원·구도·질감·카메라, Provider-specific prompt 패턴은 현재 Rules로 자동 계승하지 않는다.

폐기된 production iteration에서 반복 검증되어 일반화할 가치가 있는 원칙은 Architecture / Rules / Decision에 별도로 승격한 경우에만 현재 기준으로 사용한다.

과거 상세 값이 필요하면 Git history에서 확인할 수 있지만, 이를 새 Episode의 기본값으로 복원하지 않는다.

## 9. Scripture Work Unit 운영

실전 제작의 기본 입력은 사용자가 제시한 **한 구절 또는 의미 있는 본문 구간**이다.

이 입력 단위를 편의상 Scripture Work Unit이라 부른다.

Scripture Work Unit과 Cut의 관계는 1:1로 고정하지 않는다.

- 한 Work Unit이 한 Cut으로 충분할 수 있다.
- 한 구절 안에 단계적 변화가 있으면 여러 Cut으로 나눌 수 있다.
- 서로 강하게 이어지는 여러 절이 하나의 시각적 beat라면 한 Cut으로 묶을 수 있다.

Assistant는 실제 생성 전에 해당 본문을 보고 장면 수를 판단한다.

- 한 장면으로 충분하면 multi-Cut advisory를 생략할 수 있지만, 최소 Episode + 1-entry Storyboard + Cut Canonical Scene을 먼저 정의한 뒤 제작한다.
- 여러 장면이 더 자연스러우면 짧은 Storyboard / Cut 분해안을 먼저 제안한다.
- 사용자가 장면 수나 분할 방식을 직접 지정하면, Scripture / Architecture / Continuity와 충돌하지 않는 한 이를 우선한다.

Storyboard 제안에는 필요한 만큼만 다음을 포함한다.

- Cut 수
- 각 Cut의 Scripture Anchor
- 각 Cut의 핵심 visual beat
- 인접 Cut의 high-level Scene Relation / transition intent
- 본문 coverage나 continuity에서 아직 해결해야 할 concern

Storyboard 단계에서 상세 RETAIN / change / reset constraint를 복제하지 않는다.
그 값의 Canonical Source of Truth는 Continuity다.

이 운영은 장면 수를 늘리기 위한 규칙이 아니라,
본문 누락과 과도한 압축을 피하고 자연스러운 시각 전개를 확보하기 위한 규칙이다.

## 10. 순차 생성 기본값

여러 이미지를 한 번에 일괄 생성하는 것을 기본값으로 두지 않는다.

기본 제작은:

1. Scripture Work Unit 확인
2. 장면 수 판단
3. 필요 시 Storyboard 합의
4. 첫 Cut 생성
5. 사용자 승인 / 수정
6. 직전 승인 장면을 참고해 다음 Cut 생성

순서로 진행한다.

한 번의 요청으로 여러 이미지를 생성하는 방식은 사용자가 명시적으로 원하거나,
인접 장면 Continuity 위험이 낮다고 판단되는 경우에만 사용한다.

빠른 제작의 의미는 대량 batch 생성이 아니라,
과도한 사전 polish, 일반적인 Asset promotion, representative 확정을 뒤로 미뤄
본문과 장면 흐름을 먼저 완주하는 데 있다.

단, 어떤 working/accepted Result가 다음 Run의 actual project-owned binary reference가 되면
DEC-0009에 따라 해당 Result의 Asset Promotion과 Git LFS ingest는 다음 Run 전에 먼저 완료한다.


## 11. Scene Relation 판단

새 Cut을 만들기 전에 직전 Cut과의 관계를 먼저 판단한다.

### Continuity-oriented

같은 사건의 직접적인 다음 단계이거나,
이전 상태를 유지해야 본문의 변화가 자연스럽게 이해되는 경우에는
시각적 연결을 우선한다.

- RETAIN을 보존한다.
- 이번 본문이 요구하는 DELTA만 추가한다.
- 중간 단계를 건너뛰는 FORBIDDEN LEAP를 피한다.
- 직전 승인 이미지를 continuity anchor 후보로 사용할 수 있다.
- 실제 Provider binary reference로 사용할 때는 available Asset으로 먼저 Promotion한다.

### Transition-oriented

새로운 사건, 새로운 창조 국면, 새로운 핵심 subject가 시작되거나
이전 구도와 분위기를 계속 유지하는 것이 본문 전달을 약하게 만드는 경우에는
시각적으로 새로운 장면으로 전환할 수 있다.

이 경우에도 Story Continuity는 유지하지만
카메라, 구도, 스케일, 팔레트, 조명, 분위기는 새롭게 설계할 수 있다.

Assistant는 사용자가 별도 지시하지 않아도 Continuity와 Transition 중 더 적절한 방식을 먼저 판단한다.
사용자가 명시적으로 연결 또는 전환을 요청하면 Scripture / Architecture와 충돌하지 않는 한 이를 우선한다.

Canonical 저장은 새로운 enum을 만들지 않고 기존 Continuity Model의
`continue | partial_reset | reset`과 Storyboard의 `transition_note`를 사용한다.


## 12. Storyboard Production Preflight

여러 Cut을 포함하는 Episode 또는 Scripture Work Unit은 실제 이미지 생성 전에 Storyboard 전체를 한 번의 계획 단위로 검토한다.

이 검토는 새 데이터 엔터티나 새 Storyboard 필드를 요구하지 않는다.
현재 Storyboard의 `order / cut_id / scripture_anchor / beat / transition_note`와 관련 Episode / Cut / Continuity 정의를 함께 본다.

### 12.1 Scripture Coverage Gate

- primary Scripture / Work Unit의 의미 있는 본문 구간이 이유 없이 빠지지 않았는가
- 같은 본문이 여러 Cut에 걸치면 각 Cut의 역할이 beat로 구분되는가
- 여러 절을 하나의 Cut으로 묶을 때 중요한 사건·상태가 사라지지 않는가
- supporting Scripture가 primary Scripture의 제작 근거를 대신하고 있지 않은가

### 12.2 Beat Granularity Gate

- 각 Cut에 하나의 명확한 visual beat가 있는가
- 한 화면에 서로 경쟁하는 핵심 사건을 과도하게 압축하지 않았는가
- 본문상 변화가 거의 없는 Cut을 의미 없이 세분화하지 않았는가
- 분할 또는 통합의 이유를 사건·상태·시간·장소·시각적 초점 변화로 설명할 수 있는가

### 12.3 Scene Relation Gate

인접 Cut마다 먼저 다음 중 어떤 제작 방향이 적절한지 판단한다.

- Continuity-oriented
- Transition-oriented

Storyboard의 `transition_note`에는 이 관계의 **high-level intent**만 기록한다.

실제 `continue | partial_reset | reset`, retain / change / reset constraint는 Continuity가 소유한다.

### 12.4 Visual Repetition Gate

본문의 의미가 달라지는데도 동일한 시각적 메시지가 반복되는지 검토한다.

필요하면 다음 축의 변화 가능성을 검토한다.

- dominant visual subject
- camera distance
- viewpoint
- scale
- composition
- lighting concept
- visual rhythm

단순한 다양성을 위해 Scripture fact나 사건 chronology를 바꾸지 않는다.

### 12.5 Episode Rhythm Gate

Multi-Cut Storyboard 전체에서 다음을 점검한다.

- establishing / medium / detail의 흐름
- Continuity / Transition 배치
- 반복되는 구도와 dominant subject
- visual climax
- rest / breathing frame
- 같은 분위기의 불필요한 장기 반복

고정 비율이나 장면 수 quota는 두지 않는다.

### 12.6 Presentation Intent Gate

필요하면 각 beat가:

- Key Scripture 중심인지
- Explanatory / Transitional 중심인지

를 판단한다.

이 판단은 presentation 전략을 준비하기 위한 것이며,
Storyboard에 직접 인용문 전체나 narration 문장을 중복 저장하는 근거가 아니다.

### 12.7 Generation Entry Gate

Storyboard Preflight에서 중요한 coverage / granularity / relation / repetition 문제가 남아 있으면
여러 Cut의 본격 Generation으로 진입하지 않는다.

단, 장면 표현 가능성을 확인하기 위한 제한적인 탐색 Run은 기존 Rules 범위 안에서 허용한다.
