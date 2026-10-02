# Genesis Creation — Rapid Full-Chapter Prototype Guide

> 상태: **ACTIVE WORKING GUIDE / 2026-10-02**
>
> 목적: 창세기 1장 천지창조 전체를 빠르게 1차 완주하면서,
> 사용자 선택 레퍼런스에서 확인된 시각적 연결 원칙을 실제 시퀀스에 적용한다.
>
> 관련 결정: `DEC-0004`

## 1. 이번 Pass의 목표

이번 Pass의 목표는 개별 Cut의 production complete가 아니다.

우선순위:

1. 창세기 1장 전체 장면을 처음부터 끝까지 한 번 완주
2. 인접 이미지의 자연스러운 연결
3. 본문 사건의 단계적 변화
4. 시리즈 전체 리듬 확인
5. 이후 2차 Pass에서 디테일 수정과 선별

## 2. 사용자 선택 Working Reference Sequence

현재 대화에서 사용자가 00→06 순서로 직접 선별한 이미지 묶음을
Genesis Creation prototype의 **working visual reference sequence**로 사용한다.

이 binary들은 아직 Canonical Asset으로 승격하지 않는다.
정확한 이미지 reference가 필요한 새 채팅에서는 사용자가 00→06 세트를 다시 첨부하거나,
추후 선별 완료 뒤 Git LFS Reference Asset으로 승격해야 한다.

### Reference에서 추출한 핵심

- widescreen 16:9 계열
- 넓은 시야와 광각적 공간감
- 짙은 navy / blue-black 기반
- 물·안개·구름의 경계가 명확한 현실 물체라기보다 서로 섞이는 원초적 유체 덩어리
- 일반적인 현대 바다처럼 읽히는 깨끗한 수평선은 피함
- 밝은 단계에서도 태양 자체가 아니라 warm gold-white light가 세계 속에서 퍼짐
- 물과 빛의 표면은 지나치게 crystal / glass처럼 변하지 않고 같은 재질 언어를 유지
- 현실 풍경으로 갑자기 전환하지 않음

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

## 8. Presentation Text Test

Production Master는 계속 text-free다.

텍스트 테스트는 별도 presentation derivative로 만든다.

한 장면의 presentation 데이터는 최소 다음을 분리한다.

- scripture_anchor
- direct_quote
- translation
- narration

기본 공개 직접 인용 역본은 현재 규칙대로 개역한글이다.

시각 테스트 권장 구조:

~~~text
[성경 직접 인용 — 가장 높은 위계]
[창세기 1:3 · 개역한글]

[짧은 자체 내레이션 — 별도 위계]
~~~

본문 직접 인용과 내레이션이 같은 문장처럼 보이지 않도록 시각적으로 분리한다.

## 9. 새 채팅 시작 절차

새 채팅에서:

1. `AGENTS.md`
2. `docs/progress/CURRENT.md`
3. 이 문서
4. 관련 Genesis Episode / Storyboard / Continuity
를 읽고 상태를 복원한다.

정확한 시각 anchoring이 필요하면 사용자가 선택한 00→06 reference images를 다시 첨부한다.

그 후 **창세기 1장 전체 rapid prototype pass**를 처음부터 진행한다.
