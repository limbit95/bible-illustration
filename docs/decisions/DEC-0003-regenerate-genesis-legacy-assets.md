# DEC-0003 — Regenerate Genesis Creation C01–C04 Instead of Recovering Legacy Binaries

> 상태: **accepted / 2026-10-02 사용자 방향 확인**
> 날짜: 2026-10-02
> 대체: none
> 대체됨: none

## Context

Genesis Creation C01–C04의 Canonical definition migration은 완료되었고 C01–C04는 `approved / revision 1` 상태다.

과거 작업에는 다음 legacy image 기록이 남아 있다.

- C01: 별도 구체적 일러스트 없이 검은 화면
- C02: `wide_dramatic_moody_cinematic_scene_of_a_vast.png`
- C03: `a_cinematic_widescreen_high_detail_atmospheric_s.png`
- C04: `a_wide_dramatic_cinematic_landscape_sky_seascape.png`

하지만 C02–C04의 실제 binary는 현재 bible-illustration 저장소와 연결된 Project/Library에서 찾지 못했다.

동시에 현재 프로젝트는 새 Architecture / Rules / Templates가 실제 이미지 생성에 충분한지 검증해야 한다.

사용자는 C01–C04를 새 Architecture 기준으로 다시 생성해 결과를 비교하고, 필요하면 Architecture를 보완하는 방향을 선택했다.

## Decision

C01–C04의 legacy image binary 복구를 migration 완료의 필수 조건으로 두지 않는다.

다음 원칙을 적용한다.

1. C01–C04의 현재 `approved / revision 1` Canonical definition을 새 생성의 기준으로 사용한다.
2. C01은 완전한 black-screen image를 새 Canonical production Asset으로 생성한다.
3. C02–C04는 legacy filename의 binary 복구를 기다리지 않고 새 Generation Run으로 재생성한다.
4. 새 생성 결과는 현재 Generation Run / Result Review / Asset Promotion 규칙을 모두 적용한다.
5. legacy filename과 과거 시각적 확정 기록은 historical provenance로 보존하되 현재 representative Asset으로 자동 계승하지 않는다.
6. 과거 binary가 나중에 발견되더라도 자동 representative로 복원하지 않는다. 필요하면 historical comparison/reference 용도로 별도 검토한다.
7. 새 결과를 보면서 Cut / Continuity / Rules / Architecture의 부족한 점을 검증한다. 구조 변경이 필요하면 생성 결과를 Canonical Source로 역승격하지 않고 정식 수정 절차를 따른다.

## Why

이 선택은 단순히 legacy 파일을 포기하는 결정이 아니다.

현재 프로젝트의 목적은 새 Architecture가 실제 production workflow에서 작동하는지 검증하는 것이다.

이미 장면 의도와 continuity를 알고 있는 C01–C04를 다시 생성하면 다음을 비교할 수 있다.

- Canonical Scene 정의가 충분한가
- forbidden / required element가 실제 생성에 유효한가
- C02→C03→C04 continuity가 충분한가
- Generation Run 기록 구조가 실제 사용 가능한가
- Review / Asset Promotion 흐름에 빠진 필드가 있는가

따라서 C01–C04는 migration 이후 첫 Architecture validation production set으로 사용한다.

## Alternatives Considered

### A. legacy binary가 발견될 때까지 migration을 계속 보류

채택하지 않는다.

이유:

- 현재 binary 위치가 확인되지 않음
- Cut 정의 migration은 이미 완료됨
- 새 Architecture 검증을 불필요하게 지연함

### B. 파일명 기록만으로 legacy Asset metadata를 생성

채택하지 않는다.

이유:

- 실제 binary / checksum / dimensions를 확인할 수 없음
- `available` 상태를 정직하게 만들 수 없음
- representative Asset을 허위 복원할 위험이 있음

### C. legacy 이미지를 복구한 뒤 그대로 representative로 지정

채택하지 않는다.

이유:

- 현재 approved Canonical definition과 새 Rules 기준으로 재검토되지 않았음
- 새 Architecture validation이라는 현재 목적을 달성하지 못함

## Consequences

- legacy Asset migration blocker는 종료한다.
- C02–C04의 과거 filename은 historical record로만 유지한다.
- C01의 기존 `GEN-CREATION-01-C01-A001` pending metadata는 실제 새 생성 결과가 정해질 때까지 provisional 상태로 유지한다.
- 새 생성 시 실제 Provider request마다 Run을 기록한다.
- accepted Result가 생겨도 자동 representative가 되지 않고 Asset Promotion과 representative selection을 거친다.
- C01–C04 regeneration이 Architecture validation 작업의 시작점이 된다.

## Related Sources

- `docs/progress/GENESIS_CREATION_MIGRATION.md`
- `docs/architecture/GENERATION_RUN_MODEL.md`
- `docs/architecture/ASSET_STORAGE_POLICY.md`
- `docs/rules/GENERATION_RULES.md`
- `content/old-testament/genesis/GEN-CREATION-01/`
