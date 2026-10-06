# Genesis 1 Automation Test v2

> branch: `test/genesis1-automation-v2`
> mode: **NON-PRODUCTION AUTOMATION TEST**
> episode: `GEN-CREATION-AUTOTEST-01`
> image generation: sequential only
> Production Master: text-free

## Storyboard Preflight

- Scripture coverage: PASS — Genesis 1:1-31 전체 coverage
- Beat granularity: PASS — 20 Cut, 의미 있는 상태/초점 변화 기준
- Overcompression: PASS
- Duplicate micro-cut: PASS
- Scene relation: PASS — 19개 relation 정의
- Visual repetition: PASS — subject 전환 지점만 partial_reset
- Episode rhythm: PASS
- Text rule: PASS — 이미지 내 scripture/caption/verse/UI text 금지
- Provider: PASS — ChatGPT canonical-cut rev2

## Presentation intent

Key Scripture Frame 후보: C02, C04, C06, C08, C10, C13, C16, C17, C18, C20.
나머지는 Explanatory / Transitional 또는 text-minimal 후보다.
직접 인용 전문은 Production Master에 넣지 않는다.

## Test-only reference exception

DEC-0012의 isolated automation-test exception 사용.
same-session working image를 continuity reference로 사용할 수 있으나 Canonical Asset이 아니며,
test production data/image는 main 정식 production으로 merge하지 않는다.
