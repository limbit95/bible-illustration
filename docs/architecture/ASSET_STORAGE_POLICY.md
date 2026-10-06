# Image / Asset Storage Policy v1.1

> 상태: **CONFIRMED / 2026-10-06 pre-production hardening**
>
> 선행 조건:
> - **STEP 0-1 — Content Model v1.0 CONFIRMED**
> - **STEP 0-2 — Episode / Cut Model v1.0 CONFIRMED**
> - **STEP 0-3 — Continuity Model v1.0 CONFIRMED**
> - **STEP 0-4 — Library Model v1.0 CONFIRMED**
> - **STEP 0-5 — Generation Run Model v1.0 CONFIRMED**
> - **STEP 0-6 — Provider Integration Model v1.0 CONFIRMED**
>
> 목적: 프로젝트에서 생성·선정·참조하는 이미지 파일의 Canonical 보존 정책을 정의하고, Generated Result와 장기 Asset을 분리하며, 원본·Reference·대표 이미지·파생본의 식별자, 보존 범위, 파일명, 무결성, Git / Git LFS / 외부 전달 저장소의 역할을 확정한다.
>
> 이 단계가 확정되면 STEP 0의 7개 아키텍처 설계 영역이 모두 완료된다.

## 1. 핵심 저장 원칙

프로젝트의 장기 Source of Truth는 계속 limbit95/bible-illustration 저장소다.

따라서 v1 저장 정책은 다음으로 둔다.

~~~text
일반 Git
= Markdown / YAML / JSON 등 텍스트 Canonical 데이터와 Asset metadata

Git LFS
= 장기 보존 대상으로 승격된 이미지 binary

외부 Storage / CDN
= 배포·캐시·백업·전달용 복제본
  Canonical Source of Truth 아님
~~~

원칙:

1. 고해상도 이미지 binary를 일반 Git blob으로 누적하지 않는다.
2. 장기 보존하는 production/reference 이미지 binary는 같은 bible-illustration 저장소의 Git LFS를 기본 Canonical 저장 방식으로 사용한다.
3. Git LFS pointer와 Asset metadata는 Git history로 추적한다.
4. 외부 object storage, CDN, Provider 보관함은 Canonical 원본을 대신하지 않는다.
5. 외부 저장소만 남아 있고 GitHub Canonical Asset이 없는 상태를 정상 운영 상태로 보지 않는다.
6. 실제 Git LFS 구성과 파일 이관은 STEP 0 확정 후 최종 디렉터리 구조를 만들 때 수행한다.

## 2. Generated Result와 Asset의 차이

`GENERATION_RUN_MODEL.md`에서 정의한 Generated Result는 Provider가 반환한 개별 출력 기록이다.

모든 Generated Result가 장기 Asset이 되는 것은 아니다.

~~~text
Generation Run
  ↓
Generated Result
  ↓ review
보존 가치 판단
  ↓
Asset Promotion
  ↓
Canonical Asset metadata + retained binary
~~~

**Asset Promotion**은 특정 Result 또는 외부/수동 이미지가 앞으로도 프로젝트에서 다시 사용할 가치가 있다고 판단해 장기 관리 대상으로 등록하는 행위다.

Asset으로 승격될 수 있는 예:

- Cut의 대표 이미지 후보
- 최종 대표 이미지
- Character / Location / Object / Costume Reference
- Continuity reference
- Style reference
- 중요한 비교용 대안
- 실패 원인을 장기 학습 자료로 보존할 가치가 있는 diagnostic 이미지

일반적인 rejected Result는 metadata와 Review 기록만 남기고 binary까지 영구 보존할 필요는 없다.

## 3. Asset의 불변 identity

Asset은 **보존하기로 결정한 하나의 실제 이미지 binary identity**를 나타낸다.

중요 원칙:

1. 같은 Asset ID의 pixel content를 다른 이미지로 덮어쓰지 않는다.
2. 의미 있는 이미지 수정으로 pixel content가 달라지면 새 Asset ID를 만든다.
3. 파일명 변경이나 metadata 오탈자 수정은 새 Asset을 요구하지 않는다.
4. format conversion / resize처럼 의미가 동일한 파생 파일은 원칙적으로 새 top-level Asset이 아니라 Derivative로 관리한다.
5. Asset의 역할이 candidate에서 representative로 바뀌어도 Asset ID는 유지한다.
6. Asset ID에 final / approved / rejected 같은 가변 상태를 넣지 않는다.

## 4. Asset ID

v1에서는 Asset을 소유 맥락에 따라 안정적으로 식별한다.

### Cut-owned Asset

형식:

~~~text
<CUT_ID>-A<NNN>
~~~

예:

~~~text
BOOK-STORY-01-C03-A001
BOOK-STORY-01-C03-A002
~~~

### Library-owned Asset

형식:

~~~text
<LIBRARY_ASSET_ID>-A<NNN>
~~~

예:

~~~text
CHR-MOSES-A001
LOC-SINAI-A001
STY-BIBLICAL-HISTORICAL-REALISM-A001
~~~

규칙:

1. Asset ID는 owner scope 안에서 순차 발급한다.
2. 번호는 최소 3자리 zero-padding을 사용하되 999를 상한으로 두지 않는다.
3. 삭제·폐기된 번호를 재사용하지 않는다.
4. Cut-owned Asset은 해당 Cut의 결과·참조 용도에 사용한다.
5. Library-owned Asset은 해당 Canonical Library Entity의 reference 용도에 사용한다.
6. v1에서 owner 없는 전역 Asset namespace는 만들지 않는다.
7. 프로젝트 전역 Style reference는 STY Library Entity 아래에서 관리한다.
8. 새로운 실제 필요가 생기기 전 Asset namespace를 추가하지 않는다.

## 5. Asset Metadata

각 Asset은 binary와 별도로 Git에서 읽을 수 있는 metadata를 가진다.

개념 예:

~~~yaml
asset_id: BOOK-STORY-01-C03-A001

owner:
  type: cut
  id: BOOK-STORY-01-C03

source:
  kind: generation_result
  result_id: BOOK-STORY-01-C03-R007-O02
  parent_asset_id: null

binary:
  storage: git_lfs
  path: ...
  sha256: ...
  media_type: image/png
  width: ...
  height: ...
  size_bytes: ...

availability: available

created_at: ...

provenance:
  provider: openart
  note: null

rights:
  storage_status: permitted
  basis: project_generated
  note: null
~~~

최종 YAML/Markdown 물리 포맷은 STEP 0 완료 후 디렉터리 구조를 만들 때 확정한다.

## 6. Source Kind / Provenance

Asset은 어디에서 왔는지 추적할 수 있어야 한다.

source.kind 후보:

~~~text
generation_result
manual_edit
user_supplied
external_import
derived_import
~~~

### generation_result

Generation Run Result에서 승격.

반드시 source Result ID를 기록한다.

### manual_edit

프로젝트 관리자가 기존 Asset을 외부 편집 도구 등에서 의미 있게 수정한 새 이미지.

parent_asset_id와 편집 메모를 기록한다.

### user_supplied

사용자가 직접 제공한 이미지.

가능한 경우 원본 파일명과 제공 시점을 기록한다.

### external_import

외부 연구·고증·시각 참고 자료 중 **저장소에 binary를 장기 보존할 권리와 필요가 확인되어 실제 Asset으로 가져온 경우**에만 사용한다.

출처와 권리 근거를 기록해야 한다.

### derived_import

외부 과정에서 생성되었지만 기존 프로젝트 Asset으로 직접 추적 가능한 경우 등 제한적으로 사용한다.

가능하면 더 구체적인 source kind를 우선한다.

## 7. Binary 무결성

Asset metadata에는 가능한 한 binary 무결성을 확인할 수 있는 정보를 기록한다.

최소 권장:

- SHA-256
- media type / format
- width
- height
- size bytes

원칙:

1. 같은 Asset ID의 SHA-256이 달라지면 의도치 않은 binary overwrite 가능성으로 본다.
2. 정상적인 의미 있는 변경이면 기존 Asset을 덮어쓰지 않고 새 Asset ID를 발급한다.
3. 동일 binary를 여러 번 저장하려는 경우 SHA-256을 이용해 중복 여부를 확인한다.
4. 완전히 동일한 binary를 여러 역할에서 사용할 수 있다면 가능하면 기존 Asset ID를 재사용하고 역할/참조만 추가한다.

## 8. Asset Availability

Asset binary의 현재 보존 상태는 사용 관계와 별도로 관리한다.

기본 값:

~~~text
pending_ingest
available
missing
removed
~~~

- pending_ingest: Asset ID/metadata는 준비됐지만 Canonical binary ingest가 아직 완료되지 않음
- available: Canonical binary를 현재 조회할 수 있음
- missing: available이어야 하지만 binary가 예상 위치에서 확인되지 않아 복구 필요
- removed: 정책·권리·중복 등 명시적인 이유로 binary를 제거했으며 metadata tombstone만 유지

대표 Asset이나 active Library Reference가 missing / removed 상태인 것은 오류로 취급한다.

## 9. Asset Usage Relation

Asset 자체가 cut_representative, character_reference 같은 **현재 역할의 Source of Truth를 소유하지 않는다.**

그 역할은 Asset을 사용하는 쪽의 명시적인 관계 데이터가 소유한다.

예:

~~~text
Cut
  ├─ candidate Asset refs
  └─ representative Asset selection

Library Entity
  └─ reference Asset refs

Continuity
  └─ continuity Reference Asset ref

Generation Run
  └─ actual input Reference Asset snapshot
~~~

이 원칙을 두는 이유는 하나의 이미지가 여러 곳에서 사용될 때 Asset metadata 안의 roles 배열과 실제 사용 관계가 서로 어긋나는 문제를 막기 위해서다.

Asset metadata에는 필요하다면 검색 편의를 위한 비권위적 tag를 둘 수 있지만, 승인·대표·Reference 여부는 소비자 쪽 관계 데이터에서 판단한다.

## 10. Cut Representative Asset

Cut의 최종 제작 상태는 Asset 자체의 이름에 final을 넣는 방식이 아니라 **Cut이 현재 어떤 Asset을 대표 이미지로 선택했는지**로 관리한다.

개념 예:

~~~yaml
representative_asset:
  asset_id: BOOK-STORY-01-C03-A004
  selected_against:
    cut_revision: 2
    repository_commit: ...
  selected_at: ...

representative_history:
  - asset_id: BOOK-STORY-01-C03-A002
    selected_against:
      cut_revision: 1
      repository_commit: ...
    selected_at: ...
    replaced_at: ...
~~~

규칙:

1. 한 Cut revision에는 현재 대표 Asset을 최대 하나 둔다.
2. 과거 대표 Asset 선택 기록은 삭제하지 않는다.
3. 대표 Asset 교체가 Canonical Scene 정의 변경을 의미하지 않으면 Cut revision을 증가시키지 않는다. 대신 대표 Asset selection history를 추가한다.
4. 새 Cut revision이 생기면 이전 대표 Asset이 자동으로 새 revision의 대표 Asset이 되지 않는다.
5. 새 revision의 Canonical 기준으로 다시 검토·선정한다.
6. 대표 Asset은 available 상태여야 한다.
7. Generation Result 기반 Asset이면 현재 기준에 대한 accepted Review 근거가 있어야 한다.

## 11. Production Complete 확정

Episode/Cut Definition Approval과 분리된 production complete를 여기서 정의한다.

### Cut Production Complete

다음 조건을 모두 충족하면 현재 Cut revision을 production complete로 간주할 수 있다.

1. Cut definition_status가 approved.
2. 현재 approved Cut revision에 대해 representative_asset이 선택됨.
3. 대표 Asset binary가 available.
4. 대표 Asset이 현재 approved Cut revision과 해당 Library / Continuity 기준을 포함하는 Canonical snapshot에 대한 검토를 통과함.
5. 이후 저장소에 관련 없는 문서 변경이 생겼다는 이유만으로 production complete가 자동 무효화되지는 않음.

production complete는 Cut Definition Status와 별도의 **도출 상태**로 본다.

별도 mutable status 필드를 중복 저장하기보다 위 조건으로 계산하는 것을 우선한다.

### Episode Production Complete

현재 Episode의 모든 active Cut이 production complete이면 Episode도 production complete로 도출할 수 있다.

## 12. Library Reference Asset

Library Entity는 하나 이상의 이미지 Reference Asset을 가질 수 있다.

예:

~~~yaml
reference_assets:
  - asset_id: CHR-MOSES-A001
    profile: EXODUS
    purpose: primary_identity
    verified_against_revision: 2
~~~

원칙:

1. Reference image는 Canonical Character/Location/Object 자체가 아니다.
2. Library definition이 Source of Truth이며 이미지는 그 정의를 시각적으로 보조한다.
3. Library revision이 바뀌면 Reference가 여전히 유효한지 검토한다.
4. Reference가 바뀌어도 Library ID는 유지한다.
5. Provider Binding은 Reference Asset을 사용할 수 있지만 외부 Provider 리소스가 Reference Asset을 대체하지 않는다.

## 13. External Reference Record와 권리 / 출처

Asset은 장기 보존되는 실제 binary identity이므로, 권리 문제로 binary를 저장할 수 없는 외부 참고자료를 억지로 Asset으로 만들지 않는다.

이런 자료는 Cut 또는 Library 아래의 **External Reference Record**로 관리한다.

개념 예:

~~~yaml
external_references:
  - key: XR01
    source_url: ...
    source_title: ...
    retrieved_at: ...
    rights_status: unknown
    note: ...
~~~

원칙:

1. External Reference Record의 local key는 해당 Cut/Library 범위에서만 유일하면 된다.
2. 외부 자료를 Bible Illustration 저장소에 장기 보존할 권리가 명확하지 않다면 binary를 복제하지 않는다.
3. 링크가 사라질 수 있으므로 가능한 범위에서 출처명, 작성자/기관, 조회 시점, 설명을 함께 기록한다.
4. External Reference Record는 Canonical production master나 representative_asset이 될 수 없다.
5. Provider Run에서 실제 remote reference로 사용했다면 Run snapshot에도 사용 사실과 확인 가능한 URL/metadata를 남긴다.
6. 저장 권리와 장기 보존 가치가 확인되면 External Reference Record의 자료를 정식 Asset으로 import할 수 있다.

정식 Asset의 rights는 provenance와 분리한다.

개념 예:

~~~yaml
rights:
  storage_status: permitted
  basis: licensed
  note: ...
~~~

storage_status 후보:

~~~text
permitted
restricted
unknown
~~~

basis 후보:

~~~text
project_generated
licensed
public_domain
permission_granted
user_asserted
other
unknown
~~~

원칙:

1. user_supplied라는 provenance만으로 저장 권리가 자동 보장된다고 보지 않는다.
2. external_import는 storage_status가 permitted라고 판단할 근거가 있을 때만 Canonical binary로 저장한다.
3. public_domain / licensed / permission_granted를 추측하지 않는다.
4. 권리 상태가 unknown 또는 restricted이면 External Reference Record 방식이 기본이다.

## 14. Git / Git LFS 역할 분리

### 일반 Git에 저장

- Architecture / Rules / Progress 문서
- Episode / Storyboard / Cut / Continuity 정의
- Library 정의
- Provider Integration 설정
- Generation Run metadata
- Result Review 기록
- Asset metadata / selection 기록
- 작은 텍스트 manifest

### Git LFS에 저장

- 장기 보존하기로 승격된 원본/production 이미지
- Cut 대표 Asset
- 보존할 accepted candidate
- Library Reference Asset
- Continuity Reference Asset
- 장기 학습 가치가 있어 Asset으로 승격된 diagnostic 이미지
- 필요 시 편집 가능한 고용량 source image

### 일반 Git에 넣지 않음

- 대량 고해상도 raster binary
- 모든 Provider output을 자동 저장한 결과 묶음
- 쉽게 재생성 가능한 대용량 derivative
- 캐시 / thumbnail cache
- Provider 임시 다운로드 파일

## 15. Result Binary 보존 정책

STEP 0-5의 모든 Result metadata와 Review history는 유지하지만 binary 보존은 선택적이다.

### 반드시 장기 보존

- Asset으로 승격된 Result
- 현재 또는 과거 대표 Asset
- active/historical Library Reference로 사용된 Asset
- 과거 Run의 실제 Reference input으로 사용되어 재현성에 중요한 Asset
- 특별히 diagnostic 보존 대상으로 지정된 Asset

### 장기 보존 의무 없음

- rejected 되었고 별도 diagnostic 가치가 없는 Result
- 중복 생성물
- 비교 가치가 없는 실패 결과
- Provider에서 생성됐지만 프로젝트가 Asset으로 승격하지 않은 단순 후보

원칙:

1. Review 전 Result binary를 임의 삭제하지 않는다. Review 전까지의 binary는 Provider 보관함이나 로컬 working cache처럼 일시 영역에 있을 수 있지만 이는 Canonical 저장소로 간주하지 않는다.
2. Review와 Asset Promotion 판단이 끝난 후 비보존 Result binary는 정리할 수 있다.
3. binary를 정리해도 Result ID, Run metadata, Review, issue reason은 유지한다.
4. accepted Result도 자동 영구 보존하지 않는다. 장기 가치가 있으면 Asset으로 승격한다.
5. 대표 후보 비교에 필요한 accepted Result는 선정 과정이 끝날 때까지 유지한다.

### Run Reference Input 보존

프로젝트가 직접 통제하는 이미지 binary를 Generation Run의 실제 Reference input으로 사용할 경우 **Run 제출 전에 available Asset으로 등록해야 한다.**

이렇게 해야 Run이 참조한 실제 입력 이미지가 나중에 사라지지 않는다.

원칙:

1. Character / Continuity / Composition 등 프로젝트 소유 binary Reference는 available Asset ID로 전달한다.
2. accepted Result 또는 사용자 승인 working image를 실제 downstream reference로 사용할 경우 먼저 Asset Promotion한다.
3. 임시 로컬 파일을 중요한 Run input으로 사용한 뒤 기록 없이 버리는 흐름을 금지한다.
4. 권리 때문에 저장할 수 없는 외부 remote reference는 External Reference Record + Run snapshot으로 기록하고 재현성 한계를 인정한다.
5. 과거 Run에서 사용된 Asset Reference는 해당 Run 기록이 유지되는 동안 기본 보존 대상이다.

## 16. Asset Promotion 규칙

Generated Result를 Asset으로 승격하면 다음을 수행한다.

1. 새 Asset ID 발급
2. source.result_id 기록
3. generation_result를 승격하는 경우 가능하면 Provider가 반환한 해당 Result의 원본 binary를 byte-for-byte 그대로 Canonical master로 보존
4. binary를 Canonical Git LFS 위치에 저장
5. Git LFS ingest가 완료되기 전에는 availability를 pending_ingest로 둘 수 있음
6. SHA-256 및 기본 파일 metadata 기록
7. provenance / rights 기록
8. 필요한 Cut / Library Entity / Continuity 사용 관계를 연결
9. generation_result 기반이면 Asset metadata의 `source.result_id`로 provenance를 연결
10. binary와 metadata 확인이 끝나면 availability를 available로 전환

Asset Promotion은 Result Review와 별개다.

일반적으로 accepted Result를 승격하지만, rejected Result도 diagnostic 목적이라면 Asset으로 승격할 수 있다.

Provider 원본을 표준 포맷으로 변환하거나 압축한 파일은 원본 Asset을 대체하지 않고 Derivative로 다루는 것을 우선한다.

## 17. Asset Binary의 불변성

Canonical Asset binary는 등록 이후 immutable로 취급한다.

### 새 Asset이 필요한 변화

- 인물 얼굴/복장/사물 등 실제 pixel 내용 수정
- 생성형 AI edit
- 수동 retouch로 장면 내용 변경
- 색보정이 작품 판단에 영향을 줄 정도로 의미 있게 변경
- crop으로 장면 구성이 의미 있게 달라짐

### Derivative로 처리 가능한 변화

- 동일 composition의 단순 resize
- 포맷 변환
- 품질 압축
- thumbnail 생성
- 웹 전달을 위한 deterministic encoding

경계가 애매하고 제작 판단에 영향을 주는 변화라면 새 Asset을 만드는 쪽을 우선한다.

## 18. Derivative Model

Derivative는 Canonical Asset에서 재생성 가능한 전달용 파일이다.

예:

~~~text
BOOK-STORY-01-C03-A004
  ├─ web-1920.avif
  ├─ web-1280.webp
  └─ thumb-480.webp
~~~

Derivative에는 필요 시 다음을 기록할 수 있다.

- source asset ID
- derivative key
- transform profile/version
- format
- dimensions
- checksum

원칙:

1. Derivative는 Canonical master가 아니다.
2. 재생성 가능하면 Git LFS 장기 보존을 필수화하지 않는다.
3. 웹사이트 배포 파이프라인이 필요하면 source Asset에서 다시 생성할 수 있어야 한다.
4. 단순 derivative 생성은 Generation Run이 아니다.
5. derivative가 수동 보정되어 독립적인 작품 의미를 갖게 되면 새 Asset으로 승격한다.

## 19. 외부 Storage / CDN

향후 사이트 운영을 위해 object storage나 CDN을 사용할 수 있다.

하지만 v1 원칙:

~~~text
GitHub repo + Git LFS
= Canonical source

CDN / object storage
= delivery copy / cache / mirror
~~~

원칙:

1. 외부 URL을 Asset의 유일한 Canonical 위치로 사용하지 않는다.
2. 배포 URL이 바뀌어도 Asset ID는 유지한다.
3. 외부 storage가 삭제되어도 Git LFS Canonical binary에서 복구 가능해야 한다.
4. 웹사이트는 필요하면 export manifest를 통해 Asset ID와 배포 URL을 연결한다.
5. 다른 프로젝트 저장소에 Canonical master를 중복 유지하지 않는다.

## 20. Backup과 Source of Truth

백업은 허용하지만 Source of Truth와 구분한다.

예:

- 로컬 clone
- 조직 백업
- 외부 archive mirror

이들은 재해 복구 수단이며 Canonical 수정의 출발점이 아니다.

복구 후 최종적으로 GitHub bible-illustration의 Git / Git LFS 상태와 metadata가 다시 일치해야 한다.

## 21. 물리 파일명

Canonical binary 파일명은 사람이 임의로 지은 긴 Prompt 기반 이름을 사용하지 않는다.

기본:

~~~text
<ASSET_ID>.<ext>
~~~

예:

~~~text
BOOK-STORY-01-C03-A004.png
CHR-MOSES-A002.png
~~~

원칙:

1. Provider가 준 원본 파일명은 provenance metadata에 필요 시 기록한다.
2. 파일명에 provider, model, approved, final, version 같은 가변 정보를 넣지 않는다.
3. 상태 변경 때문에 파일명을 변경하지 않는다.
4. 파일 확장자는 실제 media format과 일치해야 한다.
5. 의미 있는 binary 변경은 같은 파일명을 overwrite하지 않고 새 Asset ID를 발급한다.

## 22. 논리적 파일 배치

정확한 최종 디렉터리 트리는 STEP 0 완료 직후 확정하지만 논리적 소유 위치는 다음 원칙을 따른다.

### Cut-owned Asset

해당 Cut의 production 영역 아래 Asset 공간에 둔다.

개념:

~~~text
.../<EPISODE_ID>/cuts/<CUT>/assets/
  <CUT_ID>-A001.png
  <CUT_ID>-A001.asset.yaml
~~~

### Library-owned Asset

해당 Library entity의 영역 아래 Asset 공간에 둔다.

개념:

~~~text
library/characters/<CHARACTER>/assets/
  CHR-MOSES-A001.png
  CHR-MOSES-A001.asset.yaml
~~~

원칙:

1. Asset owner와 물리 위치를 일치시킨다.
2. Asset role 때문에 파일을 다른 디렉터리에 중복 복사하지 않는다.
3. 여러 곳에서 사용할 때 Asset ID로 참조한다.
4. 실제 최종 폴더명은 전체 STEP 0 문서화 단계에서 통일한다.
5. owner는 등록 위치를 정하기 위한 primary registration scope이며, 다른 Cut/Library가 동일 Asset ID를 참조하는 것을 막지 않는다.

## 23. Asset 삭제 / Retire 정책

Asset으로 승격된 binary는 일반 Result보다 강한 보존 의무를 가진다.

기본 원칙:

1. current representative / active Library Reference는 물리 삭제하지 않는다.
2. 과거 representative도 제작 이력상 기본 보존한다.
3. 과거 Run의 Reference input으로 사용된 Asset도 재현성을 위해 기본 보존한다.
4. 단순 중복, 권리 문제, 손상 파일 등 명확한 사유가 있을 때만 제거를 검토한다.
5. 제거할 때 Asset metadata는 삭제하지 않고 `availability: removed`와 `removal.removed_at / reason`을 남긴다. 대체 Asset이 있으면 `replacement_asset_id`도 기록할 수 있다.
6. 동일한 역할의 새 Asset이 생겼다고 기존 Asset을 자동 삭제하지 않는다.
7. storage 절감을 위해 우선 정리할 대상은 Asset으로 승격되지 않은 Result binary와 재생성 가능한 Derivative다.

## 24. 중복 Asset 정책

동일 SHA-256 binary가 이미 Canonical Asset으로 존재하는 경우 새 파일 복사를 만들지 않는 것을 우선한다.

처리:

- 기존 Asset이 동일한 실제 이미지라면 기존 Asset을 참조
- owner가 달라 새 identity가 정말 필요한 특별한 이유가 있는지 검토
- 단순히 여러 Cut에서 사용된다는 이유만으로 binary를 복사하지 않음

단, 서로 다른 provenance를 반드시 별도 보존해야 하는 특수 사례가 생기면 metadata 관계로 해결할지 새 Asset이 필요한지 별도 검토한다.

## 25. 웹사이트 공개본과 Production Master

사이트에서 실제 보여주는 파일은 Production Master와 동일 binary일 필요가 없다.

~~~text
Production Master Asset
        ↓ deterministic transform
Web Derivative
        ↓
CDN / site
~~~

원칙:

1. Production Master를 웹 최적화 때문에 직접 축소·압축 overwrite하지 않는다.
2. 사이트는 필요한 해상도/포맷의 Derivative를 사용한다.
3. 사이트 표시용 text / verse / narration은 이미지 binary에 영구 bake-in하지 않는 방향을 유지한다.
4. 웹 배포 파일이 사라져도 Production Master에서 재생성 가능해야 한다.

## 26. Asset과 이미지 내 텍스트

제작 원본 Asset에는 사이트용 성경 본문·내레이션·UI text를 기본적으로 영구 삽입하지 않는다.

예외적으로 이미지 자체의 내용으로 글자가 반드시 존재해야 하는 장면은 Cut Canonical Scene이 결정한다.

사이트 표시용:

- scripture quote
- verse label
- narration
- chapter title
- UI caption

등은 Asset과 분리된 presentation layer에서 관리하는 것을 기본으로 한다.

## 27. STEP 0-7 불변 조건 후보

1. 프로젝트의 Canonical 이미지 Source of Truth는 bible-illustration 저장소의 Git LFS Asset이다.
2. 일반 Git은 텍스트 정의·metadata를 관리하고 고해상도 production binary를 직접 누적하지 않는다.
3. 모든 Generated Result가 Asset이 되는 것은 아니다.
4. Asset은 장기 보존하기로 승격한 실제 이미지 identity다.
5. Asset binary는 immutable이며 의미 있는 pixel 변경은 새 Asset ID를 요구한다.
6. Asset ID에는 final / approved / provider 등 가변 상태를 넣지 않는다.
7. Cut-owned / Library-owned Asset ID를 owner 기반으로 발급한다.
8. Asset metadata에 source / provenance / checksum / binary 정보와 provenance와 분리된 rights 정보를 추적한다.
9. Cut의 대표 이미지는 파일명이 아니라 representative_asset 관계로 관리한다.
10. production complete는 대표 Asset과 현재 Canonical revision의 적합성으로 도출한다.
11. Library Reference image는 Canonical Library 정의를 대체하지 않는다.
12. 저장 권리가 불명확한 외부 자료는 Asset이 아니라 External Reference Record로 관리한다.
13. External Reference Record는 representative production master가 될 수 없다.
14. 모든 Result metadata와 Review는 남기되 모든 Result binary를 영구 보존하지 않는다.
15. 중요한 Run Reference input인 프로젝트 소유 binary는 available Asset으로 보존하는 것을 우선한다.
16. 보존할 Result는 Asset Promotion을 통해 Git LFS에 등록한다.
17. generation_result를 Asset으로 승격할 때는 가능한 한 원본 Result binary를 그대로 보존한다.
18. 재생성 가능한 Derivative는 Canonical master가 아니다.
19. 외부 Storage/CDN은 전달·캐시·백업 용도이며 Source of Truth가 아니다.
20. Asset 파일명은 안정적인 Asset ID 기반으로 한다.
21. Asset의 현재 대표/Reference 역할은 소비자 쪽 명시적 관계가 Source of Truth다.
22. 동일 Asset binary를 역할별로 중복 복사하지 않는다.
23. 대표/Reference/Run dependency가 있는 Asset은 기본적으로 물리 삭제하지 않는다.
24. 웹 배포본은 Production Master에서 재생성 가능해야 한다.
25. 사이트용 성경 본문·내레이션·UI text는 기본적으로 이미지 binary와 분리한다.

## 28. STEP 0-7 이후 현재 해소 상태

현재 이미 확정된 항목:

- Git LFS track pattern → 루트 `.gitattributes`
- v1 Canonical image 확장자 → PNG / JPG / JPEG / WEBP / AVIF
- Asset metadata schema → `templates/asset-metadata.yaml`
- owner 기반 물리 경로 → `REPOSITORY_STRUCTURE.md`
- Git ignore 안전장치 → 루트 `.gitignore`

현재 의도적으로 구현·운영 시점까지 열어두는 항목:

- Production Master의 단일 기본 포맷 강제 여부
- 웹 derivative 실제 해상도 / 포맷
- 변환 도구 / pipeline 구현
- CDN / object storage vendor
- 자동 checksum / LFS 검증 CI
- Result binary 자동 정리 시점
- 백업 주기와 복구 자동화
- 외부 Reference 권리 검토의 세부 workflow

이 항목은 현재 production 시작을 막는 Architecture blocker가 아니다.

## 29. 현재 확정 상태

- 일반 Git = Canonical text/metadata
- Git LFS = promoted Canonical image binary
- Result와 Asset 분리
- Image Asset의 `asset_id`는 binary identity 전용
- Library Entity의 `library_id`와 구분
- project-owned actual Run reference는 available Asset으로 선행 Promotion
- Asset metadata `source.result_id`가 Result provenance linkage의 Source of Truth
- Asset binary immutable
- representative selection과 Asset identity 분리
- removed Asset metadata tombstone 유지
- Derivative는 Canonical master가 아님
- 사이트용 text는 Production Master와 분리

**Image / Asset Storage Policy v1.1 — COMPLETED / CONFIRMED**
