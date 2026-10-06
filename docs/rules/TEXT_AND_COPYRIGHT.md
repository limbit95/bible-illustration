# Text and Copyright Rules v1.2

> 상태: **CONFIRMED / 2026-10-06 Storyboard Production Rules refinement**
>
> 목적: 성경 직접 인용, 자체 내레이션, 이미지 내 텍스트, 외부 Reference와 이미지 권리를 분리하여 관리하는 규칙을 정의한다.

## 1. 직접 인용과 내레이션을 구분한다

### 직접 인용

성경 문구를 그대로 인용하는 텍스트.

### 자체 내레이션

시간 흐름, 장소 이동, 사건 배경, 이해 보조를 위해 프로젝트가 작성하는 설명 문장.

내레이션을 성경 직접 인용처럼 보이게 만들지 않는다.

## 2. 공개 직접 인용의 기본 역본

현재 프로젝트의 기본 제작 규칙은 공개 직접 인용에 **개역한글**을 사용하는 것이다.

직접 인용 시:

- 문구를 임의 현대화·축약·변형하지 않는다.
- 실제 장·절 범위를 확인한다.
- 역본명을 함께 관리한다.

다른 역본을 예외적으로 사용할 필요가 생기면 해당 사용 이유와 표시를 별도로 검토한다.

## 3. 이해하기 어려운 문장

직접 인용문 자체를 쉬운 현대어로 고치지 않는다.

현대적인 설명이 필요하면 별도의 자체 내레이션을 작성한다.

직접 인용과 내레이션은 데이터와 시각적 표현에서 구분한다.

## 4. Production Master에 텍스트를 bake-in하지 않는다

AI 이미지 생성 단계와 Production Master에는 기본적으로 다음을 직접 생성·영구 합성하지 않는다.

- 성경 직접 인용
- 장·절
- 역본명
- 내레이션
- 말풍선
- 챕터 제목
- UI caption
- 효과음

이 정보는 사이트 HTML/CSS 또는 편집 가능한 별도 presentation layer에서 관리한다.

이미지 자체의 장면 내용으로 글자가 반드시 존재해야 하는 경우는 Cut Canonical Scene이 별도로 결정한다.

## 5. 텍스트 데이터 분리

사이트 데이터는 최소한 다음 의미를 분리할 수 있어야 한다.

- illustration
- quote
- verse
- translation
- narration

실제 사이트 schema는 별도 구현 단계에서 정하지만 의미는 섞지 않는다.

## 6. 번역본 교차 확인

제작 검토를 위해 다른 번역본이나 원문을 참고할 수 있다.

예:

- 개역개정
- 새번역
- 새한글성경
- 히브리어 / 아람어 원문

그러나 교차 확인용 자료를 자동으로 공개 직접 인용문으로 사용하지 않는다.

## 7. 공개 전 권리 조건 재확인

과거 테스트 문서는 2026-10-01 당시의 개역한글 관련 이용 조건 확인 내용을 기록하고 있다.

이 기록을 영구적인 법적 결론으로 간주하지 않는다.

정식 공개 직전에는 사용하려는 번역본과 인용 방식에 대한 **최신 이용 조건, 출처 표시 요구, 동일성 유지 조건 등을 다시 확인**한다.

## 8. 외부 Reference 권리

외부 이미지·연구자료의 provenance와 저장 권리를 구분한다.

권리가 불명확한 자료는 binary를 저장소에 복제하지 않고 External Reference Record로 관리한다.

- source URL
- source title
- 작성자 / 기관
- 조회 시점
- 설명
- 알려진 rights 상태

를 가능한 범위에서 기록한다.

## 9. Asset Import

외부 자료를 Canonical Asset으로 import하려면 장기 저장 권리가 있다고 판단할 근거가 있어야 한다.

public_domain / licensed / permission_granted 등을 추측하지 않는다.

권리 상태가 unknown / restricted이면 External Reference Record를 기본으로 한다.

## 10. 파생 홍보물

SNS 카드 등 텍스트가 합성된 파생물이 필요할 수 있다.

그 경우에도:

- 텍스트 없는 Production Master
- 필요한 경우 편집 가능한 원본
- 배포용 파생본

을 구분한다.

배포용 파생본이 Production Master를 대체하지 않는다.


## 11. Presentation Text Selection

Presentation layer에서 직접 인용과 내레이션을 동시에 기본 노출하지 않는다.

장면별로 먼저 다음 중 하나를 선택한다.

### Key Scripture Frame

본문의 선언·명령·언약·핵심 사건 문구처럼 그 구절 자체가 장면의 중심인 경우:

- 성경 직접 인용을 우선한다.
- 같은 화면에 설명용 내레이션을 덧붙이지 않는 것을 기본으로 한다.
- 장절 표기는 작고 보조적인 위계로 둔다.
- 직접 인용문은 원문 의미를 바꾸는 요약문으로 대체하지 않는다.

### Explanatory / Transitional Frame

본문의 역사적 정보, 배경, 시간 흐름, 장면 연결을 이해시키는 것이 주목적인 경우:

- 필요한 내용을 자체 내레이션으로 설명할 수 있다.
- 직접 인용이 핵심이 아닌 경우 화면을 긴 본문으로 과밀하게 만들지 않는다.
- 내레이션은 성경 직접 인용과 혼동되지 않도록 데이터와 표현을 분리한다.

어떤 Frame이 Key Scripture인지 여부는 Episode / Storyboard / Presentation 설계에서 판단하며, 모든 성경 구간을 동일 비율의 직접 인용으로 강제하지 않는다.

Storyboard 단계에서는 각 beat가 **Key Scripture 중심인지 / Explanatory·Transitional 중심인지** 판단할 수 있다.

다만 이 판단을 위해 현재 Storyboard schema에 별도 enum 필드를 추가하지 않는다.
필요한 의도는 `beat` 또는 `transition_note`의 짧은 설명으로 충분히 드러낼 수 있으며,
실제 직접 인용문 전문·장절 표시 문구·narration 문장은 Storyboard에 중복 저장하지 않는다.

구체 presentation text와 표시 방식은 presentation layer가 소유한다.

Multi-Cut / Episode-level production에서는 `presentation-plan.yaml`이 이 계획의 물리 Source of Truth다.

각 active Cut은 필요에 따라 다음 중 하나로 분류한다.

- `key_scripture`
- `explanatory`
- `visual_only`

`key_scripture`는 Presentation Gate 전에 정확한 quote / verse / translation이 준비되어야 한다.
요약·의역 문장을 직접 인용처럼 대체하지 않는다.


## 12. Default Presentation Typography

별도 Episode-specific typography profile이 없는 경우 presentation text는 이미지 감상을 방해하지 않고 직접 인용과 내레이션의 위계를 명확히 구분하는 방향을 기본으로 한다.

- 읽기 쉬운 서체를 사용한다.
- 본문보다 장절·출처 표기를 보조적인 위계로 둔다.
- 중요한 피사체를 가리지 않는다.
- 불필요하게 큰 불투명 텍스트 박스를 기본값으로 사용하지 않는다.
- 직접 인용과 내레이션이 혼동되지 않도록 표현을 구분한다.
- 특정 폰트, 위치, 픽셀 크기, 색상, 여백 수치를 프로젝트 전역 고정값으로 두지 않는다.

구체 typography profile은 실제 Episode / presentation 요구가 생길 때 별도로 정한다.

이 Typography 규칙은 presentation derivative에 적용하며 Production Master image 자체에는 계속 text를 bake-in하지 않는다.


## 13. Presentation Gate

Presentation derivative가 필요한 Cut은 다음 조건을 만족해야 한다.

1. Production Master Result가 accepted다.
2. `presentation-plan.yaml`의 mode가 확정되어 있다.
3. `key_scripture`이면 translation / verse / exact quote가 확정되어 있다.
4. `explanatory`이면 narration이 직접 인용과 구분되어 있다.
5. Typography Rule을 확인한다.
6. Production Master 자체는 계속 text-free로 보존한다.

이 Gate를 통과하지 않은 상태에서 임의 summary caption을 최종 presentation으로 만들지 않는다.
