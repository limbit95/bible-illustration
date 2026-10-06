# Library Model v1.1

> 상태: **CONFIRMED / 2026-10-06 pre-production hardening**
>
> 선행 조건:
> - **STEP 0-1 — Content Model v1.0 CONFIRMED**
> - **STEP 0-2 — Episode / Cut Model v1.0 CONFIRMED**
> - **STEP 0-3 — Continuity Model v1.0 CONFIRMED**
>
> 목적: 여러 Episode와 Cut에서 반복 사용되는 Character, Location, Object, Costume, Environment, Visual Style을 Provider와 독립적인 Canonical Library Entity로 정의하고, 안정적인 ID·근거 정보·참조 규칙을 설계한다.
>
> 실제 이미지 binary는 Image Asset, Provider 연결은 Integration, 실행 이력은 Generation Run이 각각 소유한다.

## 1. Library의 역할

Library는 프롬프트 문구 모음이나 외부 서비스 설정 복사본이 아니다.

Library는 반복해서 등장하거나 장기 일관성이 필요한 성경 세계 요소의 **Canonical Definition Layer**다.

~~~text
Scripture / Historical Research
          ↓
Canonical Library Entity
          ↓
Episode / Cut reference
          ↓
Continuity state
          ↓
Provider-specific rendering
~~~

Library Entity은 Provider가 바뀌거나 외부 이미지가 삭제되어도 의미와 정체성이 유지되어야 한다.

## 2. Library Entity 생성 기준

모든 장면 요소를 Library로 승격하지 않는다.

다음 중 하나 이상이면 Library 등록을 우선한다.

- 여러 Episode/Cut에서 반복 등장
- 한 번만 등장하더라도 정체성이 매우 중요
- 인물 외형, 장소 구조, 주요 사물처럼 일관성 오류가 크게 보임
- 역사·문화적 고증을 여러 장면에서 재사용할 가치가 있음
- Provider가 바뀌어도 동일 정의를 다시 재현해야 함

반대로 한 Cut에만 등장하는 일반 배경 소품처럼 재사용성과 Canonical identity가 낮은 요소는 Cut-local 정의로 둘 수 있다.

즉 **재사용성과 일관성 가치가 있는 요소만 Library Entity으로 만든다.**

## 3. Library Entity 종류와 ID

v1 Library는 다음 여섯 종류를 사용한다.

~~~text
CHR  Character
LOC  Location
OBJ  Object
CST  Costume
ENV  Environment
STY  Visual Style
~~~

기본 ID 형식:

~~~text
<PREFIX>-<STABLE_KEY>
~~~

예:

~~~text
CHR-ABRAHAM
CHR-MOSES
LOC-EDEN
LOC-SINAI
OBJ-ARK-COVENANT
OBJ-TABERNACLE
CST-ANCIENT-HEBREW-MALE
CST-EGYPTIAN-ROYAL
ENV-ARID-HIGHLAND
ENV-NILE-FLOODPLAIN
STY-BIBLICAL-HISTORICAL-REALISM
~~~

규칙:

1. Prefix가 자산 종류를 결정한다.
2. ID는 저장소 전체에서 유일하다.
3. Episode/Cut 번호나 Provider 이름을 ID에 넣지 않는다.
4. 표시 이름이 바뀌어도 ID는 바꾸지 않는다.
5. 사용 이력이 있는 ID는 다른 자산에 재사용하지 않는다.
6. 자산의 정체성이 달라지면 새 ID를 발급한다.
7. 같은 실체를 여러 자산 종류로 중복 등록하지 않는다.

## 4. 공통 Library Entity Model

모든 Library Entity은 최소 다음 개념을 가진다.

~~~yaml
library_id: CHR-MOSES
library_type: character
name: 모세
aliases: []

canonical_summary: >
  이 자산이 무엇인지와 제작에서 어떤 정체성으로 다루는지 요약

basis:
  scripture: []
  historical: []
  reconstruction: []

uncertainties: []

definition_status: draft
revision: 1
~~~

공통 책임:

- library_id: 영구 Canonical ID
- library_type: 자산 종류
- name / aliases: 표시명과 검색용 별칭
- canonical_summary: 정체성의 핵심 요약
- basis: 근거 계층
- uncertainties: 미확정 역사·시각 쟁점
- definition_status: 설계·검토·승인 상태
- revision: 승인된 정의 변경 이력

### 4.1 Local Profile / Variant 원칙

하나의 Canonical Asset이 생애·시대·형태에 따라 달라진다고 해서 무조건 새 전역 Library Entity ID를 만들지 않는다.

정체성은 같고 표현 단계만 달라지는 경우 해당 Asset 내부의 local profile 또는 variant를 우선한다.

~~~text
CHR-MOSES
  ├─ profile: MIDIAN
  └─ profile: EXODUS

LOC-JERUSALEM
  ├─ profile: FIRST-TEMPLE
  └─ profile: SECOND-TEMPLE
~~~

원칙:

1. 전역 Library Entity ID는 identity를 나타낸다.
2. local profile/variant는 같은 identity의 생애·시대·형태 변화를 나타낸다.
3. local key는 해당 Asset 내부에서만 유일하면 된다.
4. 독립적으로 여러 자산에서 재사용되어야 하는 정의라면 별도 Library Entity 승격을 검토한다.
5. 상처, 먼지, 현재 위치, 파손처럼 사건 중 일시 상태는 profile이 아니라 Cut/Continuity가 관리한다.

## 5. 근거 계층

성경 본문 사실, 역사적 연구, 시각적 재구성을 같은 사실처럼 섞지 않는다.

### Scripture Basis

성경 본문에서 직접 확인되는 정보.

### Historical Basis

고고학, 고대 근동사, 복식사, 지리 자료 등 본문 외 역사적 근거.

### Visual Reconstruction

본문과 역사 자료만으로 하나의 외형을 확정할 수 없을 때 프로젝트가 일관성을 위해 선택한 시각적 해석.

예:

- 기록되지 않은 헤어스타일
- 정확한 색이 알려지지 않은 의복 색
- 자료가 제한적인 건물의 세부 장식
- 여러 가능한 역사적 형태 중 프로젝트가 채택한 하나

Visual Reconstruction을 Scripture Fact처럼 표현하지 않는다.

합리적인 대안이 여러 개이면 uncertainties에 남긴다.

## 6. Character Model

Character는 한 인물의 **지속적인 identity**를 정의한다.

예:

~~~text
CHR-MOSES
CHR-ABRAHAM
CHR-DAVID
~~~

핵심 데이터 후보:

- 성경상 identity와 역할
- 기본 외형 중 일관성이 필요한 특징
- appearance profile
- Scripture / Historical / Reconstruction basis
- uncertainties

### Appearance Profile

한 인물의 외형은 생애 전체에서 고정되지 않으므로 나이·시기마다 새 Character ID를 만들지 않는다.

~~~yaml
library_id: CHR-MOSES

appearance_profiles:
  - key: MIDIAN
    label: 미디안 시기
    notes: ...

  - key: EXODUS
    label: 출애굽 시기
    notes: ...
~~~

원칙:

1. CHR-MOSES가 인물 identity다.
2. MIDIAN / EXODUS는 Character 내부 local profile key다.
3. profile은 별도의 전역 Character ID가 아니다.
4. Cut은 Character ID와 필요 시 appearance profile을 함께 참조한다.
5. 상처, 젖음, 먼지, 위치 같은 일시 상태는 Cut/Continuity가 관리한다.

### Character와 Costume

Character 안에 의복 정의 전체를 복사하지 않는다.

Character profile은 적합한 Costume을 참조할 수 있지만 Costume 자체의 원본 정의는 Costume Library가 소유한다.

## 7. Location Model

Location은 **정체성을 가진 장소**다.

예:

~~~text
LOC-EDEN
LOC-SINAI
LOC-JERUSALEM
LOC-EGYPT-NILE
~~~

핵심 데이터 후보:

- 지리적 정체성
- 본문에서의 역할
- 알려진 지형
- 주요 랜드마크 / 건축 특징
- 공간적 관계
- 필요 시 시대별 local profile
- Environment reference
- Scripture / Historical / Reconstruction basis

현재 날씨, 특정 시각의 빛, 사건 중 임시 배치는 Cut/Continuity가 관리한다.

## 8. Environment Model

Environment는 고유 장소 identity가 아니라 재사용 가능한 **지형·생태·환경 특성**이다.

예:

~~~text
ENV-ARID-HIGHLAND
ENV-NILE-FLOODPLAIN
ENV-MEDITERRANEAN-HILLS
ENV-PRIMORDIAL-WATERS
~~~

핵심 데이터 후보:

- terrain character
- vegetation pattern
- climate tendency
- water / soil / rock character
- atmospheric tendencies
- Historical / Reconstruction basis

구분:

~~~text
Location = 어디인가
Environment = 그 공간이 어떤 환경적 성격을 가지는가
~~~

Environment가 재사용되지 않는다면 불필요하게 별도 Asset으로 만들지 않는다.

## 9. Object Model

Object는 반복적으로 등장하거나 identity가 중요한 물리적 사물이다.

예:

~~~text
OBJ-ARK-COVENANT
OBJ-TABERNACLE
OBJ-OIL-LAMP
~~~

두 종류를 허용한다.

- unique: 언약궤처럼 특정 실체 하나의 identity가 중요
- type: 등잔처럼 동일 역사적 형태 기준을 여러 장면에서 재사용

핵심 데이터 후보:

- kind
- 재료
- 형태
- 치수 / 비례
- 구조
- 장식
- 기능
- Scripture / Historical / Reconstruction basis

현재 위치, 소유자, 파손, 열림/닫힘 등 사건 중 상태는 Cut/Continuity가 관리한다.

Object와 Location이 겹쳐 보이면 지리적 장소 identity가 핵심인지, 물질적 실체 identity가 핵심인지에 따라 하나만 Canonical 소유자로 선택한다.

## 10. Costume Model

Costume은 특정 인물 한 명에 종속되지 않는 **재사용 가능한 의복 또는 ensemble 정의**다.

예:

~~~text
CST-ANCIENT-HEBREW-MALE
CST-ANCIENT-HEBREW-FEMALE
CST-EGYPTIAN-ROYAL
CST-PRIEST-HIGH
~~~

핵심 데이터 후보:

- 시대 / 문화권
- 사회적 역할
- 적용 범위
- garment components
- materials
- construction
- accessories
- 색상 근거 또는 reconstruction
- 금지해야 할 시대착오 요소
- Scripture / Historical / Reconstruction basis

실제 Cut에서 누구에게 어떤 Costume을 배정하는지는 Cut이, 장면 사이 유지 여부는 Continuity가 책임진다.

## 11. Visual Style Model

Visual Style은 역사적 사실이 아니라 **프로젝트가 장면을 어떤 시각 언어로 렌더링할지 정의하는 Provider-independent 제작 자산**이다.

예:

~~~text
STY-BIBLICAL-HISTORICAL-REALISM
~~~

핵심 데이터 후보:

- realism level
- illustration / cinematic direction
- texture character
- color philosophy
- lighting philosophy
- composition tendencies
- 인물 표현 원칙
- historical constraints
- 피해야 할 style drift

원칙:

1. 역사적 고증 데이터와 Visual Style을 분리한다.
2. Provider 모델명이나 preset 이름을 Canonical Style에 넣지 않는다.
3. 실제 Provider style/profile mapping은 STEP 0-6이 맡는다.
4. 여러 Cut에 공통인 기본 Style reference는 필요 시 Continuity Segment baseline의 `visual.style_ref`로 Library Entity ID를 참조할 수 있다.
5. Cut별 차이가 필요할 때만 Cut의 `library_refs.style`에서 명시적으로 override한다.
6. v1에서는 복잡한 style 합성보다 하나의 base style + 필요 시 override를 우선한다.

## 12. Library Entity Reference 원칙

Episode / Cut / Continuity는 Library 정의를 복사하지 않고 ID로 참조한다.

개념 예:

~~~yaml
library_refs:
  characters:
    - library_id: CHR-MOSES
      appearance_profile: EXODUS
      costume_ref: CST-ANCIENT-HEBREW-MALE

  location:
    library_id: LOC-SINAI

  environments:
    - library_id: ENV-ARID-HIGHLAND

  objects:
    - library_id: OBJ-STAFF

  style:
    library_id: STY-BIBLICAL-HISTORICAL-REALISM
~~~

현재 물리 표현은 `templates/library-definition.yaml`, `templates/cut.yaml`, `templates/continuity.yaml`을 따른다.

핵심 원칙:

- 참조자는 Library ID를 저장한다.
- Library의 긴 외형 설명을 Cut에 복사하지 않는다.
- Cut-specific 상태만 Cut/Continuity에 추가한다.
- 특정 Character가 실제 장면에서 어떤 Costume을 입는지 같은 **자산 간 배정 관계는 Cut의 장면 정의가 소유**한다.
- Library는 Character와 Costume을 서로 영구 결합해 모든 장면에 강제하지 않는다.
- Library 수정 시 참조 관계를 통해 영향 범위를 검토할 수 있어야 한다.

## 13. Definition Status와 Revision

Library도 Episode/Cut처럼 **정의 승인과 이미지 제작 상태를 분리**한다.

기본 상태:

~~~text
draft
↓
in_review
↓
approved
~~~

보조 상태:

~~~text
revision_requested
cancelled
superseded
~~~

approved는 “현재 revision이 Canonical 기준으로 참조 가능”하다는 뜻이다.

superseded는 같은 Asset의 새 revision에 사용하지 않고, 다른 Asset ID가 기존 identity를 대체하는 경우에만 사용한다.

Reference image나 Provider 등록 완료 여부와는 별개다.

승인 후 실제 시각 결과에 영향을 줄 수 있는 정의 변경은 revision 증가 대상으로 본다.

### Library Definition 승인 조건

Library Entity의 definition_status를 approved로 만들기 위한 최소 조건:

1. 공통 필수 정의와 해당 library type의 핵심 데이터가 존재한다.
2. Scripture / Historical / Visual Reconstruction이 가능한 범위에서 구분되어 있다.
3. 중요한 불확실성이 있다면 uncertainties에 기록되어 있다.
4. Provider external ID나 특정 서비스 preset이 Canonical 정의를 대신하지 않는다.
5. 동일 실체의 중복 Library Entity이 없는지 검토되었다.

Episode/Cut 작성 중에는 draft Library Entity을 임시 참조할 수 있지만, **Cut definition을 최종 approved로 만들 때 해당 Cut이 의존하는 Canonical Library Entity과 사용 profile은 approved 상태여야 한다.**

이 규칙은 승인된 장면이 아직 확정되지 않은 인물 외형이나 장소 정의에 기대는 것을 막기 위한 것이다.

## 14. Library 변경 영향 검토

공유 Library Entity의 revision이 올라가면 참조 Cut의 영향 범위를 검토한다.

~~~text
Library revision 변경
        ↓
참조 Cut 검색
        ↓
변경 필드 / 사용 profile 비교
        ↓
영향 있는 Cut만 revision 재검토
~~~

원칙:

1. Library revision 증가가 모든 참조 Cut revision의 자동 증가를 의미하지 않는다.
2. 실제 변경 필드와 해당 Cut이 사용하는 profile을 기준으로 영향 여부를 판단한다.
3. 생성 결과가 달라질 정도로 영향받는 승인 Cut만 STEP 0-2 revision 규칙을 적용한다.
4. Continuity가 영향받으면 STEP 0-3 규칙도 함께 적용한다.

## 15. Reference Image와 Provider Binding 분리

~~~text
Canonical Library Entity
        ↓
Reference Asset(s)
        ↓
Provider Binding
        ↓
OpenArt / Higgsfield / future provider
~~~

- Reference Asset 저장·버전 정책 → STEP 0-7
- Provider external ID / profile → STEP 0-6
- Provider용 prompt 최적화 → STEP 0-6
- Library에는 Provider와 무관한 Canonical 정의만 유지

외부 서비스 슬롯이 삭제되어도 Library Entity ID와 정의는 유지되어야 한다.

## 16. 자산 경계 판단 규칙

### Character vs Costume

- 인물 identity / 기본 외형 → Character
- 재사용 가능한 의복 구조 → Costume
- 특정 Cut에서 착용 상태 → Cut
- 장면 사이 의복 유지 → Continuity

### Location vs Environment

- 고유 장소 identity → Location
- 재사용 가능한 지형·생태·환경 특성 → Environment
- 현재 날씨 / 시간대 → Cut 또는 Continuity

### Location vs Object

- 지리적 장소 identity → Location
- 물질적 실체 / 제작된 구조물 identity → Object
- 같은 실체를 양쪽에 중복 등록하지 않음

### Environment vs Visual Style

- 세계 안에 실제 존재하는 환경 특성 → Environment
- 관객에게 어떻게 렌더링할지 → Visual Style

### Library vs Cut

- 여러 장면에서 재사용할 Canonical 정체성 → Library
- 이번 장면에만 필요한 상태 / 행동 → Cut 또는 Continuity

## 17. Library 불변 조건 후보

1. Library는 반복 사용되는 성경 세계 요소의 Canonical Definition Layer다.
2. Library Entity은 Provider와 독립적이다.
3. 모든 Library Entity은 안정적인 type prefix ID를 가진다.
4. 동일 실체를 여러 library type으로 중복 등록하지 않는다.
5. Scripture Basis / Historical Basis / Visual Reconstruction을 구분한다.
6. 불확실한 고증을 확정 사실처럼 숨기지 않는다.
7. 전역 Library Entity identity와 같은 Asset 내부의 local profile/variant를 분리한다.
8. Character identity와 생애 단계별 appearance profile을 분리한다.
9. Character와 Costume 원본 정의를 중복하지 않는다.
10. Location과 Environment 책임을 분리한다.
11. Object의 사건 중 상태는 Cut/Continuity가 관리한다.
12. Visual Style은 역사적 사실과 분리된 Provider-independent 제작 자산이다.
13. Episode/Cut/Continuity는 Library 정의를 복사하지 않고 ID로 참조한다.
14. Character와 Costume 같은 자산 간 실제 장면 배정은 Cut이 소유한다.
15. Library Definition Approval과 Reference Image / Provider 등록 상태를 분리한다.
16. 승인된 Library 변경은 참조 Cut에 대한 영향 검토를 수행한다.
17. Library revision 증가가 모든 참조 Cut revision 자동 증가를 의미하지 않는다.
18. 재사용성과 일관성 가치가 없는 일회성 요소를 과도하게 Library Entity으로 만들지 않는다.
19. 승인된 Cut은 자신이 의존하는 Canonical Library Entity과 사용 profile이 approved 상태여야 한다.

## 18. STEP 0-4 후속 책임의 현재 해소 상태

STEP 0-4 당시 후속 단계로 넘겼던 항목은 현재 다음 Source of Truth에서 해소되었다.

- Reference Image 저장 / 파일명 / 보존 → `ASSET_STORAGE_POLICY.md`
- Provider external resource / binding / generation profile → `PROVIDER_INTEGRATION_MODEL.md`
- Generation Run의 Library snapshot → `GENERATION_RUN_MODEL.md`
- 물리 경로와 YAML Template → `REPOSITORY_STRUCTURE.md` / `templates/library-definition.yaml`
- Reference Asset 승인 / 사용 관계 → `ASSET_STORAGE_POLICY.md`

현재 의도적으로 열어두는 항목:

- 실제 Library Entity 인스턴스
- 역사 연구 citation의 별도 전용 schema
- Provider별 reference strength / weight의 실제 값

이 항목은 실제 사례가 생길 때 최소 범위로 확장한다.

## 19. 현재 확정 상태

- Library의 Canonical semantic definition을 **Library Entity**라고 부른다.
- Library Entity의 공통 식별 필드는 `library_id / library_type`이다.
- Image binary identity인 `asset_id`와 의미를 분리한다.
- CHR / LOC / OBJ / CST / ENV / STY ID 형식은 유지한다.
- Library Entity는 Provider와 독립적이다.
- Scripture / Historical / Visual Reconstruction 근거를 분리한다.
- Character / Costume, Location / Environment, Object state, Visual Style의 책임 경계를 유지한다.
- 승인된 Cut이 의존하는 Library Entity/profile은 approved 상태여야 한다.
- Reference Asset / Provider Binding은 Canonical Library Entity와 분리한다.

**Library Model v1.1 — COMPLETED / CONFIRMED**
