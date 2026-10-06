# Orchestration Rules v1.0

> 상태: **CONFIRMED / 2026-10-06**
>
> 목적: Multi-Cut / Episode-level 위임을 실제 실행할 때 Architecture를 빠뜨리지 않도록 운영 규칙을 강제한다.

## 1. Full-scope request detection

다음과 같은 요청은 Episode-level orchestration 대상으로 본다.

- “창세기 1장 전체 만들어”
- “이 본문 전체를 알아서 제작해”
- “내가 중간 명령하지 않아도 끝까지 진행해”
- 여러 Cut이 필요한 Scripture Work Unit 전체 제작

사용자가 “Storyboard부터”라고 따로 말할 필요가 없다.

## 2. Autonomous means self-orchestrated, not batched

autonomous mode에서는 사용자가 Cut마다 “다음”을 말하지 않아도 된다.

하지만 다음을 금지한다.

- 여러 Cut을 한 Provider image request에 압축
- 1장의 collage로 전체 Cut을 대체
- Storyboard를 만든 뒤 실제 생성에서 무시
- continuity reference를 생략하고 독립 이미지를 연속 생성
- Presentation 계획을 무시하고 임의 caption을 이미지에 넣음

## 3. Planning-before-render rule

Multi-Cut autonomous request에서 첫 image tool call 전에 다음이 모두 존재해야 한다.

- Episode
- Storyboard
- Storyboard Preflight result
- active Cut Canonical definitions
- Continuity
- Presentation Plan
- Production Session
- Provider Integration resolution

이 중 하나라도 없으면 첫 image tool call 금지.

## 4. One Cut = One Production Cycle

각 active Cut은 최소 다음 cycle을 가진다.

~~~text
resolve Cut
→ Generation Entry Gate
→ Production Master image
→ Review
→ retry or accept
→ Presentation Gate
→ next Cut
~~~

사용자가 전체 범위를 위임했더라도 이 cycle을 반복한다.

## 5. Continuity execution

Continuity-oriented relation에서는:

- 직전 accepted image를 continuity reference 후보로 우선 사용
- retain / change / reset을 실제 prompt/reference 구성에 반영
- 우연한 detail을 Canonical fact로 승격하지 않음

Transition-oriented relation에서는:

- 필요한 world/story state는 유지
- reset이 허용된 camera/composition/dominant subject만 새로 설계
- “transition”을 world reset으로 오해하지 않음

## 6. Presentation execution

Production Master는 항상 text-free다.

Presentation Plan에 따라:

- key_scripture → 정확한 직접 인용 + 장절 + 역본
- explanatory → narration 또는 text-free
- visual_only → Master만 사용 가능

요약문을 Key Scripture 대신 성경 말씀처럼 넣지 않는다.

## 7. No hidden compression

자동화 편의를 위해 Cut 수를 줄이지 않는다.

본문이 자세하고 시각적 단계가 풍부하면 Cut을 충분히 나눈다.

단순 반복만을 위한 micro-cut은 만들지 않는다.

## 8. Retry discipline

현재 Cut Result가 rejected면:

- 원인을 기록
- 같은 Cut에서 새 Run
- accepted 전에는 다음 Cut로 advance하지 않음

예외적으로 blocker로 중단할 수 있다.

## 9. User interruption

autonomous session 중 사용자가:

- 특정 Cut 수정
- Storyboard 변경
- 생성 중단
- 스타일 변경

을 지시하면 현재 session을 해당 지점에서 pause하고 Canonical 영향부터 반영한다.

사용자의 개입이 없으면 blocker가 없는 한 계속 진행한다.

## 10. Automation test rule

test branch에서도:

- planning-before-render
- one-Cut-per-cycle
- continuity execution
- Presentation Gate

를 그대로 적용한다.

test-only exception은 reference asset lifecycle에만 제한적으로 적용한다.
collage/batch shortcut 예외는 없다.

## 11. Completion report

autonomous scope가 끝나면 최소 다음을 보고한다.

- 총 active Cut 수
- accepted Production Master 수
- presentation output 수
- retry / rejected Run 수
- blocker/deferred 항목
- continuity / transition 주요 구간
- test이면 production으로 승격되지 않았음을 명시
