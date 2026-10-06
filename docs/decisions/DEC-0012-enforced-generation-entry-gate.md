# DEC-0012 — Enforce Generation Entry Gate before every Provider image request

> 상태: accepted
> 날짜: 2026-10-06
> 관련: DEC-0005, DEC-0006, DEC-0009

## Context

자동화 테스트에서 Storyboard / Continuity / Text Rules가 문서로 존재했음에도 실제 이미지 생성 호출이 먼저 실행되어 다음 문제가 발생했다.

- Storyboard 없이 임의 Cut 수로 압축
- continuity 관계를 실제 생성에 반영하지 못함
- Production Master에 본문·장절·caption을 bake-in
- batch generation으로 높은 continuity risk를 우회

문서상 권고만으로는 자동 실행 시 Architecture를 건너뛰는 것을 충분히 막지 못했다.

## Decision

모든 실제 Provider Generation Run은 제출 전에 강제 Generation Entry Gate를 통과한다.

Run schema에 `entry_gate` snapshot을 추가한다.

적용 항목에서 false / unresolved가 하나라도 있으면 Provider request를 제출하지 않는다.

필수 확인 범위:

- Scripture Work Unit
- Episode
- Storyboard
- Storyboard Preflight
- Cut Canonical Scene
- Scene Relation
- Continuity
- text-free Production Master
- Provider Integration

사용자가 “알아서 전체 만들어줘”라고 위임한 경우 Assistant가 위 단계를 자동 수행할 수 있지만 생략할 수 없다.

## Experience-rich decomposition

Cut 수 최소화는 목표가 아니다.

본문에 서로 다른 의미 있는 visual beat가 충분히 존재하면, 시청자 경험을 풍부하게 하기 위해 적극적으로 여러 Cut으로 분해할 수 있다.

단, 사실상 같은 의미와 화면을 반복하는 micro-cut은 피한다.

## Automation test exception

별도 non-production test branch에서는 same-session working image를 다음 Cut의 visual continuity reference로 임시 사용할 수 있다.

이 예외는 자동화/연결성 검증을 위한 것이며:

- Canonical Asset으로 승격하지 않는다.
- test production data/image를 main의 정식 production으로 merge하지 않는다.
- 실제 production에서는 DEC-0009에 따라 downstream reference 전 Asset Promotion을 수행한다.

## Consequences

- 이미지 생성 tool call 전에 Architecture traversal이 강제된다.
- Production Master의 text bake-in 실수를 조기에 차단한다.
- 자동화 요청에서도 Storyboard/Continuity가 생략되지 않는다.
- 고연결성 Episode를 batch generation으로 무리하게 처리하기 어렵게 만든다.
