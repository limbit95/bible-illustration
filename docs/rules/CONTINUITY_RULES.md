# Continuity Rules v1.1

> 상태: **CONFIRMED / 2026-10-06 Storyboard Production Rules refinement**
>
> 목적: 연속된 Cut이 같은 사건과 세계의 다음 순간처럼 느껴지도록 유지·변화·reset 판단 규칙을 정의한다.
>
> 데이터 구조 자체는 `docs/architecture/CONTINUITY_MODEL.md`가 Source of Truth다.

## 1. Continuity의 기본 질문

각 인접 Cut 사이에서 다음을 확인한다.

- 무엇이 그대로 유지되어야 하는가
- 무엇이 변해야 하는가
- 무엇은 더 이상 제약하지 않아도 되는가
- 변화가 본문과 사건 흐름에 근거하는가

## 2. 동일 사건의 직후 장면

같은 사건을 여러 Cut으로 나눈 경우 인접 Cut은 서로 독립된 새 그림처럼 보이지 않도록 한다.

필요에 따라 유지할 요소:

- 광원의 위치와 방향
- 카메라 시점과 높이
- 주요 배경 구조
- 수면 / 지형 / 건축의 방향성
- 인물의 위치와 진행 방향
- 사물의 위치와 상태
- 색조와 분위기의 흐름
- 날씨와 시간 상태
- 사건 변화의 진행 방향

다음 Cut은 **동일한 공간이 다음 순간으로 변화한 것처럼** 느껴져야 한다.

## 3. Transition Mode

Architecture의 세 mode를 따른다.

### continue

같은 흐름을 유지한다.

Segment baseline과 명시적 retain constraint가 적용되며, 명시되지 않은 모든 visual 값을 기계적으로 복제하는 뜻은 아니다.

### partial_reset

일부 축은 유지하고 일부 축은 새 상태로 전환한다.

무엇을 유지하고 reset하는지 명시한다.

### reset

이전 장면 상태를 기본적으로 상속하지 않는다.

단, Character / Location / Object 등 Library identity는 별도 참조가 유지되는 한 사라지는 것이 아니다.

## 4. Scene Change와 Continuity Break 구분

장면이 달라졌다고 무조건 reset하지 않는다.

다음 질문으로 판단한다.

- 시간이 얼마나 지났는가
- 장소가 실제로 이동했는가
- 사건이 같은 흐름인가
- 이전 상태가 다음 장면의 의미에 필요한가
- 동일 인물·사물·환경의 상태가 이어지는가

Cross-Episode라도 같은 사건이 이어진다면 incoming boundary continuity를 명시할 수 있다.

## 5. Library와 Continuity의 경계

Library는 identity와 장기 정의를 소유한다.

Continuity는 사건 진행 중의 상태를 소유한다.

예:

- 모세의 기본 외형 → Character Library
- 현재 옷에 먼지가 묻음 → Cut / Continuity
- 지팡이의 Canonical 형태 → Object Library
- 현재 지팡이를 오른손에 들고 있음 → Cut / Continuity
- 시내산의 장소 identity → Location Library
- 현재 밤이고 연기가 퍼짐 → Cut / Continuity

## 6. Reference와 Continuity

이전 Cut Asset을 Continuity Reference로 사용할 수 있다.

그러나 이미지의 우연한 요소 전체를 Canonical Continuity로 승격하지 않는다.

- Canonical Continuity가 먼저다.
- Reference Asset은 그 constraint를 전달하는 보조 입력이다.
- Provider가 Reference에서 잘못된 요소를 반복해도 그것이 Canonical 사실이 되지 않는다.

## 7. 장면별 Style과 Continuity

같은 Episode라도 조명·색감·카메라는 사건에 따라 변할 수 있다.

변화가 허용되는지 여부는 “전역 Style 동일성”이 아니라 Transition constraint로 판단한다.

특정 Cut의 색감이나 광원을 전 시리즈에 자동 복제하지 않는다.

## 8. 이전 제작 Anchor의 취급

폐기된 production iteration의 reference image나 장면별 continuity 값은 새 Episode의 Canonical 기준으로 자동 복원하지 않는다.

과거 결과에서 일반화된 원칙만 현재 Rules로 유지하며, 새 제작의 retain / change / reset 값은 새 Storyboard와 Cut 정의를 기준으로 다시 결정한다.

## 9. 단계적 변화와 변화량

연속 Cut은 이전 상태에서 이번 본문이 요구하는 변화만 추가하는 것을 기본으로 한다.

제작 전 인접 Cut 사이에서 최소한 다음을 분리한다.

- retain: 그대로 남아야 하는 구조 / 상태 / 위치 / 방향
- delta: 이번 Cut에서 새로 변해야 하는 요소
- forbidden leap: 아직 등장하면 안 되는 미래 상태

같은 사건을 여러 Cut으로 나눈 경우 변화량이 너무 커서 의미 있는 중간 단계를 건너뛰지 않도록 한다.

반대로 본문상 변화가 거의 없고 별도 시각적 의미도 없다면 동일 상태를 여러 Cut으로 불필요하게 세분화하지 않는다.

이전 Cut에 없던 중요한 지형, 구조물, 인물, 광원, 사건 결과가 다음 Cut에서 갑자기 등장하면 Scripture상 근거가 있는지 먼저 검토한다.

## 10. Continuity Review

Result를 검토할 때:

- retain 대상이 유지됐는가
- reset 대상이 불필요하게 끌려오지 않았는가
- 인물·사물의 위치와 상태가 사건 흐름에 맞는가
- 광원 / 공간 / 카메라가 이유 없이 반전되지 않았는가
- 후대 요소가 Continuity를 이유로 선행 묘사되지 않았는가

중요한 Continuity constraint 위반은 시각적 완성도가 높더라도 rejected 근거가 된다.


## 11. 승인된 직전 장면과 제작 순서

연속 시퀀스에서는 실제 제작 순서 자체를 Continuity 도구로 사용한다.

기본 흐름:

- 현재 장면 생성
- 사용자 승인 또는 수정
- 승인된 장면을 다음 Cut의 visual continuity reference로 사용
- 다음 본문이 요구하는 DELTA만 추가

한 구절이 여러 Cut으로 분리되는 경우에도 같은 원칙을 적용한다.

직전 승인 이미지가 강한 시각 Anchor 역할을 하더라도
이미지의 모든 세부가 Canonical Continuity가 되는 것은 아니다.

다음 장면에 계승할 것은 명시적으로 구분한다.

- retain: 계속 유지
- delta: 이번 Cut에서 변화
- discard: 이전 이미지의 우연한 요소 또는 오류
- forbidden leap: 아직 등장하면 안 되는 상태

사용자가 장면 연결이 어색하다고 판단하면
개별 이미지의 미적 완성도가 높더라도 continuity concern으로 보고 재생성 또는 중간 Cut 분할을 검토한다.


## 12. Continuity와 Transition의 연출 판단

모든 인접 Cut을 시각적으로 강하게 연결하는 것을 목표로 하지 않는다.

먼저 두 Cut의 Story 관계를 판단한다.

### 연결 연출을 우선할 때

- 동일 사건의 연속 단계
- 동일 공간의 점진적 상태 변화
- 이전 상태를 봐야 다음 상태의 의미가 살아나는 경우
- 중간 단계 생략이 Scripture 의미 또는 사건 진행을 왜곡하는 경우

이때는 `continue`를 우선하고,
필요한 일부 축만 바뀌면 `partial_reset`을 사용할 수 있다.

### 전환 연출을 우선할 때

- 새로운 사건이나 창조 국면이 시작되는 경우
- 핵심 visual subject가 바뀌는 경우
- 이전 분위기 반복이 본문 의미를 약하게 만드는 경우
- 새로운 시점, 거리, 스케일, 색감, 분위기가 본문 전달에 더 적합한 경우

이때는 `partial_reset` 또는 `reset`을 사용할 수 있다.

전환은 서사적 단절을 뜻하지 않는다.
Scripture상 시간·사건·인과는 유지하되 시각 언어를 새롭게 시작할 수 있다는 뜻이다.

### Assistant 판단

새 장면 전에 필요하면 다음을 확인한다.

- 동일 사건의 다음 순간인가
- 무엇을 반드시 유지해야 하는가
- 무엇을 유지하면 오히려 반복적이고 부자연스러운가
- 새 장면의 핵심 subject는 무엇인가
- Continuity가 중요한가, Visual Reset이 더 중요한가

사용자가 Relation 방식을 지정하지 않아도 Assistant가 기본 판단한다.


## 13. Storyboard Relation Planning과 Canonical Continuity 경계

Storyboard 단계에서는 인접 Cut의 관계를 먼저 **Continuity-oriented / Transition-oriented** 관점으로 판단할 수 있다.

이 판단은 제작 방향을 정하는 planning layer이며 별도 Canonical enum을 만들지 않는다.

### Storyboard가 기록할 수 있는 것

`transition_note`에는 다음처럼 high-level intent를 기록할 수 있다.

- 동일 사건의 직접적인 다음 단계이므로 연결감을 우선한다.
- 새로운 사건 또는 핵심 subject가 시작되므로 시각적 전환을 허용한다.
- 이전 장면과 Story chronology는 이어지지만 구도와 스케일을 새로 설계한다.

### Storyboard가 소유하지 않는 것

다음은 Storyboard에 중복 저장하지 않는다.

- 정확한 transition mode
- retain path 목록
- change path와 from / to 값
- reset path 목록
- Segment baseline
- 구체적인 continuity constraint

위 데이터의 Source of Truth는 `continuity.yaml`이다.

### Canonical mapping

Storyboard의 high-level relation intent를 실제 Canonical 정의로 옮길 때:

- Continuity-oriented → `continue` 또는 `partial_reset`
- Transition-oriented → `partial_reset` 또는 `reset`

중 하나를 Scripture / Cut / Continuity 기준으로 결정한다.

`transition_note`와 Continuity가 충돌하면 상세 Canonical constraint를 소유하는 Continuity를 우선하되,
Storyboard의 이야기 의도 자체가 달라졌다면 Storyboard도 함께 수정한다.
