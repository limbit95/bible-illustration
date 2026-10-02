# Continuity Rules v1.0

> 상태: **CONFIRMED / 2026-10-02 사용자 승인**
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

Genesis Creation CUT 2→3→4에서 확인된 원초적 수면과 오른쪽 광원 계승은 중요한 실제 사례다.

하지만 이 값은 **전역 Continuity Rule이 아니라 해당 Episode/Cut migration 시 복원해야 할 구체 Continuity 데이터**다.

다른 Episode에 동일한 오른쪽 광원을 적용하지 않는다.

## 9. 단계적 변화와 변화량

연속 Cut은 이전 상태에서 이번 본문이 요구하는 변화만 추가하는 것을 기본으로 한다.

제작 전 인접 Cut 사이에서 최소한 다음을 분리한다.

- retain: 그대로 남아야 하는 구조 / 질감 / 위치 / 방향
- delta: 이번 Cut에서 새로 변해야 하는 요소
- forbidden leap: 아직 등장하면 안 되는 미래 상태

같은 사건을 여러 Cut으로 나눈 경우 변화량이 너무 커서 중간 단계를 건너뛰지 않도록 한다.

예:

~~~text
완전한 흑암
→ 같은 구조에 아주 약한 빛
→ 같은 위치에서 빛의 강도 증가
→ 빛과 어둠의 구분이 분명해짐
~~~

~~~text
원초적 물만 존재
→ 물의 흐름이 모이기 시작
→ 기존 수면보다 낮고 젖은 지형이 처음 드러남
→ 이후에야 땅의 면적과 높이가 증가
~~~

특히 이전 Cut에 없던 큰 지형, 구조물, 인물, 강한 광원, 완성된 공간 상태가
다음 Cut에서 갑자기 등장하면 Scripture상 이유가 없는 한 Continuity mismatch로 본다.

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
