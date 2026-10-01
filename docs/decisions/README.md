# Decision Records

> 상태: **CONFIRMED / 2026-10-02 사용자 승인**
>
> 목적: 장기적으로 다시 논의될 가능성이 높고, 선택 이유를 잃으면 재작업 비용이 큰 결정을 기록한다.

## 1. Decision Record가 필요한 경우

다음 중 하나 이상에 해당하면 Decision Record를 만든다.

- 현실적인 대안이 둘 이상 있었다.
- 나중에 “왜 이 방식인가”를 다시 설명할 가능성이 높다.
- 변경 비용이 크다.
- Architecture / Rules / Content / Library / Provider 등 여러 영역에 영향을 준다.
- 외부 서비스나 저장 방식처럼 장기 운영에 영향을 준다.
- 기존 확정 기준을 바꾸거나 예외를 만든다.

다음은 일반적으로 별도 Decision Record가 필요하지 않다.

- 단순 오탈자 수정
- 한 Cut에만 해당하는 일상적인 장면 선택
- Generation Run 한 번의 성공/실패
- 단순 작업 완료 기록
- 이미 Architecture / Rules가 충분히 설명하는 세부 사항을 이유 없이 중복 기록하는 경우

## 2. 파일명

형식:

~~~text
DEC-<NNNN>-<slug>.md
~~~

예:

~~~text
DEC-0001-use-git-lfs-for-canonical-assets.md
DEC-0002-change-default-public-scripture-translation.md
~~~

규칙:

1. 번호는 저장소 전역에서 단조 증가한다.
2. 삭제된 번호를 재사용하지 않는다.
3. slug는 사람이 의미를 알아볼 수 있는 짧은 kebab-case를 사용한다.
4. 파일명 변경으로 과거 참조를 불필요하게 깨지 않는다.

## 3. 상태

Decision Record 상태:

~~~text
proposed
accepted
superseded
rejected
~~~

- `proposed`: 검토 중이며 아직 운영 기준이 아님
- `accepted`: 현재 효력이 있는 결정
- `superseded`: 더 새로운 Decision이 이 결정을 대체함
- `rejected`: 검토했지만 채택하지 않음

Decision이 superseded 되면 기존 문서를 삭제하거나 내용을 새 결정으로 덮어쓰지 않는다.

## 4. 기본 문서 형식

새 Decision은 다음 구조를 사용한다.

~~~markdown
# DEC-0001 — <Title>

> 상태: proposed
> 날짜: YYYY-MM-DD
> 대체: none
> 대체됨: none

## Context

왜 이 결정이 필요한지 설명한다.

## Decision

무엇을 선택했는지 명확히 기록한다.

## Alternatives Considered

현실적으로 검토한 대안과 채택하지 않은 이유를 기록한다.

## Consequences

이 선택으로 생기는 장점, 제약, 운영 영향, 후속 작업을 기록한다.

## Related Sources

관련 Architecture / Rules / Content / Issue / Commit 등을 연결한다.
~~~

필요 없는 섹션을 억지로 길게 채우지 않는다.

## 5. Source of Truth 관계

Decision Record는 Architecture나 Rules를 대신하지 않는다.

~~~text
Decision
= 왜 이 선택을 했는가

Architecture / Rules
= 현재 무엇이 기준인가
~~~

accepted Decision의 결과가 Architecture / Rules 변경을 요구한다면 해당 정식 문서도 함께 갱신한다.

Decision만 바꾸고 Architecture / Rules를 오래된 상태로 방치하지 않는다.

현재 운영 시 실제 규칙 판단은 정식 Architecture / Rules가 우선한다.

## 6. 소급 기록 원칙

STEP 0의 모든 세부 결정을 Decision 파일로 소급 생성하지 않는다.

이미 정식 Architecture와 Rules가 충분한 Source of Truth이기 때문이다.

다만 이후 다음 조건이 생기면 과거 결정도 별도 Decision으로 기록할 수 있다.

- 반복해서 이유를 다시 확인하게 됨
- 외부 Provider / Storage migration에 직접 영향
- 여러 Architecture 영역에 걸친 결정으로 독립 설명 가치가 생김

## 7. 번호 발급

새 Decision을 만들기 전에 `docs/decisions/`의 기존 `DEC-*.md`를 확인하고 가장 큰 번호 다음 번호를 사용한다.

번호를 기억이나 채팅 내용만으로 추측하지 않는다.

## 8. CURRENT / MILESTONES와의 관계

- `docs/progress/CURRENT.md`: 지금 무엇을 하고 있는가
- `docs/progress/MILESTONES.md`: 무엇을 완료했는가
- `docs/decisions/DEC-*.md`: 왜 중요한 선택을 했는가

같은 정보를 세 문서에 장문으로 중복 복사하지 않는다.

CURRENT에는 현재 작업에 직접 필요한 Decision ID만 링크한다.
