# DEC-0010 — Keep retired production IDs in a current-tree tombstone registry

> 상태: accepted
> 날짜: 2026-10-06
> 대체: none
> 대체됨: none

## Context

Episode / Cut / Run / Result / Asset ID는 한 번 사용되면 다른 identity에 재사용하지 않는 것이 핵심 invariant다.

이전 Genesis production iteration을 current tree에서 제거한 뒤 상세 기록은 Git history에 남아 있지만, 새 ID를 발급할 때마다 전체 Git history를 검색해야만 과거 사용 여부를 확인할 수 있는 문제가 생겼다.

## Decision

current tree에 `content/identity-tombstones.yaml`을 둔다.

이 파일은 active production data가 아니라 **과거에 발급되어 재사용할 수 없는 identity의 최소 registry**다.

최소 기록:

- kind
- id
- retired_at
- reason
- last_known_commit

원칙:

1. tombstone에 등록된 ID는 새 entity에 재사용하지 않는다.
2. 과거 production 상세 장면 정의나 Prompt를 tombstone에 복제하지 않는다.
3. 상세 과거 기록은 계속 Git history가 소유한다.
4. active data를 삭제하거나 retire할 때 재사용 금지 대상 ID를 tombstone에 추가한다.
5. tombstone 자체를 단순 정리 목적으로 삭제하지 않는다.
6. 새 ID 발급 전 active tree와 tombstone registry를 함께 확인한다.

## Consequences

- Git history를 매번 탐색하지 않고 ID 충돌을 방지할 수 있다.
- 폐기된 production을 current tree에 다시 archive할 필요가 없다.
- identity invariant와 "과거 production 상세는 Git history만 보존" 원칙을 동시에 만족한다.

## Related Sources

- `docs/architecture/CONTENT_MODEL.md`
- `docs/architecture/GENERATION_RUN_MODEL.md`
- `docs/architecture/ASSET_STORAGE_POLICY.md`
- `docs/architecture/REPOSITORY_STRUCTURE.md`
- `content/identity-tombstones.yaml`
