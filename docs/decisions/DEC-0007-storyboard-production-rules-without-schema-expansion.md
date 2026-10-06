# DEC-0007 — Strengthen Storyboard Production Rules without expanding the Storyboard schema

> 상태: accepted
> 날짜: 2026-10-06
> 대체: none
> 대체됨: none
> 관련: DEC-0005, DEC-0006

## Context

실제 이미지 제작 검증에서 Storyboard Architecture 자체보다
**Storyboard를 어떻게 설계하고 검토할 것인지에 대한 Production Rule 부족**이 더 큰 문제로 확인되었다.

기존 Storyboard는 이미 다음 최소 책임을 가진다.

- Cut 표시 순서
- Cut ID
- Scripture Anchor
- 짧은 beat
- 필요 시 transition note

반면 다음 판단은 충분히 명문화되어 있지 않았다.

- Scripture coverage
- Cut boundary / beat granularity
- Scene Relation planning
- visual repetition prevention
- Key Scripture / explanatory frame 판단
- Episode-level rhythm

이 문제를 해결하기 위해 Storyboard schema에 새 필드를 추가하면
Cut Canonical Scene 또는 Continuity constraint와 책임이 중복될 위험이 있다.

## Decision

Storyboard의 현재 최소 schema를 유지한다.

`templates/storyboard.yaml`의 구조는 다음 필드만 유지한다.

- `order`
- `cut_id`
- `scripture_anchor`
- `beat`
- `transition_note`

새로운 production 판단은 **Rules와 Preflight 절차**로 강화한다.

### 1. Scripture Coverage

Episode / Scripture Work Unit의 의미 있는 본문 흐름이 이유 없이 누락되거나 중복되지 않았는지 검토한다.

동일 Scripture Anchor가 여러 Cut에 걸치는 것은 허용하지만
각 Cut의 beat 역할이 실질적으로 달라야 한다.

### 2. Beat Granularity

한 Cut에는 가능한 한 하나의 명확한 visual beat를 둔다.

분할과 통합은 절 번호가 아니라
사건·상태·시간·장소·시각적 초점 변화와 의미 보존 여부로 판단한다.

### 3. Scene Relation Planning

Storyboard 단계에서 인접 Cut의 관계를
Continuity-oriented / Transition-oriented 관점으로 먼저 판단할 수 있다.

새 Canonical enum은 만들지 않는다.

`transition_note`에는 high-level intent만 기록하고,
실제 `continue | partial_reset | reset`과 retain / change / reset constraint는 Continuity가 소유한다.

### 4. Visual Repetition Prevention

본문 의미가 달라지는데도 같은 visual message가 반복되는지 Storyboard 전체에서 검토한다.

필요하면 dominant subject, camera distance, viewpoint, scale, composition, lighting concept, visual rhythm의 변경 가능성을 검토한다.

구체 값은 Storyboard가 아니라 Cut / Continuity에서 정의한다.

### 5. Key Scripture / Explanatory Frame

Storyboard 설계 중 presentation intent를 판단할 수 있다.

하지만 별도 enum을 추가하지 않고,
직접 인용문 전문이나 narration 문장을 Storyboard에 중복 저장하지 않는다.

### 6. Episode-level Rhythm

Multi-Cut Storyboard는 개별 Cut뿐 아니라 전체 시퀀스의 거리, 전환, 반복, climax, breathing frame, 분위기 흐름을 검토한다.

기계적인 장면 비율이나 quota는 두지 않는다.

## Why no new field

현재 필드만으로 production planning의 목적을 충분히 달성할 수 있다.

- coverage는 `scripture_anchor + beat`와 Episode primary Scripture를 교차검증할 수 있다.
- granularity는 `scripture_anchor + beat`로 판단할 수 있다.
- Scene Relation의 high-level intent는 `transition_note`로 표현할 수 있다.
- 구체 장면 설계는 Cut의 책임이다.
- 구체 continuity constraint는 Continuity의 책임이다.
- visual repetition / Episode rhythm은 review concern이며 별도 Canonical data field가 아니다.
- Key Scripture / explanatory 판단은 presentation planning concern이며 copy 자체는 presentation layer가 소유한다.

따라서 새 필드를 추가하면 얻는 이점보다 책임 중복과 migration 비용이 더 크다.

## Alternatives Considered

### Storyboard에 relation type 필드 추가

채택하지 않는다.

기존 Continuity Model의 `continue / partial_reset / reset`과 역할이 중복되고
planning relation과 Canonical transition mode를 혼동할 수 있다.

### camera / composition / lighting 필드 추가

채택하지 않는다.

Storyboard가 Cut Canonical Scene과 중복된다.

### frame type enum 추가

현재는 채택하지 않는다.

Key Scripture / explanatory 판단은 필요하지만
현 단계에서는 별도 Canonical Source of Truth를 요구하지 않는다.

실제 presentation system이 독립 schema를 필요로 할 때 다시 검토한다.

### Episode rhythm metadata 추가

채택하지 않는다.

rhythm은 Storyboard 전체를 대상으로 하는 review 판단이며
현재는 저장 필드보다 Production Rule이 적절하다.

## Consequences

- 기존 Storyboard data migration이 필요 없다.
- Storyboard / Cut / Continuity 책임 경계를 유지한다.
- 이미지 생성 전에 coverage / granularity / relation / repetition / rhythm 문제를 발견할 수 있다.
- 향후 실제 운영에서 현재 필드로 표현할 수 없는 독립 Source of Truth 책임이 확인될 경우에만 schema 확장을 다시 검토한다.

## Related Sources

- `docs/architecture/OVERVIEW.md`
- `docs/architecture/EPISODE_CUT_MODEL.md`
- `docs/architecture/CONTINUITY_MODEL.md`
- `docs/rules/MASTER_RULES.md`
- `docs/rules/SCRIPTURE_RULES.md`
- `docs/rules/VISUAL_RULES.md`
- `docs/rules/CONTINUITY_RULES.md`
- `docs/rules/GENERATION_RULES.md`
- `docs/rules/TEXT_AND_COPYRIGHT.md`
- `templates/storyboard.yaml`
