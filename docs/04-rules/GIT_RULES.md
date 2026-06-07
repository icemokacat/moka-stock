# Git Branch Rules

> 팀원 모두가 혼란 없이 협업할 수 있도록 브랜치 규칙을 정리합니다.  
> 규칙을 바꾸고 싶을 때는 PR 올리기 전에 팀 채널에 먼저 공유해 주세요.

---

## 브랜치 구조

```
main
 └── develop
      ├── feature/기능명
      ├── fix/버그명
      ├── hotfix/긴급수정명
      ├── refactor/작업명
      ├── style/작업명
      ├── chore/작업명
      ├── setup/작업명
      └── docs/문서명
```

| 브랜치 | 역할 | 직접 푸시 |
|---|---|---|
| `main` | 프로덕션 배포본. 항상 배포 가능한 상태 유지 | ❌ 금지 |
| `develop` | 다음 릴리즈를 위한 통합 브랜치 | ❌ 금지 |
| `feature/*` | 새 기능 개발 | ✅ |
| `fix/*` | 일반 버그 수정 | ✅ |
| `hotfix/*` | 프로덕션 긴급 수정 (main에서 분기) | ✅ |
| `refactor/*` | 동작 변경 없는 코드 구조 개선 | ✅ |
| `style/*` | 코드 포맷팅, 주석 추가·수정 등 기능·로직 변경 없는 작업 | ✅ |
| `chore/*` | 기존 인프라·환경 **변경** (팀 전체 영향, 공유 필요) | ✅ |
| `setup/*` | 신규 설정·파일 **추가** (Docker, DB, 공통 유틸 등 초기 작업) | ✅ |
| `docs/*` | 설계 문서 추가 및 업데이트 (`docs/` 폴더 하위) | ✅ |

> **`refactor` vs `style`:** `refactor`는 코드 구조를 개선하는 것, `style`은 구조는 그대로 두고 포맷·주석만 손대는 것입니다. 로직이 조금이라도 바뀌면 `refactor`입니다.

> **`chore` vs `setup`:** `chore`는 기존 것을 **바꾸는** 작업으로 팀 전체에 영향을 줍니다. `setup`은 새 설정이나 파일을 **추가하는** 작업으로 기존 동작에 영향을 주지 않습니다.

> **브랜치 보호 설정:** `main`과 `develop`은 GitHub/GitLab의 Protected Branch로 설정되어 있어 직접 푸시가 원천 차단됩니다. 반드시 PR을 통해서만 머지하세요.

---

## 브랜치 네이밍 규칙

### 형식

```
<타입>/<이슈번호>-<짧은-설명>
```

### 예시

```
feature/123-소셜-로그인
feature/87-다크모드-토글
fix/210-결제-금액-오류
fix/198-모바일-메뉴-닫힘
hotfix/오더-api-타임아웃
refactor/auth-서비스-레이어-분리
refactor/결제-모듈-구조-개선
style/전체-코드-포맷팅
style/orderservice-주석-정리
chore/eslint-설정-업데이트
setup/docker-초기-설정
setup/db-연결-설정
setup/공통-유틸-클래스-추가
docs/domain-analysis-auth
docs/architecture-api-gateway
docs/technical-design-payment
docs/rules-git-branch
```

### 규칙

- **소문자와 하이픈(-) 만 사용** (언더스코어 `_`, 슬래시 중복 금지)
- **이슈 번호는 있으면 반드시 포함** (없으면 생략 가능)
- **설명은 무엇을 하는지 명확하게**, 최대 40자 이내로

### 잘못된 예시

```
❌ Feature/AddLogin                      → 대문자 사용
❌ feat/123-소셜-로그인                  → 브랜치 타입은 feature/, 커밋 타입(feat:)과 혼용 금지
❌ feature/fix                           → 타입과 설명이 같아서 불명확
❌ my-branch                             → 타입 없음
❌ feature/로그인페이지수정및버튼스타일변경  → 너무 김
```

---

## 브랜치 생명주기

### 문서 작업 (docs)

```
develop → docs/* → (PR) → develop
```

```bash
# 1. develop 최신화 후 브랜치 생성
git checkout develop
git pull origin develop
git checkout -b docs/domain-analysis-auth

# 2. docs/ 폴더 하위 문서 추가 또는 수정
#    docs/domain-analysis/
#    docs/architecture/
#    docs/technical-design/
#    docs/rules/

# 3. 작업 후 develop에 PR
# 4. 머지 완료 후 브랜치 삭제
```

> **왜 문서도 PR을 쓰나요?** 리뷰 없이 바로 머지되면 잘못된 설계 문서가 팀 기준이 되어버립니다. PR 히스토리가 곧 "왜 이렇게 설계했는가"의 기록이 됩니다.

### 일반 기능 개발

```
develop → feature/* → (PR) → develop
```

```bash
# 1. develop 최신화 후 브랜치 생성
git checkout develop
git pull origin develop
git checkout -b feature/123-소셜-로그인

# 2. 작업 후 develop에 PR
# 3. 머지 완료 후 브랜치 삭제
```

### 긴급 수정 (hotfix)

```
main → hotfix/* → (PR) → main + develop
```

```bash
# 1. main 기준으로 브랜치 생성
git checkout main
git pull origin main
git checkout -b hotfix/오더-api-타임아웃

# 2. 수정 후 main과 develop 모두 PR
# 3. 양쪽 모두 머지 확인 후 브랜치 삭제
```

> **주의:** hotfix는 `main`과 `develop` 양쪽에 머지해야 합니다. 하나라도 빠뜨리면 다음 배포 때 같은 버그가 재발합니다.  
> 브랜치 삭제는 **두 곳 모두 머지된 것을 확인한 뒤** 진행하세요.

---

## 커밋 메시지 규칙

### 형식

```
<타입>: <변경 내용 요약>

<선택: 상세 설명>
```

### 타입 목록

| 타입 | 용도 | 대응 브랜치 접두사 |
|---|---|---|
| `feat` | 새 기능 | `feature/` |
| `fix` | 버그 수정 | `fix/`, `hotfix/` |
| `refactor` | 동작 변경 없는 코드 구조 개선 | `refactor/`, `feature/` |
| `style` | 코드 포맷팅, 주석 추가·수정 (기능·로직 변경 없음) | `style/` |
| `test` | 테스트 추가/수정 | `feature/`, `fix/` |
| `chore` | 기존 인프라·환경 변경 | `chore/` |
| `setup` | 신규 설정·파일 추가 (초기 작업) | `setup/` |
| `docs` | 문서 변경 | `docs/` |
| `revert` | 커밋 되돌리기 | — |

> **`refactor` vs `style` 구분:** 변수명 변경, 함수 분리, 의존성 정리 → `refactor`. 들여쓰기, 줄바꿈, 주석 추가, 따옴표 통일 → `style`.

### 예시

```
feat: 카카오 소셜 로그인 추가

OAuth 2.0 기반으로 카카오 로그인 구현.
리다이렉트 URI는 환경변수로 관리.
```

```
fix: 모바일 네비게이션 메뉴 미닫힘 수정
refactor: 인증 서비스 레이어 분리
style: 전체 파일 Prettier 포맷팅 적용
style: OrderService 메서드 주석 추가
chore: GitHub Actions Node 18 → 20 업그레이드
setup: Docker Compose 초기 설정 추가
setup: DateUtils, StringUtils 공통 유틸 클래스 추가
```

### 규칙

- 제목은 **50자 이내**, 명사형으로 간결하게 작성 ("수정했음" ❌ → "수정" ✅)
- 제목 끝에 마침표 붙이지 않기
- 상세 설명이 필요하면 빈 줄 한 칸 띄우고 작성

---

## 자주 하는 실수와 해결법

### develop을 최신화하지 않고 브랜치를 팠을 때

```bash
git checkout feature/123-내-브랜치
git rebase origin/develop
```

### 이미 main에 직접 커밋해버렸을 때

팀 채널에 먼저 알리고 아래 방법으로 복구하세요.

```bash
# 방법 1: 커밋 되돌리기 (히스토리 유지 — 권장)
git revert <커밋해시>
git push origin main

# 방법 2: 강제 푸시 (히스토리 삭제 — 팀 전원 동의 후에만)
# ⚠️ Protected Branch가 설정되어 있으면 강제 푸시 자체가 막힐 수 있습니다.
#    이 경우 관리자 권한 해제 후 진행하고, 완료 후 즉시 재설정하세요.
git reset --hard <복구할커밋>
git push --force-with-lease origin main
```

### 머지 충돌이 났을 때

```bash
# develop 최신화 후 rebase 권장 (merge commit 줄이기)
git fetch origin
git rebase origin/develop

# 충돌 파일 수정 후 — 충돌 파일만 정확히 지정해서 스테이징
# git add . 는 빌드 결과물 등 불필요한 파일까지 올라갈 수 있으므로 지양
git add <충돌파일1> <충돌파일2>
git rebase --continue
```

### AI Git Commit Prompt

```
Format: [EMOJI] [TYPE]: [description in {language}]

Types & Emoji:
✨ feat      New feature
🐛 fix       Bug fix
🚑 hotfix    Critical production fix
♻️ refactor  Code restructuring without behavior change
🎨 style     Formatting, comments only (no logic change)
🧪 test      Add or update tests
🔧 chore     Modify existing infra or environment settings
🚀 setup     Add new config or files (initial setup)
📝 docs      Documentation changes
⏪ revert    Revert a commit

Rules:
- Subject under 50 chars, noun form (e.g. "Add" O / "Added" X)
- No period at the end of subject
- No scope, type only
- If context is needed, add body after a blank line

{diff}
```