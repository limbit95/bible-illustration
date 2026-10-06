# Architecture Refinement Handoff — Storyboard Production Rules

> 상태: **ACTIVE HANDOFF / 2026-10-06**
>
> 목적: 이전 이미지 테스트에서 일반화된 문제를 Architecture / Rules에 반영하되, 폐기된 production iteration의 구체 장면 값에 의존하지 않고 Storyboard 제작 규칙을 강화한다.

## 1. 현재 기준

이전 Genesis production/test iteration의 상세 production data와 이미지는 현재 tree에서 retired 처리한다.

새 Architecture refinement는 과거 이미지의 구도·팔레트·광원·질감·카메라·장면 progression을 재현하기 위한 작업이 아니다.

현재 유지하는 것은 이미 일반화된 다음 원칙이다.

- Scripture Work Unit 기반으로 작업한다.
- 한 절과 한 Cut을 1:1로 고정하지 않는다.
- 필요하면 한 절을 여러 Cut으로 분해할 수 있다.
- 여러 절이 하나의 visual beat라면 한 Cut으로 묶을 수 있다.
- 여러 장면이면 생성 전에 Storyboard를 먼저 제안하고 사용자와 조정한다.
- 실제 생성은 기본적으로 한 장면씩 순차 진행한다.
- 인접 Cut은 Continuity-oriented / Transition-oriented 관계를 먼저 판단한다.
- Canonical 저장은 기존 `continue | partial_reset | reset`과 Storyboard의 `transition_note`를 사용한다.

## 2. 현재 Production 상태

현재 active Genesis production data는 없다.

- active Episode: none
- active Storyboard: none
- active Cut: none
- active Continuity: none
- active Run / Asset / representative image: none

새 제작을 시작할 때 이전 production 파일이나 이미지에서 복원하지 않는다.

과거에 사용한 production identity는 ID invariant에 따라 재사용하지 않으며, 새 Episode/Cut identity는 새 Storyboard 설계가 확정된 뒤 결정한다.

## 3. Storyboard Architecture 책임

현재 Storyboard의 정식 책임:

- Cut 표시 순서
- Cut ID
- Scripture Anchor
- 짧은 beat
- 필요 시 transition intent

Cut은 상세 Canonical Scene Specification을 담당한다.

Continuity는 실제 retain / change / reset constraint를 담당한다.

Storyboard에 Cut 상세 scene specification이나 Continuity 전체를 복제하지 않는다.

## 4. Storyboard Production Rules 강화 범위

### 4.1 Scripture Coverage

- Episode / Scripture Work Unit의 본문이 Storyboard에서 빠지지 않는지 확인한다.
- 같은 본문이 이유 없이 중복되지 않는지 확인한다.
- 한 절을 여러 Cut으로 나누면 각 Cut의 beat 역할이 실질적으로 달라야 한다.
- 여러 절을 한 Cut으로 묶으면 의미 있는 사건·상태가 과도하게 압축되지 않아야 한다.

### 4.2 Cut Boundary / Beat Granularity

- 한 Cut에는 가능한 한 하나의 명확한 visual beat를 둔다.
- 한 화면에 여러 핵심 사건을 억지로 압축하지 않는다.
- 변화가 거의 없는 미세 Cut을 이유 없이 늘리지 않는다.
- 분할과 통합은 절 번호가 아니라 사건·상태·시간·장소·시각적 초점의 변화로 판단한다.

### 4.3 Scene Relation Planning

Storyboard 단계에서 인접 Cut의 관계를 먼저 판단한다.

- Continuity-oriented
- Transition-oriented

Storyboard에는 high-level transition intent만 기록한다.

실제 retain / change / reset constraint는 Continuity가 Source of Truth다.

### 4.4 Visual Repetition Prevention

본문의 의미가 달라졌는데도 연속 Cut이 같은 시각적 메시지를 반복하는지 검토한다.

필요하면 다음 축을 새롭게 설계할 수 있다.

- dominant visual subject
- camera distance
- viewpoint
- scale
- composition
- lighting concept
- visual rhythm

단순 다양성을 위해 Scripture fact나 Story chronology를 왜곡하지 않는다.

### 4.5 Key Scripture / Explanatory Frame

Storyboard 설계 중 각 beat가 본문 선언 자체가 중심인지, 설명·전환이 중심인지 판단할 수 있다.

실제 직접 인용문이나 narration 문장은 Storyboard에 중복 저장하지 않는다.

### 4.6 Episode-level Rhythm

Multi-Cut Storyboard는 개별 Cut뿐 아니라 전체 흐름을 검토한다.

- establishing / medium / detail 장면의 균형
- Continuity / Transition 배치
- 반복되는 구도나 dominant subject
- visual climax
- rest / breathing frame
- 지나친 분위기 반복

수치 quota를 강제하지 않고 Scripture와 이야기 목적을 우선한다.

## 5. Template 변경 원칙

현재 `templates/storyboard.yaml`의 최소 필드:

- order
- cut_id
- scripture_anchor
- beat
- transition_note

먼저 이 구조로 Production Rules를 운용한다.

새 필드는 다음 조건이 모두 충족될 때만 제안한다.

1. 기존 필드와 Rules만으로 책임을 표현할 수 없다.
2. 새 Source of Truth 책임이 실제로 필요하다.
3. Cut / Continuity와 중복되지 않는다.
4. 기존 데이터와 Template migration 영향이 정당화된다.

사용자 승인 없이 새 구조를 확정하지 않는다.

## 6. 다음 작업

1. 현재 Rules에 이미 있는 Storyboard 관련 규칙 정리
2. 부족한 Production Rule 추가 설계
3. Architecture / Rules 간 책임 충돌 감사
4. Template 변경 필요 여부 최종 판단
5. 사용자 승인
6. 승인 뒤 새 Genesis production 시작 여부 결정

이미지 생성은 이 단계에서 진행하지 않는다.
