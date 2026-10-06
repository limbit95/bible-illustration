# Episode Orchestration Model v1.0

> 상태: **CONFIRMED / 2026-10-06**
>
> 목적: 사용자가 “한 장 전체를 알아서 만들어”, “이 Episode 전체를 자동으로 제작해”처럼 Multi-Cut 범위를 위임했을 때,
> 기존 Cut 단위 Architecture를 생략하지 않고 끝까지 순차 실행하는 Episode-level orchestration layer를 정의한다.

## 1. 왜 별도 Orchestration Layer가 필요한가

기존 Architecture는 다음을 잘 정의한다.

- Scripture Work Unit
- Episode / Storyboard / Cut
- Continuity
- Generation Run
- Result Review
- Asset Promotion
- Presentation text 원칙

그러나 위 요소를 **Episode 전체 범위에서 어떤 순서로 반복 호출하는가**는 충분히 고정되어 있지 않았다.

따라서 사용자가 전체 범위를 위임했을 때:

- Storyboard를 만들었지만 실제 생성에서 생략
- 20개의 Cut을 하나의 collage로 대체
- Continuity 관계를 순차 reference에 반영하지 않음
- Production Master와 Presentation output을 혼동

하는 잘못된 shortcut이 발생할 수 있었다.

Orchestration Layer는 이 문제를 해결한다.

## 2. Orchestration의 책임

Episode Orchestration은 “장면이 무엇인가”를 정의하지 않는다.

책임:

- 사용자 위임 범위를 해석한다.
- 전체 Episode의 planning completeness를 확인한다.
- Storyboard active Cut 순서를 실행 순서로 사용한다.
- 각 Cut의 Generation Gate를 호출한다.
- Cut별 생성·검토·재시도·진행 여부를 결정한다.
- Presentation Plan을 따라 Master와 presentation derivative를 분리한다.
- blocker가 없으면 다음 Cut으로 자동 진행한다.
- 전체 scope가 끝날 때까지 중간 단계를 collage / montage / batch shortcut으로 대체하지 않는다.

## 3. Source of Truth 관계

~~~text
User Episode-level Request
        ↓
Episode Production Session
        ↓
Episode / Storyboard / Presentation Plan
        ↓
Storyboard Preflight
        ↓
for each active Cut in Storyboard order:
    Cut + Continuity
        ↓
    Generation Entry Gate
        ↓
    Production Master Run
        ↓
    Result Review
        ↓
    accepted?
      ├─ no → same Cut retry / blocker
      └─ yes
           ↓
       Presentation Gate
           ↓
       presentation derivative if required
           ↓
       next active Cut
        ↓
Episode scope complete
~~~

Episode Production Session은 Storyboard order를 복제하지 않는다.
실행 순서는 항상 Storyboard가 Source of Truth다.

## 4. Episode Production Session

Multi-Cut autonomous request는 실행 전에 `production-session.yaml`을 만든다.

Session은 Canonical Scene이 아니라 **execution control record**다.

최소 책임:

- session_id
- episode_id
- purpose
- execution_mode
- source commit
- generation strategy
- approval / advance policy
- presentation plan reference
- current progress cursor
- blocker / completion status

Session이 Episode / Storyboard / Cut / Continuity 정의를 복제하지 않는다.

## 5. Execution Mode

v1:

~~~text
assisted
autonomous
~~~

### assisted

사용자 확인을 자주 받으며 다음 Cut으로 진행한다.

### autonomous

사용자가 Episode 전체 범위를 위임한 경우 사용한다.

원칙:

- 사용자가 Cut별 명령을 반복할 필요가 없다.
- blocker가 없으면 현재 Cut review 통과 후 다음 Cut으로 자동 진행한다.
- 사용자의 명시적 중단/수정 요청은 즉시 우선한다.
- Architecture Gate는 자동 모드에서도 생략하지 않는다.

## 6. Generation Strategy

Episode-level autonomous production의 기본값:

~~~text
sequential_per_cut
~~~

이는 다음을 뜻한다.

- 한 active Cut = 한 generation cycle
- 한 cycle에서 해당 Cut의 Production Master만 생성
- Review 통과 후 다음 Cut으로 이동
- 인접 Continuity가 필요하면 직전 accepted image를 올바른 reference lifecycle로 사용

다음은 금지한다.

- 여러 active Cut을 한 이미지 collage로 렌더링하여 각 Cut 생성을 대체
- 전체 Episode를 한 번의 Provider prompt로 batch 압축
- Storyboard panel/contact sheet를 Production Master로 간주
- 아직 Review되지 않은 Cut을 건너뛰고 후속 Cut을 확정

사용자가 collage/contact sheet 자체를 별도로 요청할 수는 있지만,
그 결과는 production Cut output이 아니다.

## 7. Advance Gate

다음 Cut으로 이동하려면 현재 Cut에서:

1. Generation Entry Gate 통과
2. Production Master Result 생성
3. 현재 Canonical 기준 Review 완료
4. Result가 accepted
5. 다음 Cut의 actual binary reference로 사용할 경우 필요한 reference lifecycle 처리
6. Presentation Plan상 필요한 output 처리 또는 명시적 deferred 상태 기록

이 충족되어야 한다.

rejected Result에서는 다음 Cut으로 이동하지 않는다.

## 8. Presentation Orchestration

Production Master와 presentation output은 별도 단계다.

각 Cut은 `presentation-plan.yaml`에서 다음 중 하나를 가진다.

~~~text
key_scripture
explanatory
visual_only
~~~

### key_scripture

- 승인된 직접 인용문이 필요하다.
- 기본 역본은 Text Rules에 따른다.
- quote / verse / translation이 준비되지 않으면 Presentation Gate를 통과하지 못한다.
- Production Master에 텍스트를 bake-in하지 않는다.

### explanatory

- 필요한 경우 자체 narration을 사용한다.
- 직접 인용과 혼동하지 않는다.

### visual_only

- presentation derivative를 만들지 않아도 된다.

## 9. Presentation Gate

presentation output이 필요한 Cut은 최소 다음을 확인한다.

- Production Master accepted
- presentation mode 확정
- key_scripture이면 정확한 quote / verse / translation 확정
- explanatory이면 narration 확정 또는 text-free 결정
- typography rule 확인
- Master image를 훼손하지 않는 derivative workflow 사용

Presentation Gate를 통과한 derivative만 최종 presentation output으로 본다.

## 10. Autonomous completion

autonomous session의 Episode scope complete는 다음으로 판정한다.

- Storyboard의 모든 active Cut이 generation cycle을 통과
- 각 Cut에 accepted Production Master Result가 존재
- required presentation output이 모두 complete 또는 명시적으로 deferred
- unresolved blocker 없음

이는 Canonical Episode Definition Approval과 별개다.

## 11. Blocker

다음은 자동 진행을 중단하는 blocker다.

- Scripture 범위 불명확
- Storyboard coverage/preflight 실패
- Cut / Continuity 정의 충돌
- Generation Entry Gate 실패
- 반복 retry 후 accepted Result 없음
- key_scripture presentation에 필요한 직접 인용문 미확정
- Provider 기능으로 필요한 operation 수행 불가
- 사용자 지시와 Canonical Rule이 충돌

blocker가 생기면 임의 shortcut으로 우회하지 않는다.

## 12. Automation Test

non-production automation test에서도 동일 Orchestration Model을 사용한다.

다만 DEC-0012가 허용한 test-only reference exception을 사용할 수 있다.

Test 결과를 평가할 때 최소 확인:

- 전체 scope가 Storyboard로 분해되었는가
- 모든 Cut이 개별 cycle로 실행되었는가
- relation에 따라 continuity/transition이 실제 생성에 반영되었는가
- Production Master는 text-free인가
- Presentation Plan의 Key Scripture / explanatory / visual-only 구분이 실제 output에 반영되었는가
- collage/batch shortcut이 없었는가

## 13. 불변 조건

1. Episode-level 위임은 Cut-level Architecture를 생략하지 않는다.
2. Storyboard order가 유일한 Cut 실행 순서 Source of Truth다.
3. autonomous는 “단계를 생략한다”가 아니라 “단계를 스스로 수행한다”는 뜻이다.
4. production Cut output은 one-Cut-at-a-time cycle로 생성한다.
5. collage/contact sheet는 Cut output을 대체하지 않는다.
6. rejected Cut을 건너뛰어 다음 Cut을 확정하지 않는다.
7. Production Master와 Presentation derivative를 구분한다.
8. Key Scripture presentation은 정확한 quote data 없이 완료하지 않는다.
9. blocker는 shortcut이 아니라 중단/수정 사유다.
10. Session은 Canonical Scene 정의를 복제하지 않는다.

**Episode Orchestration Model v1.0 — COMPLETED / CONFIRMED**
