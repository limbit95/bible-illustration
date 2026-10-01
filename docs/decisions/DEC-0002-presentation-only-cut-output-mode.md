# DEC-0002 — Presentation-only Cut Output Mode

> 상태: **proposed / REVIEW READY**
> 날짜: 2026-10-02
> 대체: none
> 대체됨: none

## Context

Genesis Creation의 기존 확정 작업에는 CUT 1 — 창세기 1:1이 존재한다.

이 Cut은 의도적으로 별도 일러스트를 만들지 않고 검은 화면과 presentation layer의 성경 본문 텍스트로 표현하도록 확정되어 있다.

현재 Architecture는 active Cut의 production complete를 다음과 같이 정의한다.

- Cut definition approved
- 현재 approved Cut revision에 대한 representative Asset 선택
- representative Asset binary available
- current Canonical 기준 Review 통과

따라서 현재 모델만 그대로 적용하면 **의도적으로 이미지 Asset이 없어야 하는 CUT 1**을 production complete로 만들 수 없다.

의미 없는 검은 PNG를 만들어 representative Asset으로 등록하는 것은 다음 원칙과 충돌한다.

- 최소 구현 원칙
- 이미지와 presentation text 분리
- Asset은 장기 보존 가치가 있는 실제 image identity라는 원칙

## Decision

### 1. Cut에 output mode를 둔다

Cut v1은 다음 두 output mode를 지원한다.

~~~text
illustration
presentation_only
~~~

기본값은 `illustration`이다.

### 2. Cut physical field

`cut.yaml`에 다음 필드를 추가한다.

~~~yaml
output:
  mode: illustration
~~~

presentation-only Cut은:

~~~yaml
output:
  mode: presentation_only
  presentation_instruction: >
    별도 일러스트 없이 검은 화면을 사용한다.
~~~

로 표현한다.

`presentation_instruction`은 화면의 비이미지 시각 연출을 설명하는 Canonical instruction이다.

성경 직접 인용문·장절·역본명·내레이션 자체는 이 필드에 중복 저장하지 않는다. 해당 텍스트는 presentation/site data layer에서 별도로 관리한다.

### 3. illustration mode

`output.mode: illustration`인 Cut은 기존 Asset Storage Policy를 그대로 따른다.

Production complete 조건:

1. Cut definition approved
2. current approved revision representative Asset 선택
3. representative Asset available
4. current Canonical snapshot Review 통과

### 4. presentation_only mode

`output.mode: presentation_only`인 Cut은 representative Asset을 요구하지 않는다.

Production complete 조건:

1. Cut definition approved
2. output.mode가 presentation_only로 명시됨
3. presentation_instruction이 장면을 구현할 만큼 명확함
4. Scripture / Rules 기준으로 presentation 방식이 검토됨
5. 해당 Cut에 representative Asset이 필요하지 않다는 상태가 의도적임

presentation_only Cut에 단순 검은 PNG나 placeholder image를 만들 필요는 없다.

### 5. Asset 관계

presentation_only Cut도 향후 실제 이미지가 필요해지면 Cut revision을 검토한 뒤 `illustration` mode로 변경할 수 있다.

approved 이후 output mode가 바뀌면 의미 있는 Canonical 변경이므로 Cut revision 증가와 재승인이 필요하다.

### 6. Storyboard / Continuity

presentation_only도 정상적인 Cut identity를 유지한다.

따라서:

- Storyboard 순서에 포함될 수 있다.
- Scripture Anchor / beat를 가진다.
- 인접 Cut과 Continuity transition을 가질 수 있다.
- Episode active Cut 구성에 포함될 수 있다.

다만 이미지 자체에서 상속할 physical visual state가 없으면 Continuity는 필요한 제약만 명시한다.

## Alternatives Considered

### A. 검은 PNG를 대표 Asset으로 생성

채택하지 않는다.

이유:

- presentation 연출을 불필요한 binary identity로 만든다.
- Asset Promotion 의미가 약해진다.
- 최소 구현 원칙과 맞지 않는다.

### B. CUT 1을 Cut이 아닌 별도 title card entity로 분리

v1에서는 채택하지 않는다.

이유:

- 새로운 전역 content entity가 추가되어 구조가 복잡해진다.
- 기존 Storyboard/Cut 흐름으로 충분히 표현할 수 있다.
- 현재 실제 요구는 “이미지가 없는 Cut” 하나를 정직하게 지원하는 것이다.

### C. CUT 1을 삭제하고 사이트에서 별도 처리

채택하지 않는다.

이유:

- 기존 확정 제작 흐름에서 CUT 1은 의도적인 첫 장면이다.
- Storyboard 역사와 Scripture Anchor가 사라진다.
- 저장소 Source of Truth 밖에 중요한 연출 결정이 남게 된다.

## Consequences

장점:

- 최소 구현 원칙을 Architecture가 실제로 지원한다.
- 불필요한 placeholder Asset 생성을 막는다.
- 검은 화면, 여백, 텍스트 중심 연출 같은 향후 장면도 동일 모델로 처리할 수 있다.
- Storyboard/Cut identity는 유지된다.

제약:

- Cut template과 Episode/Cut/Asset Architecture 문서에 작은 확장이 필요하다.
- production complete 도출 로직이 output mode에 따라 분기된다.
- presentation/site text data의 최종 schema는 별도 구현 단계에서 여전히 필요하다.

## Required Follow-up if Accepted

1. `docs/architecture/EPISODE_CUT_MODEL.md`에 Cut output mode 추가
2. `docs/architecture/ASSET_STORAGE_POLICY.md` production complete 예외 추가
3. `docs/rules/MASTER_RULES.md` 최소 구현과 presentation_only 연결
4. `templates/cut.yaml` output block 추가
5. Genesis Creation CUT 1 migration에 `presentation_only` 적용

## Related Sources

- `docs/architecture/EPISODE_CUT_MODEL.md`
- `docs/architecture/ASSET_STORAGE_POLICY.md`
- `docs/rules/MASTER_RULES.md`
- `docs/rules/SCRIPTURE_RULES.md`
- `docs/rules/TEXT_AND_COPYRIGHT.md`
- `templates/cut.yaml`
- `docs/progress/GENESIS_CREATION_MIGRATION.md`
