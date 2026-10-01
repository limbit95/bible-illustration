# DEC-0001 — Git LFS and Ignore Policy

> 상태: **proposed / REVIEW READY**
> 날짜: 2026-10-02
> 대체: none
> 대체됨: none

## Context

STEP 0-7에서 다음 원칙은 이미 CONFIRMED 상태다.

- 일반 Git은 Markdown / YAML / JSON 등 텍스트 Canonical data와 metadata를 관리한다.
- 장기 보존 대상으로 승격된 production/reference 이미지 binary는 Git LFS를 Canonical 저장 방식으로 사용한다.
- 모든 Generated Result binary를 영구 보존하지 않는다.
- 재생성 가능한 Derivative는 Canonical master가 아니다.
- Asset은 Cut 또는 Library의 primary owner 아래 등록한다.

Repository Structure v1.0은 루트에 \`.gitattributes\`와 \`.gitignore\`가 필요하다고 정의했지만 실제 추적 패턴과 제외 정책은 아직 확정하지 않았다.

이 결정을 통해 다음 문제를 해결해야 한다.

1. Provider에서 내려받은 모든 이미지가 실수로 Git에 쌓이는 문제
2. Canonical Asset binary가 일반 Git blob으로 들어가는 문제
3. Git LFS를 전체 이미지 확장자에 전역 적용해 문서용/임시 이미지까지 LFS가 되는 문제
4. Provider 원본 포맷을 변환하지 않고 보존해야 하는 Asset Promotion 원칙
5. 임시 파일·캐시·비밀값이 저장소에 들어가는 문제

## Decision

### 1. Git LFS 대상은 크기가 아니라 Canonical Asset 위치로 결정한다

Git LFS는 **Asset Promotion이 끝난 Canonical image binary**만 추적한다.

v1의 LFS 대상 경로는 다음 두 범위다.

~~~text
content/**/assets/
library/**/assets/
~~~

즉 이미지가 크다는 이유만으로 LFS에 넣지 않고, Canonical Asset으로 등록됐기 때문에 LFS에 넣는다.

### 2. v1 Canonical image 확장자

v1에서 Canonical Asset binary로 허용하고 LFS 추적하는 이미지 확장자는 다음과 같다.

~~~text
.png
.jpg
.jpeg
.webp
.avif
~~~

규칙:

1. 확장자는 lowercase를 사용한다.
2. Provider가 반환한 원본이 위 포맷 중 하나라면 Asset Promotion 시 가능하면 byte-for-byte 그대로 master로 보존한다.
3. 단순히 포맷을 통일하기 위해 PNG나 JPEG로 강제 변환하지 않는다.
4. 새로운 포맷이 실제로 필요해지면 먼저 정책과 \`.gitattributes\`를 검토한 뒤 추가한다.
5. PSD / TIFF / HEIC 등은 v1에서 자동 Canonical Asset 포맷으로 허용하지 않는다.
6. 편집 source file을 장기 보존해야 하는 실제 사례가 생기면 별도 검토 후 LFS 패턴을 확장한다.

### 3. 제안 \`.gitattributes\`

승인 후 루트 \`.gitattributes\`에 다음 패턴을 적용한다.

~~~gitattributes
# Canonical Cut-owned image Assets
content/**/assets/*.png  filter=lfs diff=lfs merge=lfs -text
content/**/assets/*.jpg  filter=lfs diff=lfs merge=lfs -text
content/**/assets/*.jpeg filter=lfs diff=lfs merge=lfs -text
content/**/assets/*.webp filter=lfs diff=lfs merge=lfs -text
content/**/assets/*.avif filter=lfs diff=lfs merge=lfs -text

# Canonical Library-owned image Assets
library/**/assets/*.png  filter=lfs diff=lfs merge=lfs -text
library/**/assets/*.jpg  filter=lfs diff=lfs merge=lfs -text
library/**/assets/*.jpeg filter=lfs diff=lfs merge=lfs -text
library/**/assets/*.webp filter=lfs diff=lfs merge=lfs -text
library/**/assets/*.avif filter=lfs diff=lfs merge=lfs -text
~~~

일반 \`*.png\` 전역 LFS 패턴은 사용하지 않는다.

이렇게 하면 LFS 여부가 파일 확장자만이 아니라 프로젝트의 Canonical Asset 경계와 일치한다.

### 4. 일반 이미지 binary는 기본적으로 Git에서 제외한다

Asset Promotion 전 Result, Provider 다운로드, 테스트 이미지, 비교용 임시 출력은 Canonical Asset이 아니다.

따라서 루트 \`.gitignore\`는 일반 이미지 binary를 기본 ignore하고, Canonical Asset 경로만 명시적으로 허용한다.

제안:

~~~gitignore
# Image binaries are non-canonical by default.
*.png
*.jpg
*.jpeg
*.webp
*.avif
*.psd
*.tif
*.tiff
*.heic

# Canonical Cut-owned Assets
!content/**/assets/*.png
!content/**/assets/*.jpg
!content/**/assets/*.jpeg
!content/**/assets/*.webp
!content/**/assets/*.avif

# Canonical Library-owned Assets
!library/**/assets/*.png
!library/**/assets/*.jpg
!library/**/assets/*.jpeg
!library/**/assets/*.webp
!library/**/assets/*.avif
~~~

이 정책의 목적은 이미지 파일을 금지하는 것이 아니라 **먼저 Asset Promotion을 거치도록 강제하는 안전장치**다.

문서용 이미지 등 새로운 합법적 저장 위치가 실제로 필요해지면 예외 경로를 명시적으로 추가한다.

### 5. Local working 영역은 Git에서 제외한다

Provider 다운로드와 임시 비교 파일은 저장소 루트의 다음 local-only 경로를 사용할 수 있다.

~~~text
.work/
.cache/
.tmp/
.downloads/
.exports/
~~~

이 디렉터리는 로컬 편의를 위한 영역이며 Canonical Repository Structure가 아니다.

빈 디렉터리를 저장소에 생성하지 않는다.

제안 \`.gitignore\`:

~~~gitignore
.work/
.cache/
.tmp/
.downloads/
.exports/
~~~

### 6. Secret / local environment는 Git에서 제외한다

Provider credential은 저장소에 들어가면 안 된다.

~~~gitignore
.env
.env.*
!.env.example
~~~

실제 secret 값이 없는 예제 환경 파일이 필요할 때만 \`.env.example\`을 허용한다.

### 7. OS / editor / log 파일은 Git에서 제외한다

~~~gitignore
.DS_Store
Thumbs.db
*.swp
*.swo
*~
*.log
~~~

### 8. Canonical metadata는 ignore하지 않는다

다음은 일반 Git에서 계속 추적한다.

- \`*.yaml\`
- \`*.yml\`
- \`*.md\`
- 필요한 \`*.json\`
- Asset metadata
- Generation Run metadata
- Result Review
- Asset selection
- External Reference Record

이미지 binary를 ignore하더라도 해당 이미지의 Canonical metadata까지 제외하지 않는다.

### 9. Derivative는 기본적으로 Repository에 저장하지 않는다

다음과 같은 재생성 가능한 전달본은 v1에서 Git/Git LFS의 기본 저장 대상이 아니다.

~~~text
web-1920.avif
web-1280.webp
thumb-480.webp
~~~

Derivative는 필요 시 build/export 과정에서 생성하고 delivery storage/CDN으로 전달한다.

향후 reproducibility 때문에 특정 Derivative를 장기 관리할 필요가 생기면 별도 정책을 추가한다.

### 10. Asset Promotion 흐름

권장 흐름:

~~~text
Provider Result / user supplied image
        ↓
Review
        ↓
장기 보존 가치 판단
        ↓
Asset ID 발급
        ↓
Canonical assets/ 경로로 원본 binary 이동
        ↓
asset metadata 작성
        ↓
Git LFS 추적 여부 확인
        ↓
commit
~~~

Promotion되지 않은 Result를 단순히 \`assets/\` 폴더에 넣어 LFS 저장하는 것은 금지한다.

### 11. Git LFS 확인 절차

Canonical Asset을 커밋하기 전에 가능한 경우 다음을 확인한다.

~~~text
git check-attr filter -- <asset-file>
git lfs ls-files
~~~

목적:

- 대상 binary가 실제 LFS filter를 사용하는지 확인
- 일반 Git blob으로 잘못 올라가는 사고 방지

실제 자동 검증 스크립트나 CI는 현재 단계에서 만들지 않는다.

### 12. 기존 binary migration

현재 단계에서는 기존 tracked image binary를 강제로 LFS migration하지 않는다.

Genesis Creation CUT 1–4 migration 시 실제 기존 이미지 파일의 위치와 상태를 확인하고:

- Canonical Asset으로 승격할 이미지인지
- 원본 binary가 확보되어 있는지
- 새 Asset ID가 무엇인지

를 결정한 뒤 새 구조에 등록한다.

과거 이미지라고 해서 자동으로 모든 binary를 Git LFS에 넣지 않는다.

## Alternatives Considered

### A. 모든 이미지 확장자를 전역 LFS 처리

~~~gitattributes
*.png filter=lfs ...
*.jpg filter=lfs ...
~~~

채택하지 않는다.

이유:

- 임시 Result와 Canonical Asset의 경계가 사라짐
- 향후 문서 이미지나 기타 binary도 자동 LFS 처리됨
- STEP 0-7의 Asset Promotion 모델과 맞지 않음

### B. 이미지 binary를 모두 일반 Git에 저장

채택하지 않는다.

이유:

- Repository history가 빠르게 비대해짐
- Canonical high-resolution image 보존 정책과 충돌
- 이미 CONFIRMED된 Git LFS 원칙과 충돌

### C. 모든 이미지 binary를 외부 Storage에만 저장

채택하지 않는다.

이유:

- STEP 0-7에서 GitHub repository + Git LFS를 Canonical Source로 확정함
- 외부 Storage/CDN은 delivery/cache/backup 역할임

### D. Provider별 다운로드 폴더를 Repository에 영구 보관

채택하지 않는다.

이유:

- Provider 구조가 Canonical Repository Structure에 침투함
- Result와 Asset의 구분이 약화됨
- 대량 불필요 binary 축적 가능

## Consequences

장점:

- Canonical Asset만 LFS 비용과 history를 사용한다.
- Provider 다운로드 실수 커밋 가능성을 낮춘다.
- Asset Promotion 절차가 저장 위치와 일치한다.
- Provider 원본 포맷을 불필요하게 변환하지 않는다.
- Cut-owned / Library-owned Asset이라는 기존 구조와 자연스럽게 연결된다.

제약:

- 문서용 이미지 등 새로운 binary 저장 영역이 필요하면 명시적 예외가 필요하다.
- PSD/TIFF 같은 source format을 장기 관리하려면 정책 확장이 필요하다.
- 사용자가 Asset Promotion 없이 이미지를 임의 위치에 저장하면 Git에서 무시되므로 반드시 metadata 등록 절차를 따라야 한다.
- Git LFS 사용 가능 여부와 실제 저장 용량/비용 관리는 별도 운영 이슈다.

## Related Sources

- \`docs/architecture/ASSET_STORAGE_POLICY.md\`
- \`docs/architecture/REPOSITORY_STRUCTURE.md\`
- \`docs/rules/GENERATION_RULES.md\`
- \`templates/asset-metadata.yaml\`
- \`docs/progress/CURRENT.md\`

## Approval Effect

이 Decision이 \`accepted\` 되면 다음을 수행한다.

1. 루트 \`.gitattributes\` 생성
2. 루트 \`.gitignore\` 생성
3. \`AGENTS.md\`에 Git LFS / ignore 운영 원칙 추가
4. \`CURRENT.md\`와 \`MILESTONES.md\`에 정책 확정 기록
5. Genesis Creation CUT 1–4 migration 단계로 이동
