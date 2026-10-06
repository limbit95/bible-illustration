# Pre-Production Architecture Hardening Audit — 2026-10-06

> 상태: **PASSED / READY FOR NEW PRODUCTION**
>
> 목적: Storyboard refinement 이후 Architecture / Rules / Templates / Identity / Provider Integration / Asset lifecycle을 실제 production end-to-end 관점에서 다시 감사하고, 새 성경 일러스트 제작에 진입할 수 있는지 최종 판정한다.
>
> 이전 `ARCHITECTURE_COMPLETION_AUDIT.md`의 완료 판정을 더 깊은 범위로 확장·대체한다.

## 1. 감사 범위

현재 hardening branch 기준:

- Architecture 문서: 9개
- Rules 문서: 7개
- YAML Templates: 11개
- active Genesis production path: 0개
- retired identity tombstone: 36개
- ChatGPT 최소 Integration: provider / generation profile / prompt adapter 구성
- synthetic end-to-end lifecycle dry-run: PASS

실제 이미지 생성은 수행하지 않았다.

## 2. 감사에서 해소한 구조 문제

### 2.1 STEP 0 당시 stale handoff 문구

정식 Architecture 문서에 남아 있던:

- 이후 STEP에서 정한다
- 다음 STEP
- 최종 구조 확정 시 결정

같은 과거 시점 문구를 현재형 Source of Truth로 정리했다.

이미 후속 Architecture에서 확정된 책임은 해당 정식 문서로 연결하고,
실제로 아직 열어둔 구현 세부만 명시적으로 남겼다.

### 2.2 Continuity reset schema

Architecture / Rules가 `retain / change / reset`을 요구하면서
Template에는 reset path가 없던 불일치를 해소했다.

`templates/continuity.yaml`은 이제:

- `retain`
- `change`
- `reset`

을 모두 표현할 수 있다.

`partial_reset`에서는 이전 constraint에서 해제되는 path를 명시한다.

full `reset`은 이전 non-Library state를 기본 상속하지 않으므로 모든 path 열거를 강제하지 않는다.

### 2.3 Working Result → downstream reference lifecycle

Sequential Generation에서 accepted / user-approved working image를 다음 Cut의 visual anchor로 사용하는 흐름과
Asset Promotion 지연 원칙 사이의 모호성을 DEC-0009로 해소했다.

현재 규칙:

1. working / accepted Result는 planning 단계에서 anchor 후보가 될 수 있다.
2. 실제 downstream Provider request의 project-owned binary reference가 되면 먼저 Asset Promotion한다.
3. Git LFS ingest와 `availability: available`을 확인한다.
4. 다음 Run은 `references[].asset_ref`로 해당 Asset을 기록한다.
5. representative selection은 계속 뒤로 미룰 수 있다.

v1에서는 project-owned binary에 직접 `result_ref` dependency를 만들지 않는다.

### 2.4 Library semantic identity vs Image Asset identity

DEC-0008에 따라 두 identity를 분리했다.

- Canonical semantic definition → **Library Entity**
  - `library_id`
  - `library_type`
- 실제 장기 보존 image binary → **Image Asset**
  - `asset_id`

예:

~~~text
CHR-MOSES
= Library Entity

CHR-MOSES-A001
= Image Asset
~~~

현재 active Library data가 없어서 production data migration은 발생하지 않았다.

### 2.5 Episode-level visual direction ownership

DEC-0011에 따라 별도 Episode Visual Profile entity를 만들지 않는다.

책임:

- reusable visual identity → Library Visual Style Entity
- Episode / Segment-wide visual state → Continuity `baseline.visual`
- Cut-specific camera / composition / subject → Cut Canonical Scene
- Provider execution settings → Generation Profile / Run
- project-wide artistic rule → Visual Rules

working guide는 Canonical Source of Truth가 아니다.

### 2.6 Retired identity reuse prevention

DEC-0010에 따라 `content/identity-tombstones.yaml`을 추가했다.

현재 이전 Genesis production에서 사용된 36개 identity를 등록했다.

- Episode: 1
- Cut: 6
- Run: 12
- Result: 11
- Image Asset: 6

새 identity 발급 전:

1. current active tree
2. identity tombstone registry

를 모두 확인한다.

따라서 기존 `GEN-CREATION-01`과 그 하위 C01–C06 / Run / Result / Asset ID는 새 production에 재사용하지 않는다.

### 2.7 Storyboard transition_note 방향

각 Storyboard entry의 `transition_note` 방향을 고정했다.

~~~text
previous active Cut
        ↓
current Storyboard entry
~~~

즉 현재 entry로 **들어오는 high-level transition intent**다.

첫 active Cut은 기본적으로 `null`이다.

실제 continuity mode와 constraint는 계속 Continuity가 소유한다.

### 2.8 Result ↔ Asset linkage

Asset Promotion 시 Run Result에 mutable reverse link를 중복 저장하지 않는다.

Canonical provenance:

~~~text
Image Asset metadata
  source.kind: generation_result
  source.result_id: ...
~~~

Result → Asset 조회는 Asset metadata의 `source.result_id`를 기준으로 도출한다.

### 2.9 Removed Asset tombstone

Image Asset binary가 remove되어도 metadata를 삭제하지 않는다.

~~~yaml
availability: removed

removal:
  removed_at: ...
  reason: ...
  replacement_asset_id: ...
~~~

형태로 역사 기록을 유지할 수 있다.

### 2.10 ChatGPT 최소 Integration

현재 실제 첫 production에 필요한 최소 Integration을 추가했다.

- `integrations/chatgpt/provider.yaml`
- `integrations/chatgpt/generation-profiles/chat-native-standard.yaml`
- `integrations/chatgpt/prompt-adapters/canonical-cut-v1.md`

확인할 수 없는 내부 model/version/hidden prompt는 추측하지 않는다.

Provider Integration은 Canonical Scene을 대체하지 않는다.

## 3. Template 감사

YAML Template은 11개를 유지한다.

이번 hardening에서 schema 책임을 맞추기 위해 필요한 최소 변경만 수행했다.

핵심 확인:

- Storyboard: 기존 5개 entry field 유지
  - order
  - cut_id
  - scripture_anchor
  - beat
  - transition_note
- Continuity: reset path 표현 가능
- Library definition: `library_id / library_type`
- Provider Binding: `canonical_ref.library_id`
- Generation Run: Library snapshot은 `library_id`, project-owned binary reference는 `asset_ref`
- Asset metadata: removal tombstone 표현 가능

Storyboard에 relation enum이나 camera / lighting metadata를 새로 추가하지 않았다.

## 4. Synthetic lifecycle dry-run

상세 기록:

`docs/progress/PREPRODUCTION_HARDENING_DRY_RUN.md`

검증 경로:

~~~text
Scripture Work Unit
  ↓
Episode
  ↓
Storyboard
  ↓
Storyboard Preflight
  ↓
Cut
  ↓
Continuity
  ↓
ChatGPT Integration
  ↓
Run / Result Review
  ↓
Result → Reference Asset Promotion
  ↓
next Run asset_ref
  ↓
Result Review
  ↓
Representative Asset
  ↓
Production Complete derivation
~~~

실제 이미지나 production identity를 만들지 않은 synthetic fixture로 검증했다.

결과:

**PASS**

## 5. 정적 감사 결과

현재 정식 Architecture 문서에서:

- stale `다음 STEP` 작업 지시: 없음
- stale `의도적으로 미확정` 섹션: 없음
- retired Genesis production ID 예시: 없음
- `Library Asset` semantic 용어 충돌: 없음
- Storyboard relation 새 enum: 없음
- active Genesis production data: 없음

현재 구조 수:

- Architecture: 9
- Rules: 7
- Templates: 11

## 6. 의도적으로 열어두는 비-blocker

다음 항목은 현재 Architecture blocker가 아니다.

- 실제 Library Entity instance
- 66권 Book Code registry의 별도 파일화
- Provider별 API / SDK / retry 코드
- 특정 Provider external resource ID
- Provider별 reference weight 실제 값
- Production Master의 단일 기본 format 강제 여부
- web derivative pipeline
- CDN / object storage vendor
- presentation / site schema
- Library Reference 전용 non-Cut Generation Run target
- 자동 invariant / checksum / LFS validation CI

실제 요구가 발생할 때 현재 Architecture 책임 안에서 최소 범위로 확장한다.

## 7. 최종 판정

**PASS — PRE-PRODUCTION ARCHITECTURE HARDENING COMPLETE**

현재 Architecture / Rules / Templates / Identity / Integration / Asset lifecycle은
새 production을 처음부터 진행할 수 있는 일관된 기준선을 제공한다.

현재 Architecture blocker는 없다.

## 8. 새 Genesis production 진입 순서

1. Scripture Work Unit 확인
2. current tree + `content/identity-tombstones.yaml`에서 ID 사용 이력 확인
3. **기존 `GEN-CREATION-01`을 재사용하지 않고 새 Episode identity 발급**
4. Storyboard 신규 설계
5. Multi-Cut이면 Storyboard Production Preflight
6. Cut Canonical Scene 정의
7. Continuity 정의
8. 필요한 Library Entity / Historical Reference 준비
9. 사용자 검토
10. ChatGPT Integration으로 한 장면씩 Generation
11. 다음 Cut의 actual binary reference가 되는 Result는 먼저 Asset Promotion
12. Review / representative selection / production complete 도출

이전 Genesis 이미지나 장면별 micro-rule은 새 production의 입력으로 복원하지 않는다.
