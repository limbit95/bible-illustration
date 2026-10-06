# Genesis Creation — Rapid Full-Chapter Prototype Guide

> 상태: **ACTIVE WORKING GUIDE / 2026-10-03**
>
> 목적: 창세기 1장 천지창조 전체를 빠르게 1차 완주하면서,
> 사용자 선택 레퍼런스에서 확인된 시각적 연결 원칙을 실제 시퀀스에 적용한다.
>
> 관련 결정: `DEC-0004`, `DEC-0005`

## 1. 이번 Pass의 목표

이번 Pass의 목표는 개별 Cut의 production complete가 아니다.

우선순위:

1. 창세기 1장 전체 장면을 처음부터 끝까지 한 번 완주
2. 인접 이미지의 자연스러운 연결
3. 본문 사건의 단계적 변화
4. 시리즈 전체 리듬 확인
5. 이후 2차 Pass에서 디테일 수정과 선별

## 1.1 이번 Pass의 실제 운영 단위

전체 목표는 창세기 1장 완주이지만, 실제 제작은 **본문 한 구간씩 순차 진행**한다.

기본 흐름:

1. 사용자가 개역한글 본문 한 구절 또는 의미 있는 구간을 가져온다.
2. Assistant가 해당 본문을 1장면으로 갈지 여러 장면으로 나눌지 먼저 판단한다.
3. 여러 장면이 필요하면 생성 전에 짧은 Storyboard를 제안한다.
4. 사용자와 장면 구성을 정한 뒤 첫 장면을 생성한다.
5. 사용자가 승인하거나 수정한다.
6. 다음 장면은 직전 승인 이미지를 visual continuity anchor로 사용하되 Canonical Continuity를 우선한다.
7. 이 과정을 다음 본문으로 반복한다.

**한 구절 = 한 이미지**로 고정하지 않는다.
분리·등장·이동·성장·질서 형성처럼 단계가 중요한 본문은 여러 Cut으로 나눌 수 있다.

또한 rapid prototype의 속도는 여러 장을 한 번에 뽑는 방식으로 확보하지 않는다.
Asset promotion, Git LFS ingest, representative 확정, 과도한 polish를 뒤로 미루는 방식으로 확보한다.

## 1.2 Scene Relation 판단

Genesis Creation prototype에서도 모든 이미지를 강제로 연결하지 않는다.

각 새 본문 / Cut 전에:

- 같은 창조 행위의 점진적 단계면 **Continuity**
- 새로운 창조 국면이나 새로운 visual subject면 **Transition**

을 먼저 판단한다.

Continuity에서는 이전 승인 이미지의 RETAIN + DELTA를 중심으로 진행한다.
Transition에서는 Story와 Scripture fact는 유지하되 구도, 카메라, 스케일, 팔레트, 분위기를 새롭게 설계할 수 있다.

예:
- 흑암 → 빛 등장 → 빛/어둠 분리: Continuity 비중 높음
- 뭍 형성 → 식물 창조: Transition 가능
- 식물 창조 → 광명과 주야 질서: Transition 권장 가능

사용자가 연결/전환을 따로 말하지 않아도 Assistant가 먼저 판단하고,
필요하면 Storyboard 제안에 relation 판단을 포함한다.

## 2. 사용자 선택 Working Reference Sequence

사용자가 2026-10-03 최종 첨부한 00→06 이미지 묶음을 Genesis Creation rapid prototype의 **working visual reference sequence**로 사용한다.

이 sequence의 가장 중요한 목적은 각 이미지의 독립적 디자인 복제가 아니라 **인접 이미지 사이의 변화량과 연결 연출을 재현하는 것**이다.

이 binary들은 아직 Canonical Asset으로 승격하지 않는다. 새 채팅에서 정확한 visual anchoring이 필요하면 사용자가 00→06 세트를 다시 첨부한다. 전체 prototype 선별 뒤에만 필요한 reference / production binary를 Git LFS Asset으로 승격한다.

### 2.1 00→06 연결 흐름

00 — **완전한 흑암**
- pure black에 가까운 시작 화면
- 형체·공간·광원·천체를 드러내지 않는다

01 — **흑암 속 원초적 구조가 아주 약하게 감지됨**
- 짙은 blue-black / navy
- 물·안개·구름처럼 보이지만 어느 하나로 고정되지 않는 원초적 유체 덩어리
- 광원은 사실상 없음
- 현대적 바다 / 뚜렷한 수평선으로 읽히지 않게 한다

02 — **동일한 세계에 차가운 빛이 처음 스며듦**
- 01의 큰 덩어리 배치와 카메라를 최대한 유지
- 우측 계열에서 작은 cool blue-white light가 나타남
- 빛은 공간을 일부만 드러내며 어둠이 여전히 우세

03 — **같은 위치의 빛이 따뜻해지기 시작**
- 02의 구조를 유지
- 광원의 위치와 흐름은 이어지고 warm gold-white가 처음 섞임
- 전체 세계를 한 번에 밝히지 않는다

04 — **금빛 광휘가 확장되고 빛/어둠 대비가 커짐**
- 이전 물/안개/구름 덩어리와 표면 언어 유지
- 동일 방향의 빛이 강해지며 반사 영역이 넓어짐
- 갑작스러운 새로운 지형·천체·현대적 풍경을 추가하지 않는다

05 — **밝은 영역과 열린 공간감이 한 단계 더 확대됨**
- 04와 같은 세계의 다음 순간이어야 한다
- warm gold-white의 비중이 증가하지만 blue-black 영역도 남김
- 질감과 카메라를 이유 없이 바꾸지 않는다

06 — **같은 장면 언어가 더 안정된 밝은 상태로 진행**
- 05의 덩어리·광원 방향·표면 질감을 유지
- 밝음의 진행은 명확하지만 별도의 새 세계처럼 보이지 않는다
- 이후 궁창·물의 분리·뭍 등장 같은 사건을 만들 때도 이 연속성을 출발점으로 삼는다

### 2.2 Genesis Creation 전용 Visual Profile

다음은 **Genesis Creation 한정** working profile이다. 성경 전체 프로젝트의 전역 Style로 사용하지 않는다.

- widescreen 16:9 계열
- 넓은 시야 / 광각적 공간감
- 초기 palette: pure black / blue-black / deep navy
- 빛의 progression: 거의 없음 → cool blue-white → warm gold-white
- 원초적 water / mist / cloud가 서로 섞이는 유기적 유체 질감
- 고정된 현실 사물보다 거대한 흐름과 덩어리의 움직임을 우선
- 지나치게 glass / crystal처럼 반짝이는 표면 금지
- 현실적인 현대 바다 사진처럼 정돈된 수평선·해변·산악 풍경으로 갑자기 변하지 않음
- 후속 상태는 이전 장면의 구조를 보존한 채 Scripture가 요구하는 delta만 추가

이 profile은 docs/rules/VISUAL_RULES.md의 전역 규칙을 대체하지 않는다.

## 3. 가장 중요한 Continuity 원칙

**다음 Cut을 새로 그리지 말고 이전 Cut을 다음 순간으로 변화시킨다.**

인접 Cut 제작 시 항상 두 집합을 먼저 정한다.

### RETAIN

이번 장면에서도 유지되어야 하는 것.

예:
- 카메라 위치
- 광각감
- 화면의 큰 덩어리 배치
- 물/안개 흐름 방향
- 팔레트
- 표면 질감
- 광원의 기본 방향

### DELTA

본문 때문에 이번 장면에서 새로 변해야 하는 것.

한 Cut의 생성 instruction은 가능하면 이 DELTA를 중심으로 작성한다.

## 4. 변화량 규칙

한 단계에서 필요한 것보다 더 많이 미래 상태를 보여주지 않는다.

좋은 진행 예:

~~~text
완전한 흑암
→ 구조는 같고 아주 희미한 빛만 시작
→ 같은 위치에서 빛이 조금 더 강해짐
→ 빛과 어둠의 구분이 명확해짐
~~~

~~~text
하나로 뒤엉킨 원초적 물
→ 일부에서 작은 공간이 벌어지기 시작
→ 동일한 물 덩어리가 위·아래로 더 분리
→ 분리된 공간이 안정됨
~~~

~~~text
물이 가득한 상태
→ 물의 흐름이 한쪽으로 모이기 시작
→ 기존 수면보다 아주 낮은 젖은 지형이 처음 드러남
→ 이후에야 땅의 면적과 고도가 증가
~~~

나쁜 진행 예:

~~~text
물만 있음
→ 다음 Cut에서 갑자기 높은 산과 넓은 육지
~~~

## 5. Composition Continuity

같은 사건의 연속 Cut에서는 가능하면 전 Cut 이미지를 visual reference로 직접 사용한다.

새 Cut 생성 시:
- 이전 Cut을 primary continuity anchor
- 필요할 경우 Episode working reference를 secondary style anchor
- 장면에 새로 생기는 요소만 instruction으로 강조

카메라 변경이 본문 전달에 꼭 필요하지 않으면 임의로 바꾸지 않는다.

## 6. Prototype 제작 속도

이번 Pass에서는 한 Cut을 완벽하게 만드는 데 오래 머무르지 않는다.

기본:
- 첫 생성
- 큰 위화감이 있으면 1~2회 수정
- 전체 흐름 판단에 충분하면 다음 Cut으로 이동

전체 완주 후:
- 연결이 튀는 부분
- Scripture 의미가 약한 부분
- 사용자가 특히 중요하게 느끼는 장면
을 2차 Pass에서 집중 수정한다.

## 7. Asset / LFS 처리

Prototype 중 생성된 모든 이미지를 즉시 Asset으로 승격하지 않는다.

~~~text
rapid generation / edit
→ whole-chapter sequence review
→ shortlist
→ accepted Result / Review 정리
→ Asset promotion
→ Git LFS ingest
→ representative selection
~~~

기존 C05/C06 pending_ingest 후보는 historical record로 남기고,
현재 prototype pass를 막는 blocker로 사용하지 않는다.

## 8. Presentation Text Reference

Production Master는 계속 text-free다. 텍스트가 포함된 결과는 **presentation derivative**다.

사용자가 2026-10-03 승인한 텍스트 분위기를 현재 Genesis Creation prototype의 presentation reference로 사용한다.

### 8.1 선택 원칙

Genesis Creation은 본문 자체가 핵심 창조 선언과 행위를 구성하는 경우가 많으므로:

- 중요한 본문 장면은 **개역한글 직접 인용 우선**
- Key Scripture Frame에서는 본문과 나레이션을 동시에 넣지 않는 것을 기본으로 함
- 역사·배경·장면 연결 설명이 주목적인 별도 Frame에서만 narration 사용
- 장절 / direct_quote / translation / narration 데이터는 분리 관리

### 8.2 시각 분위기

- 작고 절제된 sans-serif
- 화려한 장식체 금지
- 영화 자막 / 오프닝 카드 같은 조용한 인상
- 기본 좌하단 배치
- 넉넉한 여백
- 장절 표기는 본문보다 작게
- off-white text
- 아주 약한 shadow 또는 필요 시 subtle gradient
- 이미지의 주요 시각 요소를 가리지 않음
- pure-black 장면에서 절제된 intertitle 사용 가능

1672×941 reference 기준:
- left margin 약 5~6%
- bottom margin 약 7~8%
- reference label 약 18 px 수준
- body 약 31 px 수준

수치는 고정값이 아니라 비례 재현을 위한 기준이다.

세부 전역 규칙은 docs/rules/TEXT_AND_COPYRIGHT.md를 따른다.

## 9. 새 채팅 시작 절차

새 채팅에서:

1. `AGENTS.md`
2. `docs/progress/CURRENT.md`
3. 이 문서
4. 관련 Genesis Episode / Storyboard / Continuity
를 읽고 상태를 복원한다.

정확한 시각 anchoring이 필요하면 사용자가 선택한 00→06 reference images를 다시 첨부한다.

그 후 사용자가 가져오는 개역한글 본문을 Scripture Work Unit으로 삼아 **한 장면씩 순차적으로** Genesis 1 전체 rapid prototype을 진행한다. 장면 분해가 필요하면 생성 전에 짧은 Storyboard를 먼저 제안한다.
