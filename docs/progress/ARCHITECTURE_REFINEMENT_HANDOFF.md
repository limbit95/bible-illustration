# Architecture Refinement Handoff — after Genesis image-generation tests

> 상태: **ACTIVE HANDOFF / 2026-10-06**
>
> 목적: Genesis Creation 이미지 테스트를 일시 중지하고, 실제 테스트에서 확인된 문제와 결정 사항을 바탕으로 Architecture / Rules 보완 작업을 새 채팅에서 이어가기 위한 진행 기록이다.

## 1. 현재 판단

이미지 테스트의 목적은 최종 Asset 생산보다 **Architecture가 실제 제작에서 원하는 결과를 유도하는지 검증하는 것**이었다.

테스트 결과 다음 방향이 확인되었다.

- 한 번에 여러 이미지를 일괄 생성하는 것보다 **Scripture Work Unit 기반 순차 제작**이 실제 수정 비용이 낮다.
- 한 구절은 필요에 따라 여러 Cut으로 나눌 수 있고, 여러 절이 하나의 beat라면 묶을 수 있다.
- 모든 인접 이미지를 강제로 연결하면 새로운 장면 연출이 제한된다.
- 동일 사건의 단계적 진행은 Continuity가 중요하지만, 새로운 창조 국면 / visual subject에서는 Transition이 필요하다.
- Genesis Creation의 구체 palette / texture / lighting progression은 Episode-specific profile이어야 한다.
- Presentation text는 Production Master와 분리하고, 중요한 본문은 direct quote 우선 / narration은 설명용으로 제한하는 방향이 적절하다.

이 판단은 이미 DEC-0005 / DEC-0006 및 관련 Rules에 반영되어 있다.

## 2. 이미지 테스트 상태

Genesis Creation 이미지 테스트는 **일시 중지**한다.

현재 생성된 테스트 이미지는 Architecture 검증을 위한 working output이며, 새로 명시적으로 승격하지 않는 한 Canonical Asset / representative / Production Master로 자동 취급하지 않는다.

테스트 이미지는 사용자가 별도 로컬 백업으로 보관한다. 전체 architecture refinement가 끝난 뒤 필요한 이미지만 다시 선별한다.

## 3. Storyboard 현재 상태 확인

현재 Storyboard Architecture 자체는 이미 존재한다.

정식 책임:

- Cut 표시 순서
- Cut ID
- Scripture Anchor
- 짧은 beat
- 필요 시 transition note

`templates/storyboard.yaml`의 현재 최소 필드도 위 책임과 일치한다.

또한 DEC-0005 / MASTER_RULES에는, 사용자가 본문을 주었을 때 여러 장면이 필요하면 Assistant가 먼저 짧은 Storyboard를 제안하는 advisory rule이 존재한다.

현재 advisory 항목:

- Cut 수
- 각 Cut의 Scripture Anchor
- visual beat
- RETAIN
- DELTA
- FORBIDDEN LEAP

그러나 실제 이미지 테스트 결과, **Storyboard 제작 규칙 자체는 아직 충분히 강하지 않다.**

## 4. 다음 Architecture 보완의 최우선 과제 — Storyboard Rules 강화

새 채팅에서 가장 먼저 아래 항목을 설계 / 검토한다.

### 4.1 Scripture coverage

- Episode / Work Unit의 본문이 Storyboard에서 누락되지 않는지
- 같은 본문이 이유 없이 중복되는지
- 여러 절을 합치거나 한 절을 여러 Cut으로 나눈 근거가 명확한지

### 4.2 Cut boundary / beat granularity

- 한 Cut이 하나의 명확한 visual beat를 갖는지
- 한 화면에 너무 많은 사건을 압축하지 않는지
- 반대로 의미 없는 미세 Cut을 과도하게 늘리지 않는지
- 한 구절 = 한 이미지로 고정하지 않되, Cut 분할 이유를 설명할 수 있는지

### 4.3 Scene Relation planning

Storyboard 단계에서 인접 Cut마다 다음을 먼저 판단한다.

- Continuity-oriented
- Transition-oriented

Canonical 저장은 새 enum을 만들기보다 기존:

- continuity mode: `continue | partial_reset | reset`
- `storyboard.transition_note`

를 사용한다.

### 4.4 Visual repetition prevention

연속 Cut이 같은 subject / 구도 / 스케일 / 낮밤 분할 이미지를 반복하지 않는지 검토한다.

새 visual treatment가 본문을 더 잘 전달하는 경우:

- camera distance
- viewpoint
- scale
- dominant subject
- lighting concept
- composition rhythm

을 의도적으로 바꿀 수 있어야 한다.

단, 시각적 다양성을 위해 Scripture fact나 Story chronology를 왜곡하지 않는다.

### 4.5 Key Scripture vs explanatory frame

Storyboard 단계에서 필요하면 각 beat가:

- Key Scripture 중심 장면인지
- explanatory / transitional 장면인지

판단할 수 있어야 한다.

이는 Presentation text 전략에 영향을 주지만, Storyboard에 긴 display text를 저장하지는 않는다.

### 4.6 Episode rhythm review

개별 Cut뿐 아니라 Episode 전체를 보고:

- 장면의 거리 변화
- 넓은 establishing / 중간 / 세부 장면의 리듬
- Continuity와 Transition의 배치
- 반복 구도
- 시각적 climax / rest

를 검토한다.

### 4.7 Storyboard / Cut / Continuity 책임 경계

강화 규칙을 추가하더라도 책임 중복을 만들지 않는다.

- Storyboard: 순서 / Scripture Anchor / beat / transition intent
- Cut: Canonical Scene Specification
- Continuity: 실제 retain / change / reset constraints

Storyboard에 Cut 상세 scene specification이나 Continuity 전체를 복제하지 않는다.

## 5. Template 변경은 규칙 설계 이후

현재 `templates/storyboard.yaml`은:

- order
- cut_id
- scripture_anchor
- beat
- transition_note

만 가진다.

Storyboard Rules 강화 후에도 이 최소 스키마로 충분한지 먼저 검토한다.

새 필드를 추가하는 것은 자동 전제가 아니다. 현재 `transition_note`와 Continuity transition data로 충분하면 Template은 유지한다.

필드 추가가 실제 Source of Truth 책임을 명확하게 개선할 때만 Architecture / Template 변경을 제안한다.

## 6. 새 채팅 시작 순서

새 채팅에서는 반드시:

1. `AGENTS.md`
2. `docs/progress/CURRENT.md`
3. `docs/architecture/OVERVIEW.md`
4. `docs/architecture/EPISODE_CUT_MODEL.md`
5. `docs/architecture/CONTINUITY_MODEL.md`
6. `docs/rules/MASTER_RULES.md`
7. `docs/rules/CONTINUITY_RULES.md`
8. `docs/rules/GENERATION_RULES.md`
9. `templates/storyboard.yaml`
10. 이 문서

를 확인한다.

그 뒤 **Storyboard production rules 강화**를 첫 Architecture refinement 작업으로 시작한다.

이미지 생성 재개는 Storyboard Rules 강화 및 관련 Architecture / Rules / Template 충돌 검토가 끝난 뒤 진행한다.
