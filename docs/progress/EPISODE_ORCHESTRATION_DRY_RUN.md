# Episode Orchestration Synthetic Dry Run — 2026-10-06

> 상태: **PASS**
>
> 목적: “이 Episode 전체를 알아서 만들어”라는 high-level autonomous request가
> Cut-level Architecture를 생략하지 않고 끝까지 순차 실행되는지 검증한다.
>
> 실제 Provider image request는 수행하지 않는다.

## 1. Synthetic request

~~~text
“이 3-Cut Scripture Work Unit 전체를
중간 명령 없이 알아서 만들어”
~~~

해석:

- scope: multi-cut episode
- execution_mode: autonomous
- generation_strategy: sequential_per_cut
- collage substitute: forbidden
- presentation: enabled

## 2. Planning completeness

첫 Provider call 전에 다음이 존재한다고 가정한다.

- Episode
- Storyboard 3 entries
- Storyboard Preflight PASS
- Cut C01 / C02 / C03 definitions
- C01→C02 / C02→C03 Continuity
- Production Session
- Presentation Plan
- Provider Integration

**결과: PASS**

## 3. Production Session

개념:

~~~yaml
session_id: SYNTH-SESSION-001
episode_id: SYNTH-EPISODE
purpose: automation_test
execution_mode: autonomous

policy:
  generation_strategy: sequential_per_cut
  allow_batch_generation: false
  allow_collage_as_cut_substitute: false
  require_storyboard_preflight: true
  require_generation_entry_gate: true
  require_result_review_before_advance: true
  require_presentation_gate: true
  user_confirmation_between_cuts: false
~~~

Storyboard 순서를 Session에 복제하지 않는다.

**결과: PASS**

## 4. Presentation Plan

~~~yaml
presentation:
  - cut_id: SYNTH-C01
    mode: visual_only

  - cut_id: SYNTH-C02
    mode: key_scripture
    quote:
      translation: KRV
      text: <verified exact quote>

  - cut_id: SYNTH-C03
    mode: explanatory
    narration: <project narration>
~~~

확인:

- Master text-free 유지
- Key Scripture와 narration 분리
- C02는 exact quote 없이는 Presentation Gate 통과 불가

**결과: PASS**

## 5. C01 cycle

~~~text
resolve C01
→ entry_gate
   orchestration_ready = true
   presentation_plan_ready = true
→ Production Master Run
→ Result Review = accepted
→ Presentation mode = visual_only
→ C01 complete
→ advance C02
~~~

**결과: PASS**

## 6. C02 continuity + key scripture cycle

C01→C02가 Continuity-oriented라고 가정한다.

~~~text
resolve C02
→ previous accepted C01 reference lifecycle 확인
→ retain / change / reset 반영
→ entry_gate PASS
→ C02 Production Master 생성
→ Review accepted
→ Presentation Gate
   mode = key_scripture
   exact quote / verse / translation 확인
→ presentation derivative
→ review
→ advance C03
~~~

C02 key scripture text가 준비되지 않았다면 C03로 advance하지 않고 blocked가 된다.

**결과: PASS**

## 7. C03 transition + explanatory cycle

C02→C03가 Transition-oriented라고 가정한다.

- world/story state는 필요한 만큼 유지
- reset 허용 camera/composition/dominant subject는 재설계
- continuity를 world reset으로 해석하지 않음

~~~text
C03 Production Master
→ Review accepted
→ explanatory narration 확인
→ presentation derivative
→ Episode scope complete
~~~

**결과: PASS**

## 8. Shortcut rejection

### 3 Cut을 한 collage로 생성

판정: **REJECT**

이유:

- one-Cut-per-cycle invariant 위반
- C01 review 전에 C02/C03 결과를 확정
- Continuity reference lifecycle 실행 불가
- individual Production Master 부재

### 한 Provider request에서 3개의 서로 다른 Cut image 생성

판정: **REJECT**

여러 Result가 같은 Cut 변형인 것은 Run Model상 가능하지만,
서로 다른 active Cut을 한 Run으로 묶는 것은 target=one Cut invariant 위반이다.

### Storyboard contact sheet

판정: **허용 가능하지만 production output 아님**

planning/review용 derivative일 뿐 Cut Production Master를 대체하지 않는다.

## 9. Autonomous behavior

사용자에게 C01 뒤 “다음?”을 묻지 않는다.

- accepted + no blocker → 자동 C02
- accepted + no blocker → 자동 C03
- blocker → pause / report
- user interrupt → pause / canonical impact 처리

**결과: PASS**

## 10. Dry-run 결론

**PASS**

Episode-level autonomous request가:

~~~text
high-level request
→ planning
→ Session
→ per-Cut sequential cycles
→ Review
→ Presentation Gate
→ next Cut
→ scope complete
~~~

로 동작하며 기존 Cut-level Architecture를 우회하지 않는다.
