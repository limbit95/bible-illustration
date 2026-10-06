# DEC-0008 — Distinguish Canonical Library Entity identity from image Asset identity

> 상태: accepted
> 날짜: 2026-10-06
> 대체: none
> 대체됨: none

## Context

기존 Library Model은 `CHR-MOSES`, `LOC-SINAI`, `STY-...` 같은 Canonical 정의를 "Library Asset"이라고 불렀고 공통 식별 필드로 `asset_id`를 사용했다.

Image / Asset Storage Policy는 `CHR-MOSES-A001`, `<CUT_ID>-A001`처럼 실제 장기 보존 이미지 binary에도 "Asset"과 `asset_id`를 사용한다.

두 identity는 성격이 다르다.

- Library 정의: 인물·장소·사물·복식·환경·스타일의 Canonical semantic identity
- Image Asset: 실제 binary identity

실제 Library data가 아직 없는 현재 시점에 이 용어 충돌을 해소하는 것이 가장 안전하다.

## Decision

Canonical Library 정의를 **Library Entity**라고 부른다.

Library Entity의 공통 필드는 다음으로 통일한다.

- `library_id`
- `library_type`

ID 형식 자체는 바꾸지 않는다.

예:

- `CHR-MOSES`
- `LOC-SINAI`
- `OBJ-ARK-COVENANT`
- `CST-ANCIENT-HEBREW-MALE`
- `ENV-ARID-HIGHLAND`
- `STY-BIBLICAL-HISTORICAL-REALISM`

"Asset"과 `asset_id`는 실제 장기 보존 이미지 binary identity에 사용한다.

예:

- `CHR-MOSES-A001`
- `GEN-EXAMPLE-02-C01-A001`

Provider Binding, Cut Library reference, Generation Run Library snapshot 등 Canonical Library 정의를 가리키는 참조는 `library_id`를 사용한다.

## Migration impact

현재 active Library Entity data가 없으므로 production data migration은 발생하지 않는다.

변경 대상은 Architecture / Rules / Templates / Integration schema 표현뿐이다.

과거 Git history의 `asset_id: CHR-...` 기록은 역사 기록으로 그대로 둔다.

## Consequences

- semantic definition과 image binary identity를 이름만으로 구분할 수 있다.
- 자동 검증과 검색 시 `asset_id`의 의미가 단일해진다.
- Library-owned image Asset이라는 개념은 계속 유지된다.
- Repository의 `library/` 물리 구조는 변경하지 않는다.

## Related Sources

- `docs/architecture/LIBRARY_MODEL.md`
- `docs/architecture/GENERATION_RUN_MODEL.md`
- `docs/architecture/PROVIDER_INTEGRATION_MODEL.md`
- `docs/architecture/ASSET_STORAGE_POLICY.md`
- `templates/library-definition.yaml`
- `templates/provider-binding.yaml`
- `templates/generation-run.yaml`
