# Pre-Production Architecture Hardening Dry Run — 2026-10-06

> 상태: **PASS**
>
> 목적: 실제 이미지 생성이나 active production data 생성 없이, 현재 Architecture / Rules / Templates를 따라 2-Cut production lifecycle을 끝까지 통과시켜 구조적 blocker를 확인한다.
>
> 이 문서의 `SYNTH-*` ID는 **비할당 fixture 표기**이며 production identity가 아니다.

## 1. 검증 경로

다음 lifecycle을 순서대로 검증했다.

~~~text
Scripture Work Unit
  ↓
Episode
  ↓
Storyboard (2 Cuts)
  ↓
Storyboard Production Preflight
  ↓
Cut Canonical Definitions
  ↓
Continuity partial_reset
  ↓
ChatGPT Integration resolution
  ↓
Run 1 / Result 1 Review
  ↓
Result 1 → Reference Asset Promotion
  ↓
Run 2 uses Asset reference
  ↓
Result 2 Review
  ↓
Representative Asset selection
  ↓
Production Complete derivation
~~~

실제 Provider request나 이미지 binary는 생성하지 않았다.

## 2. Identity allocation gate

실제 production ID 발급 시 다음 두 곳을 모두 확인하는 흐름을 검증했다.

1. current active tree
2. `content/identity-tombstones.yaml`

이전 Genesis production의 Episode / Cut / Run / Result / Asset ID는 tombstone에 남아 있으므로 새 identity로 재사용할 수 없다.

**결과: PASS**

## 3. Episode / Storyboard

개념 fixture:

~~~yaml
episode_id: SYNTH-EPISODE

storyboard:
  - order: 10
    cut_id: SYNTH-C01
    scripture_anchor: [...]
    beat: fixture beat A
    transition_note: null

  - order: 20
    cut_id: SYNTH-C02
    scripture_anchor: [...]
    beat: fixture beat B
    transition_note: 같은 사건의 다음 단계로 들어오는 전환
~~~

검증:

- Storyboard가 order / scripture_anchor / beat를 소유한다.
- 첫 active Cut의 `transition_note`는 null.
- C02의 `transition_note`는 **C01 → C02 incoming relation**을 표현한다.
- Cut 상세 Scene과 Continuity constraint를 Storyboard에 중복하지 않는다.

**결과: PASS**

## 4. Cut / Library Entity

Cut은 최소 Canonical Scene만 소유한다.

Library reference가 필요할 경우 semantic identity는 `library_id`를 사용한다.

개념:

~~~yaml
library_refs:
  style:
    library_id: STY-SYNTHETIC
~~~

Image binary의 `asset_id`와 Library semantic identity의 `library_id`가 분리되어 있다.

**결과: PASS**

## 5. Continuity partial_reset

C01 → C02가 일부 상태는 유지하고 일부 시각 축은 새로 설정하는 경우:

~~~yaml
transitions:
  - from: SYNTH-C01
    to: SYNTH-C02
    mode: partial_reset

    retain:
      - world.location

    change:
      - path: event.phase
        from: start
        to: progressed

    reset:
      - visual.camera_axis
~~~

검증:

- `partial_reset`에서 reset path를 물리 schema로 표현 가능하다.
- 같은 path를 retain / change / reset에 모순되게 중복하지 않는다.
- full `reset`은 이전 non-Library state를 기본 상속하지 않으며 모든 path 열거를 요구하지 않는다.
- Library Entity identity는 reset path로 삭제하는 개념이 아니다.

**결과: PASS**

## 6. Episode-level visual direction

별도 Episode Visual Profile entity를 만들지 않고 책임을 분해한다.

- reusable style identity → Library Visual Style Entity
- segment-wide palette / texture / lighting state → Continuity baseline.visual
- Cut-specific camera / composition → Cut Canonical Scene
- Provider execution aspect ratio / resolution / weight → Generation Profile / Run

working guide 자체를 Canonical owner로 사용하지 않는다.

**결과: PASS**

## 7. ChatGPT Integration resolution

현재 registry에서 `chatgpt` Provider를 찾고:

- provider config → `integrations/chatgpt/provider.yaml`
- generation profile → `chat-native-standard`
- prompt adapter → `canonical-cut` rev1

로 resolve할 수 있다.

내부 model/version 또는 hidden prompt는 추측하지 않고 unknown / unavailable을 허용한다.

**결과: PASS**

## 8. Run 1 / Result Review

Run 1은 Cut revision + Git source commit을 snapshot한다.

Run execution status와 Result Review decision을 분리한다.

개념:

~~~text
Run 1: completed
Result 1: accepted
~~~

accepted는 representative Asset 선정과 동일하지 않다.

**결과: PASS**

## 9. Working Result → downstream reference

Result 1이 사용자 승인 working image 또는 accepted Result이고 C02의 실제 reference가 된다고 가정한다.

DEC-0009에 따라 Run 2 제출 전에 Result 1을 먼저 Asset으로 Promotion한다.

~~~yaml
asset_id: SYNTH-C01-A001

source:
  kind: generation_result
  result_id: SYNTH-C01-R001-O01

availability: available
~~~

그 뒤 Run 2는:

~~~yaml
references:
  - asset_ref: SYNTH-C01-A001
    role: continuity
    source_cut_id: SYNTH-C01
~~~

로 기록한다.

검증:

- 임시 working binary를 직접 Run dependency로 남기지 않는다.
- direct `result_ref` dependency가 필요 없다.
- Asset Promotion은 reference dependency가 되는 Result에만 선행 강제된다.
- representative selection은 아직 미룰 수 있다.

**결과: PASS**

## 10. Result ↔ Asset provenance

Asset metadata의:

~~~yaml
source:
  kind: generation_result
  result_id: SYNTH-C01-R001-O01
~~~

를 Canonical linkage로 사용한다.

Run Result 쪽에 mutable reverse `promoted_asset_ids`를 중복 저장하지 않는다.

Result → Asset 조회는 Asset metadata의 `source.result_id` 검색으로 도출한다.

**결과: PASS**

## 11. Representative selection / Production Complete

현재 approved Cut revision 기준으로:

1. Result Review accepted
2. promoted Asset availability=available
3. representative selection 존재
4. 현재 Canonical snapshot에 대한 적합성 확인

이 모두 충족되면 Cut production complete를 도출할 수 있다.

Episode production complete는 모든 active Cut이 production complete일 때 도출한다.

별도 mutable production status가 필요하지 않다.

**결과: PASS**

## 12. Asset removal

Asset binary를 retire/remove해야 하는 경우 metadata를 삭제하지 않고:

~~~yaml
availability: removed

removal:
  removed_at: ...
  reason: ...
  replacement_asset_id: null
~~~

형태로 tombstone을 유지할 수 있다.

**결과: PASS**

## 13. Dry-run 결론

**PASS — end-to-end structural blocker 없음**

이번 hardening으로 기존 감사에서 확인된 다음 문제를 해소했다.

- STEP 0 당시의 stale handoff 문구
- Continuity `reset` path의 Template 표현 누락
- working Result를 downstream reference로 사용할 때의 lifecycle ambiguity
- Episode-specific visual direction의 Canonical owner 모호성
- Library semantic identity와 Image Asset identity의 `asset_id` 충돌
- retired production ID 재사용 위험
- `transition_note` 방향 모호성
- Run Result ↔ Asset reverse link 중복 위험
- ChatGPT Integration 최소 실행 정의 부재

실제 이미지는 생성하지 않았고 active Episode/Cut/Asset도 만들지 않았다.
