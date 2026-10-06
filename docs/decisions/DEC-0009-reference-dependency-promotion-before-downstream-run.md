# DEC-0009 — Promote project-owned working images before they become downstream Run references

> 상태: accepted
> 날짜: 2026-10-06
> 대체: none
> 대체됨: none
> 관련: DEC-0005

## Context

Sequential Generation에서는 직전 accepted Result 또는 사용자 승인 working image를 다음 Cut의 visual continuity reference로 사용할 수 있다.

동시에 빠른 제작을 위해 일반적인 Asset Promotion / Git LFS ingest / representative selection은 뒤로 미룰 수 있다.

그러나 실제 Provider 요청에 사용한 중요한 project-owned reference binary가 임시 working storage에만 존재하면 과거 Run의 입력 재현성이 깨진다.

## Decision

accepted Result 또는 사용자 승인 working image는 **계획·비교 단계에서는 Asset Promotion 전에도 visual anchor 후보로 사용할 수 있다.**

하지만 그 binary를 실제 downstream Generation Run의 reference input으로 제출하려면:

1. 해당 binary에 Asset ID를 발급한다.
2. Canonical Asset metadata를 작성한다.
3. Git LFS Canonical 위치에 ingest한다.
4. `availability: available`을 확인한다.
5. 이후 Run의 `references[].asset_ref`로 사용한다.

즉 중요한 project-owned binary reference는 **실제 downstream request 이전에 Promotion을 완료**한다.

v1에서는 project-owned binary에 대한 직접 `result_ref` 의존성을 만들지 않는다.

권리 때문에 저장할 수 없는 외부 reference만 External Reference Record + Run snapshot 방식의 예외를 사용한다.

## Asset Promotion 지연 원칙과의 관계

Asset Promotion을 뒤로 미룰 수 있다는 DEC-0005 원칙은 유지한다.

다만 다음 Cut의 실제 reference dependency가 된 Result는 예외다.

- 단순 후보 / 비교용 Result → Promotion 지연 가능
- 다음 Run의 actual binary reference → 먼저 Promotion 필수

대표 Asset 선정은 계속 뒤로 미룰 수 있다.

Reference용 Asset Promotion과 representative selection은 다른 결정이다.

## Result ↔ Asset linkage

Image Asset metadata의 `source.result_id`를 Result→Asset provenance의 Canonical linkage로 사용한다.

Generation Run Result에 별도 mutable `promoted_asset_ids`를 중복 저장하지 않는다.

Result에서 승격 Asset을 찾아야 할 때는 Asset metadata의 `source.result_id`로 역조회한다.

## Consequences

- 순차 생성의 속도 원칙을 유지하면서 실제 reference dependency의 재현성을 확보한다.
- Run reference schema를 Asset 기반으로 단순하게 유지한다.
- Run 기록에 mutable reverse link를 추가하지 않는다.
- Continuity reference로 쓰였다는 이유만으로 해당 Asset이 representative Asset이 되는 것은 아니다.

## Related Sources

- `docs/decisions/DEC-0005-scripture-unit-sequential-generation.md`
- `docs/architecture/GENERATION_RUN_MODEL.md`
- `docs/architecture/ASSET_STORAGE_POLICY.md`
- `docs/rules/GENERATION_RULES.md`
- `templates/generation-run.yaml`
- `templates/asset-metadata.yaml`
