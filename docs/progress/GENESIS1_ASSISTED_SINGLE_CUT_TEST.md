# Genesis 1 Assisted Single-Cut Test

> branch: `test/genesis1-assisted-single-cut`
> episode: `GEN-CREATION-ASSISTED-TEST-01`
> mode: **NON-PRODUCTION / ASSISTED**
> status: **READY FOR FIRST USER PROMPT**
> image generated: **NO**

## 목적

전체 자동화 테스트와 분리하여 사용자가 한 장씩 원하는 장면을 지시할 때
현재 Architecture가 Cut 단위로 정확히 작동하는지 검증한다.

## 현재 준비 상태

- Genesis 1:1–31 Episode scope: 준비 완료
- Production Session: `assisted`
- generation strategy: `sequential_per_cut`
- batch generation: 금지
- collage substitute: 금지
- user confirmation between cuts: 필수
- Storyboard: 첫 프롬프트 대기
- Cut: 아직 없음
- Continuity: 첫 Cut 이전이므로 없음
- Presentation Plan: 첫 프롬프트 대기
- Generation Run: 아직 없음
- Image: 아직 생성하지 않음

## 첫 프롬프트 수신 후 절차

1. 사용자의 장면 요구를 Scripture Anchor와 visual beat로 정식화
2. Storyboard에 정확히 1개 Cut entry 추가
3. Cut Canonical Scene 작성
4. 첫 Cut이면 Continuity relation은 not_applicable
5. presentation mode를 key_scripture / explanatory / visual_only 중 하나로 확정
6. key_scripture이면 정확한 개역한글 quote / verse / translation 준비
7. Generation Run `entry_gate` 확인
8. text-free Production Master 1장만 생성
9. 결과 Review
10. 필요한 Presentation derivative 처리
11. 사용자 확인 전 다음 Cut 자동 생성 금지

## 현재 blocker

`awaiting_first_user_prompt`

사용자 프롬프트가 오기 전에는 Cut 내용이나 이미지를 임의로 생성하지 않는다.
