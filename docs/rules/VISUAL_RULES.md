# Visual Rules v1.2

> 상태: **CONFIRMED / 2026-10-06 pre-production hardening**
>
> 목적: 장면별 유연성을 유지하면서 프로젝트 전체가 공유할 시각적 품질과 표현 기준을 정의한다.

## 1. 상위 시각 방향

프로젝트가 지향하는 기본 시각 언어:

- 고품질 2D 디지털 일러스트
- 회화적 질감
- 설득력 있는 형태·원근·깊이
- 성경 역사 서사에 어울리는 공간감
- 인물뿐 아니라 배경과 환경도 실제 세계처럼 충분히 구성
- 캐릭터 중심 장면과 넓은 환경 장면을 본문 목적에 맞게 선택

피하는 방향:

- 실사 사진처럼 보이는 과도한 포토리얼리즘
- 유치한 아동 만화풍
- SD / 과도한 chibi 비율
- 인물과 동물의 지나친 카툰화
- 장면의 역사적 분위기를 약화하는 현대 광고·게임 UI 같은 표현

정확한 Style 정의는 Library의 Visual Style Entity이 Source of Truth다.

## 2. 고정하지 않는 요소

프로젝트 전체에서 다음을 하나의 값으로 강제하지 않는다.

- 조명
- 색감
- 날씨
- 분위기
- 카메라 각도
- 렌즈감
- 화면 구성
- 지형
- 건축
- 회화성과 사실성의 세부 비율

이 값들은 장면의 Scripture, Location, Environment, Continuity에 따라 달라질 수 있다.

## 3. Reference의 범위

레퍼런스는 목적을 구분한다.

- 글로벌 품질 / Style Reference
- 특정 Cut의 장면 / Composition Reference
- Character Reference
- Location / Object / Costume Reference
- Continuity Reference
- Historical Reference

특정 Cut에서 잘 나온 색감·구도·광원을 시리즈 전체의 절대 기준으로 자동 승격하지 않는다.

## 4. 카메라와 구도

카메라는 장면의 이야기 목적을 전달해야 한다.

사용 가능한 예:

- 초광각 전경
- 넓은 풍경
- 전신
- 중경
- 상반신
- 클로즈업
- 높은 시점
- 낮은 시점
- 인물 뒤에서 보는 구도

“다양하게 보여주기 위해” Continuity를 깨뜨리지는 않는다.

같은 사건의 직후 Cut에서는 Continuity가 요구하는 카메라·공간 방향이 다양성보다 우선한다.

## 5. 시각적 초점

한 Cut에는 가능한 한 하나의 주요 사건 또는 하나의 시각적 초점을 둔다.

많은 사건을 한 화면에 압축해 모든 요소의 의미가 약해지는 것을 피한다.

## 6. 환경의 깊이

배경은 단순 장식이 아니다.

장소와 시대가 중요한 경우:

- 전경 / 중경 / 후경
- 지형의 높낮이
- 건축과 사물의 스케일
- 대기 원근
- 이동 가능한 공간 구조

등을 통해 장면이 실제 장소처럼 느껴지도록 한다.

## 7. 폭력·전쟁·심판

본문에 있는 전쟁·죽음·심판을 사건 자체에서 삭제하지 않는다.

다만 이야기 전달에 필요하지 않은 잔혹한 신체 훼손을 시각적 볼거리로 강조하지 않는다.

사건의 의미와 결과가 이해되는 수준을 우선한다.

## 8. 초자연 장면

하나님·천사·초자연 현상은 `SCRIPTURE_RULES.md`의 본문 우선 원칙을 따른다.

시각적 장엄함을 높이기 위해 본문에 없는 육체적 형상이나 우주 구조를 임의 확정하지 않는다.

## 9. 이미지 내 텍스트

Production Master에는 성경 구절, 장·절, 내레이션, 챕터 제목, UI text를 기본적으로 영구 합성하지 않는다.

상세 규칙은 `TEXT_AND_COPYRIGHT.md`를 따른다.

## 10. 품질 검토

visual_quality와 technical_integrity를 구분한다.

### Visual Quality

- 구도가 장면 목적을 전달하는가
- 형태와 공간이 설득력 있는가
- 인물과 환경의 완성도가 충분한가
- 프로젝트 Style과 심하게 이탈하지 않는가

### Technical Integrity

- 비정상적인 신체 구조
- 사물의 깨진 형태
- 부자연스러운 반복
- 원치 않는 문자 artifact
- 해상도 / crop 문제
- 기타 생성 오류

기술적으로 깨끗하더라도 Scripture / Historical / Continuity 기준에 실패하면 대표 Asset 후보가 될 수 없다.


## 11. Episode-level Visual Direction 책임

프로젝트 전체 Visual Rules와 특정 Episode의 구체 미술 방향을 구분하되,
v1에서는 별도 `Episode Visual Profile` entity나 working guide를 Canonical Source of Truth로 만들지 않는다.

책임은 다음처럼 분해한다.

- 여러 Episode에서 재사용할 시각 언어 identity → **Library Visual Style Entity**
- 한 Episode/Segment에서 유지·진행되는 팔레트·질감·조명 상태·공간 방향 → **Continuity Segment baseline.visual**
- 특정 Cut의 카메라·구도·dominant subject·장면별 강조 → **Cut Canonical Scene Specification**
- 모델·해상도·reference weight·Provider 실행용 aspect ratio 등 → **Generation Profile / Run snapshot**
- 프로젝트 전체의 작품적 규칙으로 승격된 값 → **Visual Rules**

특정 Episode에서 선호한 색감·질감·광원·구도를 성경 전체 기본값으로 자동 승격하지 않는다.

working note나 대화에서 나온 장기 유지 결정은 위 정식 owner 중 하나에 반영해야 하며,
임시 working guide 자체를 Canonical 기준으로 남기지 않는다.

폐기된 production iteration의 구체 visual direction은 새 Episode의 기본값으로 계승하지 않는다.

## 12. Storyboard 단계의 Visual Repetition Prevention

시각적 반복 문제는 생성 결과를 본 뒤에만 발견하는 것이 아니라 Storyboard 단계에서도 먼저 검토한다.

연속 Cut의 본문 의미가 달라지는데도 다음이 계속 동일하면 repetition concern으로 본다.

- dominant visual subject
- camera distance
- viewpoint
- scale
- composition
- lighting concept
- visual rhythm

반복 자체가 잘못은 아니다.

같은 사건의 점진적 변화처럼 continuity가 의미의 핵심이면 반복되는 시각 구조가 오히려 필요할 수 있다.

반대로 본문의 핵심이 달라졌는데 이전 구도와 subject를 습관적으로 유지해서 장면 간 의미 차이가 약해지면
Transition-oriented relation이나 새로운 visual treatment를 검토한다.

**다양성 자체를 목표로 삼지 않는다.**
본문 의미의 차이를 더 정확하게 전달하기 위해 필요한 경우에만 시각 축을 변화시킨다.

구체 카메라·구도·조명 값은 Storyboard에 장황하게 저장하지 않고
실제 Cut Canonical Scene과 필요 시 Continuity에서 구체화한다.

## 13. Episode-level Visual Rhythm Review

여러 Cut을 가진 Episode는 개별 Cut의 완성도뿐 아니라 전체 시퀀스의 시각 리듬도 검토한다.

검토 항목:

- 넓은 establishing 장면, 중간 거리 장면, detail 장면이 본문 목적에 맞게 배치되는가
- 동일한 카메라 거리나 viewpoint가 이유 없이 길게 반복되는가
- Continuity 구간이 필요한 만큼 이어지고 적절한 지점에서 Transition이 발생하는가
- visual climax가 본문의 중요도와 맞는가
- 강한 장면이 연속될 때 rest / breathing frame이 필요한가
- 동일한 조명·팔레트·분위기가 본문 변화와 무관하게 관성적으로 계속되는가
- Episode의 시작과 끝이 시각적으로 같은 강도로 평평하게 느껴지지 않는가

다음과 같은 기계적 quota는 만들지 않는다.

- N Cut마다 반드시 close-up
- establishing / medium / detail을 동일 비율로 배치
- 일정 간격마다 palette 변경

Storyboard rhythm은 항상 Scripture와 production intent를 우선한다.
