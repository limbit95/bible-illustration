# Genesis Creation CUT 1–4 Migration Readiness

> 상태: **BLOCKED / REVIEW READY**
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
Asset: intentionally none
~~~

Blocker:

현재 production complete 모델은 모든 active Cut에 representative Asset을 요구한다.

따라서 CUT 1을 억지로 검은 PNG Asset으로 만들지 않고 `DEC-0002 — Presentation-only Cut Output Mode` 승인 여부를 먼저 결정해야 한다.

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

C01은 presentation-only 방향이므로 DEC-0002 결정 후 필요한 transition을 최소 정의한다.

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

### BLOCKER A — presentation-only Cut 모델

`DEC-0002` 승인 필요.

### BLOCKER B — C02–C04 image binary

현재 확인된 것은 파일명과 시각적 확정 기록뿐이다.

실제 binary가 확보되지 않으면:

- 기존 이미지를 Canonical Asset으로 migration할 수 없음
- 대표 Asset을 현재 available 상태로 복원할 수 없음
- 해당 Cut production complete를 복원할 수 없음

단, Cut definition / Storyboard / Continuity migration 자체는 binary와 독립적으로 진행 가능하다.

## 11. 다음 실행 순서

1. DEC-0002 검토 및 승인
2. 승인 시 Architecture / Rules / Cut Template 최소 수정
3. GEN-CREATION-01 Episode / Storyboard / C01–C04 / Continuity 생성
4. C02–C04 legacy binary가 उपलब्ध하면 Asset Promotion + LFS ingest
5. binary 미확보 시 Asset migration은 unresolved로 기록
6. migration audit
7. C01–C04 Canonical definition approval 검토
8. CUT 5 설계 재개
