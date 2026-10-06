# Continuity Model v1.0

> 상태: **CONFIRMED / 2026-10-01 사용자 승인**
>
> 선행 조건:
> - **STEP 0-1 — Content Model v1.0 CONFIRMED**
> - **STEP 0-2 — Episode / Cut Model v1.0 CONFIRMED**
>
> 목적: 인접 Cut과 필요 시 Episode 경계 사이에서 무엇이 유지되고, 무엇이 의도적으로 변화하며, 어디서 continuity가 reset되는지를 Canonical 데이터로 정의한다.
>
> 이 단계에서는 Character / Location / Object의 최종 Library 스키마, Provider별 reference 전달 방식, 이미지 Asset 저장 방식은 확정하지 않는다.

## 1. Continuity의 역할

Continuity는 각 Cut의 장면 설명을 반복 저장하는 문서가 아니다.

Continuity의 책임은 다음 질문에 답하는 것이다.

> **이전 장면에서 다음 장면으로 넘어갈 때 무엇이 같은 세계의 연속으로 유지되어야 하고, 무엇이 사건 진행에 따라 달라져야 하는가?**

따라서 Continuity는 **Cut과 Cut 사이의 관계 데이터**로 취급한다.

기본 구조:

```text
Cut A Canonical Scene
        ↓
Continuity Transition
        ↓
Cut B Canonical Scene
```

이미지 생성 결과는 Continuity의 Source of Truth가 아니다.

승인 이미지가 존재하더라도 Canonical Continuity 정의가 별도로 남아 있어야 한다.

## 2. Source of Truth 책임 분리

연속성 정보가 여러 곳에 중복되지 않도록 역할을 분리한다.

### Library — 장기적으로 변하지 않는 Canonical 정의

예:

- 인물의 기본 외형
- 특정 age stage
- 지역의 기본 지형
- 주요 사물의 기본 형태
- 시대별 의복 기준

상세 스키마는 STEP 0-4에서 확정한다.

### Cut — 현재 장면 자체의 정의

예:

- 이 Cut에서 일어나는 사건
- 이 장면에 반드시 필요한 요소
- 금지 요소
- 현재 장면의 핵심 시각 상태

### Continuity — 장면 사이의 유지 / 변화 규칙

예:

- 같은 인물의 의상이 다음 Cut에서도 유지되어야 함
- 오른쪽에 있던 광원이 다음 Cut에서도 오른쪽에 있어야 함
- 인물이 화면 왼쪽에서 오른쪽으로 이동 중임
- 이전 Cut에서 들고 있던 물건을 다음 Cut에서도 들고 있어야 함
- 시간이 밤으로 바뀌므로 조명 상태는 reset됨

즉:

```text
Storyboard = 무엇을 어떤 순서와 beat로 보여줄 것인가
Library = 무엇인가
Cut = 지금 무엇을 보여주는가
Continuity = 다음 장면으로 무엇이 이어지고 무엇이 변하는가
```

Storyboard의 `transition_note`는 관계의 high-level intent를 표현할 수 있지만
실제 transition mode와 retain / change / reset constraint를 소유하지 않는다.

Storyboard planning과 Continuity가 같은 정보를 중복 저장하지 않도록 한다.

## 3. Continuity의 범위

Continuity는 모든 Episode와 모든 Cut 사이에 무조건 동일하게 적용하지 않는다.

세 가지 범위를 구분한다.

### 3.1 Intra-Episode Continuity

같은 Episode 안에서 Storyboard상 연속된 active Cut 사이의 관계다.

기본적으로 모든 인접 active Cut 쌍은 다음 중 하나로 분류되어야 한다.

- `continue`
- `partial_reset`
- `reset`

### 3.2 Cross-Episode Continuity

Episode가 나뉘었다고 해서 continuity가 자동으로 끊기거나 자동으로 이어지는 것은 아니다.

예를 들어 긴 성경 장을 제작 편의상 두 Episode로 나눴지만 사건은 같은 장소와 시간에서 바로 이어질 수 있다.

이 경우 이전 Episode의 마지막 Cut과 다음 Episode의 첫 Cut 사이에 **명시적인 continuity transition**을 둔다.

반대로 Canonical Episode Sequence상 바로 다음 Episode라도 시간·장소·사건이 크게 바뀐다면 `reset`이다.

**Episode 순서 자체로 continuity를 추론하지 않는다.**

### 3.3 No Continuity Requirement

서로 비교할 필요가 없는 Cut/Episode 사이에는 continuity 관계를 만들지 않는다.

Canonical Sequence 전체를 거대한 연속 장면처럼 취급하지 않는다.

## 4. Continuity Transition Mode

인접 장면의 관계는 세 가지 모드로 구분한다.

### continue

같은 장면·공간·시간 흐름이 이어진다.

단, `continue`라고 해서 모든 시각 요소를 자동 상속하지 않는다.

**Segment baseline과 명시적인 retain constraint만 유지 의무를 가진다.** 그 밖의 기록되지 않은 값은 생성 자유도를 유지한다.

예:

```text
Cut A
인물이 길의 왼쪽에서 이동을 시작
↓ continue
Cut B
같은 이동 방향과 공간 관계를 유지하며 중앙으로 진행
```

### partial_reset

일부 continuity 축은 유지되지만 일부는 새로 설정된다.

예:

- 같은 인물이지만 시간이 며칠 뒤로 이동
- 같은 장소지만 날씨가 바뀜
- 같은 사건이지만 카메라 축을 의도적으로 변경
- 인물 외형은 유지하지만 의복이 사건상 변경됨

유지되는 항목과 reset되는 항목을 명시한다.

### reset

새로운 시간·장소·사건 맥락으로 전환되어 이전 장면의 상태를 기본적으로 상속하지 않는다.

단, 반복 등장 인물의 Canonical 외형처럼 Library에서 오는 기본 정의는 별개다.

`reset` 이후에도 반드시 유지해야 하는 요소가 있다면 명시적으로 다시 참조한다.

## 5. Continuity 데이터 영역

연속성은 최소 다음 영역으로 나눈다.

### 5.1 World

장면의 물리적 세계 상태.

예:

- location
- terrain
- architecture
- environment
- weather
- time of day
- 계절 또는 장기 시간 흐름

### 5.2 Character

등장 인물의 장면 간 상태.

예:

- character reference
- age stage
- 외형 상태
- costume
- accessories
- 상처 / 먼지 / 젖음 등 physical condition
- 들고 있는 물건
- 등장 여부

인물의 기본 외형 자체는 Library가 Source of Truth이며 Continuity에는 **해당 장면에서 이어져야 하는 상태**만 기록한다.

### 5.3 Object

장면에서 중요한 사물의 상태.

예:

- 위치
- 소유자
- 열림 / 닫힘
- 파손 / 완전
- 비어 있음 / 채워짐
- 사건 진행에 따른 상태 변화

### 5.4 Spatial

공간 방향과 상대 위치.

예:

- 인물이 화면의 어느 쪽에 있는가
- 인물 A와 B의 상대 위치
- 이동 방향
- 건물 / 산 / 강 등의 방향
- 화면 좌우가 바뀌면 안 되는 중요한 spatial axis

단순 화면 구도보다 **장면 연결성을 깨뜨릴 수 있는 공간 관계**를 우선 기록한다.

### 5.5 Event State

사건의 진행 상태.

예:

- 이전 Cut에서 시작된 행동이 어디까지 진행되었는지
- 누가 무엇을 들고 이동하고 있는지
- 어떤 물체가 이미 파괴되었는지
- 군중이 모이는 과정인지 흩어지는 과정인지

### 5.6 Visual

물리적 세계 상태와 별개로 컷 연결에 중요한 시각 규칙.

예:

- primary light source 방향
- 광원의 성격
- 핵심 색조
- camera axis
- 시선 방향
- 필요 시 framing continuity

모든 카메라 수치를 고정하려는 목적은 아니다.

다음 Cut에서 바뀌었을 때 관객에게 장면이 뒤집힌 것처럼 느껴지는 요소만 Canonical Continuity로 관리한다.

## 6. Continuity Segment

모든 Cut 전환마다 동일한 세계 상태를 반복 기록하지 않기 위해 Episode 안에 **Continuity Segment** 개념을 둔다.

Continuity Segment는 같은 기본 시간·장소·장면 맥락을 공유하는 **연속된 active Cut들의 범위**다.

이는 전역 식별자를 가진 독립 콘텐츠 엔터티가 아니다.

Episode 내부 continuity 문서에서 사용하는 로컬 구조다.

개념 예:

```yaml
episode_id: BOOK-STORY-01

segments:
  - key: S01
    start_cut_id: BOOK-STORY-01-C01
    end_cut_id: BOOK-STORY-01-C03

    baseline:
      world:
        location_ref: LOC-EXAMPLE
      visual:
        camera_axis: forward

transitions:
  - from: BOOK-STORY-01-C01
    to: BOOK-STORY-01-C02
    mode: continue

  - from: BOOK-STORY-01-C02
    to: BOOK-STORY-01-C03
    mode: continue
    change:
      - character.position
```

`S01` 같은 segment key는 Episode 내부에서만 사용하며 Content Model의 영구 ID로 취급하지 않는다.

Segment에 Cut 목록이나 Cut 순서를 별도로 복제하지 않는다.

`start_cut_id`와 `end_cut_id` 사이에 포함되는 Cut은 **Storyboard의 현재 active Cut 순서**를 기준으로 계산한다.

따라서 Storyboard가 Cut order의 Source of Truth라는 STEP 0-1 원칙을 유지한다. Segment 내부에 새 Cut이 삽입되면 별도 목록 동기화 없이 해당 범위에 자연스럽게 포함된다.

### Transition의 저장 위치

Cut 간 transition은 특정 Segment 안에 소유시키지 않고 **Episode continuity의 단일 transition 목록**에서 관리한다.

이렇게 하면:

- 같은 Segment 내부 전환
- 서로 다른 Segment 사이의 전환
- partial reset
- full reset

을 모두 같은 규칙으로 표현할 수 있다.

모든 인접 active Cut 쌍에는 transition이 정확히 하나만 존재해야 한다.

## 7. Baseline과 Transition Delta

Continuity 데이터는 **전체 상태 복사**보다 baseline + 변화량(delta)을 우선한다.

### Baseline

해당 Continuity Segment에서 기본적으로 유지되는 중요한 상태를 정의한다.

예:

```yaml
baseline:
  world:
    location_ref: ...
    weather: clear
  spatial:
    movement_direction: left-to-right
```

### Transition Delta

Cut 사이에서 실제로 달라지는 부분만 기록한다.

개념 예:

```yaml
from: C03
to: C04
mode: continue

retain:
  - world.location
  - spatial.movement_direction

change:
  - path: character.CHR-XXX.position
    from: left
    to: center

reason: 인물이 장면 중앙으로 이동하는 사건 진행
```

단, baseline에 이미 유지가 명확한 항목을 모든 transition의 `retain`에 반복 작성할 필요는 없다.

`retain`은 특히 중요해서 명시적으로 강조할 필요가 있는 continuity constraint에 사용한다.

## 8. 값이 없는 것의 의미

Continuity 문서에 특정 항목이 기록되어 있지 않다고 해서 “이전 Cut과 반드시 같아야 한다”는 뜻은 아니다.

기본 원칙:

> **기록 없음 = continuity constraint 없음**

단, 현재 Segment의 baseline에 정의된 값은 segment 내에서 기본 continuity constraint로 적용된다.

이 원칙은 불필요하게 모든 시각 요소를 고정하여 이미지 생성 자유도를 잃는 것을 막기 위한 것이다.

## 9. Reset 규칙

다음 상황은 continuity reset 또는 partial reset 후보가 된다.

- 장소 이동
- 의미 있는 시간 점프
- 새로운 사건 시작
- 꿈 / 환상 / 회상 진입 또는 종료
- 날씨나 계절이 크게 변화
- 인물의 age stage 변화
- 의도적인 시각 언어 전환
- Episode 분할 지점에서 실제 사건 맥락도 함께 바뀜

하지만 위 조건이 발생했다고 자동으로 full reset하지 않는다.

예:

- 장소가 바뀌어도 같은 인물의 의상과 소지품은 유지될 수 있음
- 시간이 지나도 같은 건물 구조는 유지될 수 있음
- Episode가 바뀌어도 동일 사건 직후라면 대부분의 continuity를 이어갈 수 있음

따라서 reset은 **축별로 판단**한다.

## 10. Cross-Episode Boundary

연속된 두 Episode 사이에서 실제 장면 continuity가 중요하면 동일한 transition 개념을 사용한다.

개념 예:

```yaml
episode_boundary:
  from_cut: GEN-CREATION-01-C07
  to_cut: GEN-CREATION-02-C01
  mode: continue

  retain:
    - world.environment
    - visual.primary_light_direction
```

Cross-Episode Continuity는 Canonical Episode Sequence에 있다고 자동 생성하지 않는다.

명시적 연결이 있을 때만 관리한다.

동일한 경계 transition을 두 Episode에 중복 저장하지 않는다.

개념적으로 **도착하는 Episode가 자신의 incoming boundary를 소유**하도록 한다.

즉 이전 Episode의 마지막 Cut을 참조하되, 실제 transition 정의는 다음 Episode 쪽 continuity 데이터에서 한 번만 관리한다. 최종 물리 파일 배치는 STEP 0 전체 구조를 확정할 때 결정한다.

## 11. Continuity와 Revision

Continuity는 이미지 생성에 영향을 주는 Canonical 정의의 일부다.

따라서 이미 승인된 Cut에 대해 다음과 같은 continuity constraint가 의미 있게 바뀌면 영향을 받는 Cut revision을 다시 검토한다.

예:

- 광원 방향 변경
- 인물 위치 방향 변경
- 의상 유지 규칙 변경
- 사건 진행 상태 변경
- 중요한 object 상태 변경

원칙:

1. Continuity 자체에 별도 전역 revision 체계를 먼저 만들지 않는다.
2. Git 변경 이력은 문서 변경 기록을 보존한다.
3. 생성 결과를 달라지게 할 정도의 Continuity 변경은 영향을 받는 Cut의 revision 증가 대상으로 본다.
4. Episode 구조나 active Cut 구성이 바뀌면 STEP 0-2의 Episode revision 규칙도 적용한다.

## 12. Storyboard Relation Intent와 Continuity 확정

Storyboard 단계에서 인접 Cut을 Continuity-oriented 또는 Transition-oriented로 먼저 판단할 수 있다.

이 판단은 제작 planning이며 별도 Canonical relation enum을 추가하지 않는다.

Canonical Continuity를 작성할 때는 해당 의도를 참고해 기존 세 mode 중 하나를 결정한다.

- Continuity-oriented → `continue` 또는 `partial_reset`
- Transition-oriented → `partial_reset` 또는 `reset`

Storyboard의 `transition_note`가 구체 retain / change / reset 데이터를 대신하지 않는다.

## 13. Continuity와 Definition Approval

STEP 0-2에서 Cut / Episode 정의 승인과 이미지 제작 완료를 분리했다.

Continuity는 **정의 승인 측**에 포함된다.

### Cut 정의 승인 추가 조건

첫 active Cut을 제외하고, 이전 장면과 continuity가 필요한 Cut은 다음 중 하나가 해결되어야 한다.

- 유효한 `continue` transition
- 유효한 `partial_reset` transition
- 의도적인 `reset`

즉 장면 사이 관계를 미결정 상태로 둔 채 Cut 정의를 최종 승인하지 않는다.

### Episode 정의 승인 추가 조건

Episode의 모든 인접 active Cut 쌍에 대해 continuity mode가 결정되어야 한다.

Cross-Episode Continuity가 필요한 경우 Episode 경계 transition도 정의되어야 한다.

## 14. Continuity와 Generated Asset 검토

이미지 생성 후에는 Canonical Continuity 정의와 결과를 비교한다.

예:

```text
Canonical:
primary light source = right

Generated Asset:
primary light source = left

판정:
continuity mismatch
```

이 경우 Canonical Continuity 데이터를 생성 이미지에 맞춰 자동으로 수정하지 않는다.

기본 처리:

- 생성 결과를 수정 / 재생성하거나
- Canonical 정의 자체가 잘못되었다고 판단되는 경우에만 정식 revision 절차를 거쳐 Continuity/Cut 정의를 변경한다.

즉 **AI가 우연히 만든 결과가 Canonical 세계관을 역으로 결정하지 않는다.**

## 15. Continuity 불변 조건 후보

1. Continuity는 Cut 자체가 아니라 Cut 사이의 유지·변화 관계를 정의한다.
2. Library의 Canonical Asset 정의를 Continuity에 복제하지 않는다.
3. Cut의 전체 Scene Specification을 Continuity에 복제하지 않는다.
4. 같은 장면 맥락이 이어지는 구간은 Continuity Segment로 묶을 수 있다.
5. Segment baseline은 공통 continuity constraint를 정의한다.
6. Segment는 Cut 순서를 복제하지 않고 Storyboard의 active Cut 순서를 참조한다.
7. 모든 인접 active Cut 쌍의 transition은 Episode continuity의 단일 목록에서 정확히 한 번 정의한다.
8. transition은 baseline에서 달라지는 변화와 중요한 retain 조건을 기록한다.
9. `continue`라도 Segment baseline 또는 명시적 retain에 없는 값까지 자동 상속하지 않는다.
10. 기록되지 않은 값은 기본적으로 continuity constraint가 아니다.
11. 모든 인접 active Cut 쌍은 `continue / partial_reset / reset` 중 하나로 분류된다.
12. Episode 경계는 continuity를 자동으로 끊거나 자동으로 이어주지 않는다.
13. Cross-Episode Continuity가 필요하면 명시적으로 정의하며 동일 경계는 한 번만 저장한다.
14. 의미 있는 Continuity 변경은 영향을 받는 Cut revision 재검토 대상이다.
15. Generated Asset은 Continuity의 Source of Truth가 아니다.
16. 생성 결과와 Canonical Continuity가 충돌하면 기본적으로 생성 결과를 수정한다.
17. Continuity 검토는 Cut/Episode Definition Approval의 일부다.

## 16. STEP 0-3에서 의도적으로 미확정하는 항목

다음은 이후 STEP에서 정한다.

- Character / Location / Object의 실제 Library ID와 필드 구조
- Continuity에서 Library 자산을 참조하는 최종 문법
- Provider에 continuity reference image를 전달하는 방식
- reference strength / image weight 같은 provider 설정
- Generated Asset의 continuity 평가 점수 체계
- Markdown / YAML 최종 물리 저장 포맷
- Continuity Segment의 최종 파일 배치
- 웹사이트 표시용 transition 효과

## 17. STEP 0-3 검토 포인트

사용자 검토가 필요한 핵심 항목:

- Continuity를 Cut 사이 관계 데이터로 두는 원칙
- World / Character / Object / Spatial / Event State / Visual 영역 구분
- Episode 내부 Continuity Segment 사용과 Storyboard order 비중복 원칙
- 모든 인접 Cut transition을 Episode-level 단일 목록에서 관리하는 원칙
- baseline + transition delta 방식
- `continue / partial_reset / reset` 3단계 transition mode
- 기록 없음은 constraint 없음으로 해석하는 원칙
- Episode 경계 continuity를 자동 추론하지 않고 incoming boundary를 한 곳에서만 관리하는 원칙
- 의미 있는 Continuity 변경 시 영향을 받는 Cut revision을 다시 검토하는 원칙
- Continuity를 Cut/Episode Definition Approval 조건에 포함하는 원칙
- Generated Asset보다 Canonical Continuity 정의를 우선하는 원칙

위 항목은 2026-10-01 사용자 승인으로 확정되었다.

**STEP 0-3 — Continuity Model: COMPLETED / CONFIRMED**

다음 작업은 **STEP 0-4 — Library Model**이다.


---
