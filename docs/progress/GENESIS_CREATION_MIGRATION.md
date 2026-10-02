# Genesis Creation CUT 1–4 Migration

> 상태: **CANONICAL DEFINITION MIGRATED / AUDIT PASSED / ASSET MIGRATION PARTIAL**
> 마지막 갱신: 2026-10-02
>
> 목적: 기존 Genesis Creation CUT 1–4 작업을 현재 Canonical Repository Structure로 이관하기 전에, 기존 확정 정보와 이관 blocker를 명확히 기록한다.

## 1. Source

현재 bible-illustration 저장소 자체에는 기존 Genesis Creation CUT 파일이나 image binary가 존재하지 않는다.

기존 작업 상태는 이전 작업 인수인계 문서 `bible_illustration_chat_handoff_v0.4(1).md`에서 복원했다.

해당 handoff는 historical migration source이며, 현재 Architecture / Rules를 대체하지 않는다.

## 2. 기존 Episode 범위

기존 작업은 창세기 1:1–13을 다음 흐름으로 계획했다.

1. CUT 1 — 창 1:1
2. CUT 2 — 창 1:2
3. CUT 3 — 창 1:3
4. CUT 4 — 창 1:4–5
5. CUT 5 — 창 1:6–8
6. CUT 6 — 창 1:9–10
7. CUT 7 — 창 1:11–12
8. CUT 8 — 창 1:12–13

현재 8-cut 구성은 **Genesis Creation의 기존 작업 계획**으로만 취급한다.

전역적으로 Chapter를 8컷에 맞추는 legacy rule은 현재 Production Rules에서 폐기되어 있다.

## 3. Canonical Episode mapping 제안

~~~text
Episode ID: GEN-CREATION-01
Book path: content/old-testament/genesis/GEN-CREATION-01/
Primary Scripture: Genesis 1:1–13
Current migration scope: C01–C04
Future work resumes from: C05
~~~

Episode는 migration 중 `draft / revision 1`로 유지한다.

C05–C08 정의가 아직 없으므로 migration 시 Storyboard에는 우선 실제로 복원된 C01–C04만 등록하고, Episode가 approved 되기 전에 C05 이후를 같은 revision 1에서 확장한다.

## 4. CUT 1 — Genesis 1:1

Legacy state:

- 방향 확정
- 별도 구체적 일러스트 없음
- 검은 화면
- 사이트 presentation layer에 개역한글 Genesis 1:1 직접 인용
- 목적: 시각적으로 특정하기 어려운 시작을 과도하게 상상하지 않는 최소 구현

Migration mapping:

~~~text
Cut ID: GEN-CREATION-01-C01
Storyboard order: 10
Scripture Anchor: GEN 1:1
Asset: required — actual black-screen illustration Asset
~~~

Resolution:

`DEC-0002 — Presentation-only Cut Output Mode`는 채택하지 않는다.

CUT 1은 기존 Architecture를 그대로 사용한다. 별도 장면 요소 없이 **완전한 검은 화면 자체를 실제 Cut-owned image Asset으로 제작**하고, 그 Asset을 representative로 선정한다.

성경 직접 인용과 장·절 표시는 이미지에 bake-in하지 않고 presentation layer에서 분리한다.

## 5. CUT 2 — Genesis 1:2

Legacy state:

- 이미지 1차 확정
- 기록된 원본 파일명:
  `wide_dramatic_moody_cinematic_scene_of_a_vast.png`
- 짙은 흑청색 원초적 수면
- 넓은 어둠과 안개/흐름
- 완성된 현대적 바다 풍경보다 원초적 수면
- 강한 광원 없음
- 이후 Cut의 공간적 기반

Migration mapping:

~~~text
Cut ID: GEN-CREATION-01-C02
Storyboard order: 20
Scripture Anchor: GEN 1:2
~~~

Asset blocker:

현재 연결된 Project/Library/저장소 검색에서 위 파일명의 실제 binary는 찾지 못했다.

따라서 아직:

- Asset ID 발급
- checksum 기록
- Git LFS ingest
- representative selection
- availability: available

을 수행할 수 없다.

## 6. CUT 3 — Genesis 1:3

Legacy state:

- 이미지 확정
- 기록된 원본 파일명:
  `a_cinematic_widescreen_high_detail_atmospheric_s.png`
- CUT 2와 같은 세계/수면
- 오른쪽 부근에서 따뜻한 금백색 빛 첫 등장
- 태양/달/별 없음
- 오른쪽 광원은 CUT 4로 계승

Migration mapping:

~~~text
Cut ID: GEN-CREATION-01-C03
Storyboard order: 30
Scripture Anchor: GEN 1:3
~~~

Asset blocker:

현재 실제 binary를 찾지 못했다.

binary가 복구되기 전에는 Canonical Asset을 available 상태로 등록하지 않는다.

## 7. CUT 4 — Genesis 1:4–5

Legacy state:

- 이미지 확정
- 기록된 원본 파일명:
  `a_wide_dramatic_cinematic_landscape_sky_seascape.png`
- CUT 3의 바로 다음 순간
- 오른쪽 광원 유지
- 오른쪽은 밝아지고 왼쪽은 깊은 어둠 유지
- 새 빛의 폭발이 아니라 기존 빛과 어둠의 구별/질서화

Migration mapping:

~~~text
Cut ID: GEN-CREATION-01-C04
Storyboard order: 40
Scripture Anchor: GEN 1:4–5
~~~

Asset blocker:

현재 실제 binary를 찾지 못했다.

## 8. Continuity mapping

기존 작업에서 확인된 continuity를 현재 모델로 옮기면 다음 방향이 된다.

### C01 → C02

C01은 실제 검은 화면 Asset을 사용하지만 구체적 세계 요소를 담지 않는다.

C02에서 원초적 수면 세계가 처음 구체적으로 시각화되므로, C01의 검정 화면 자체를 물리적 공간 Continuity로 강제 상속하지 않는다. Transition은 장면 도입의 성격을 반영해 migration audit에서 최소 제약으로 확정한다.

### C02 → C03

~~~text
mode: continue
retain:
- primordial water world
- broad camera/world orientation
change:
- warm light appears from right
~~~

### C03 → C04

~~~text
mode: continue
retain:
- primordial water world
- right-side warm light direction
- no sun/moon/stars
change:
- distinction between light and darkness becomes stronger
~~~

CUT 5 진입 시 유지해야 할 legacy anchor:

- 원초적 수면 존재
- 빛/어둠 구별 시작
- 오른쪽 따뜻한 빛 / 왼쪽 깊은 어둠
- 태양/달/별 없음
- 육지 없음
- 식물/동물/인간 없음

이 값은 전역 Rule이 아니라 GEN-CREATION-01의 구체 Continuity 데이터로 migration해야 한다.

## 9. Migration 시 상태 처리

기존 handoff의 “방향 확정 / 이미지 확정”을 새 Architecture의 `definition_status: approved`로 자동 변환하지 않는다.

이유:

- 당시에는 현재 Canonical Cut schema가 존재하지 않았다.
- 새 Cut 정의와 Continuity를 먼저 작성하고 현재 Scripture / Rules 기준으로 migration audit가 필요하다.

권장:

~~~text
Episode: draft / rev1
C01–C04: in_review / rev1
~~~

migration audit 통과 후 현재 Canonical definition을 approved로 전환한다.

이미지의 과거 확정 상태는 별도 legacy provenance로 이해하되, binary가 복구되지 않은 C02–C04를 현재 representative Asset으로 허위 등록하지 않는다.

## 10. 현재 Blockers

### BLOCKER A — C02–C04 image binary

현재 확인된 것은 파일명과 시각적 확정 기록뿐이다.

실제 binary가 확보되지 않으면:

- 기존 이미지를 Canonical Asset으로 migration할 수 없음
- 대표 Asset을 현재 available 상태로 복원할 수 없음
- 해당 Cut production complete를 복원할 수 없음

단, Cut definition / Storyboard / Continuity migration 자체는 binary와 독립적으로 진행 가능하다.

## 11. 다음 실행 순서

1. GEN-CREATION-01 Episode / Storyboard / C01–C04 / Continuity 생성
2. C01 검은 화면 Asset 제작 및 representative 등록
3. C02–C04 legacy binary가 확보되면 Asset Promotion + LFS ingest
4. binary 미확보 시 C02–C04 Asset migration은 unresolved로 기록
5. migration audit
6. C01–C04 Canonical definition approval 검토
7. CUT 5 설계 재개


## 12. Canonical Definition Migration Result

2026-10-02 기준 다음 Canonical 파일을 생성했다.

~~~text
content/old-testament/genesis/GEN-CREATION-01/
├─ episode.yaml
├─ storyboard.yaml
├─ continuity.yaml
└─ cuts/
   ├─ C01/cut.yaml
   ├─ C02/cut.yaml
   ├─ C03/cut.yaml
   └─ C04/cut.yaml
~~~

또한 `content/episode-sequence.yaml`에 `GEN-CREATION-01`을 order 10으로 등록했다.

상태:

- Episode: `draft / revision 1`
- C01–C04: `approved / revision 1`
- 과거 handoff의 “확정” 상태를 현재 Architecture의 `approved`로 자동 변환하지 않음

## 13. C01 Asset Migration Status

C01용 Asset ID를 다음과 같이 예약했다.

~~~text
GEN-CREATION-01-C01-A001
~~~

metadata 위치:

~~~text
content/old-testament/genesis/GEN-CREATION-01/cuts/C01/assets/
  GEN-CREATION-01-C01-A001.asset.yaml
~~~

현재 상태:

~~~text
availability: pending_ingest
~~~

실제 PNG binary는 아직 Canonical Git LFS에 ingest되지 않았다.

현재 연결된 GitHub 도구에는 Git LFS object upload 기능이 없으므로 binary가 없는 상태에서 `available`로 허위 기록하거나 일반 Git blob으로 우회 커밋하지 않았다.

따라서 아직 `asset-selection.yaml`도 만들지 않았다.

## 14. Canonical Migration Audit

2026-10-02 migration audit 결과:

~~~text
25 / 25 checks PASSED
~~~

검증 항목:

- Canonical Episode Sequence에 Episode 등록
- Episode ID / primary Scripture / revision 상태
- Storyboard C01–C04 참조와 order 10/20/30/40
- 모든 Cut의 Episode 소속 및 in_review / revision 1
- 인접 active Cut 3쌍 transition 존재
- C02–C04 primordial-waters Segment
- 태양/달/별/육지/식물/동물/인간 부재 baseline
- C01 Asset이 pending_ingest이며 available로 허위 기록되지 않음
- C01 binary가 Git에 잘못 커밋되지 않음
- binary ingest 전 representative selection이 생성되지 않음

Canonical definition migration 자체는 audit를 통과했다.

## 15. Remaining Work

### Definition approval

2026-10-02 사용자 승인으로 C01–C04 Canonical Scene 정의를 모두 `approved / revision 1`로 전환했다.

Episode는 C05 이후 active Cut 정의가 아직 없으므로 현재 `draft / revision 1`을 유지한다.

### C01 binary

실제 완전한 black-screen PNG를 생성하고 Git LFS object로 ingest해야 한다.

그 후:

1. SHA-256 / dimensions / size 기록
2. availability를 `available`로 변경
3. `asset-selection.yaml` 생성
4. current Cut revision 기준 representative로 선정
5. Review 기록

### C02–C04 legacy binary

기존 파일명의 실제 binary는 여전히 미확보다.

binary가 복구되면 현재 Cut 정의에 대해 다시 Review한 뒤 Asset Promotion을 수행한다.

복구되지 않으면 과거 filename/status 기록은 historical provenance로만 유지하며 새 Canonical 기준으로 재생성 여부를 결정한다.

## 16. Next

1. C01 Git LFS binary ingest 및 representative 등록
2. C02–C04 legacy binary 복구 또는 새 Canonical 기준 재생성 방침 결정
3. Asset migration / representative Review 완료
4. migration 최종 감사 및 완료 처리
5. C05 — Genesis 1:6–8 설계 재개
