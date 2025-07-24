# GitHub 릴리스 가이드

이 가이드는 .env 파일을 사용하여 GitHub에 자동으로 릴리스하는 방법을 설명합니다.

## 사전 준비

1. `.env` 파일에 GitHub 토큰이 설정되어 있어야 합니다:
   ```
   GH_TOKEN=your_github_personal_access_token
   ```

2. 모든 변경사항이 커밋되어 있어야 합니다:
   ```cmd
   git add .
   git commit -m "your commit message"
   ```

## 릴리스 단계

### 1. 버전 업그레이드

패치 버전 업그레이드 (예: 1.0.0 → 1.0.1):
```cmd
npm version patch
```

마이너 버전 업그레이드 (예: 1.0.0 → 1.1.0):
```cmd
npm version minor
```

메이저 버전 업그레이드 (예: 1.0.0 → 2.0.0):
```cmd
npm version major
```

### 2. 태그 생성 및 푸시

버전 업그레이드 명령은 자동으로 Git 태그를 생성합니다. 
태그를 원격 저장소에 푸시:
```cmd
git push origin main --tags
```

### 3. GitHub 릴리스 및 빌드

#### Windows에서 릴리스 (PowerShell):
```powershell
# .env 파일 로드
Get-Content .env | ForEach-Object {
    if ($_ -match "^(.+)=(.+)$") {
        [System.Environment]::SetEnvironmentVariable($matches[1], $matches[2])
    }
}

# 빌드 및 릴리스
npm run dist -- --publish=always
```

#### Windows에서 릴리스 (Command Prompt):
```cmd
# .env 파일의 변수를 수동으로 설정
for /f "tokens=1,2 delims==" %%a in (.env) do set %%a=%%b

# 빌드 및 릴리스
npm run dist -- --publish=always
```

#### Mac/Linux에서 릴리스:
```bash
# .env 파일 로드 및 빌드
source .env && npm run dist -- --publish=always
```

## 전체 프로세스 예시 (Windows PowerShell)

```powershell
# 1. 변경사항 커밋
git add .
git commit -m "Add new feature"

# 2. 버전 업그레이드
npm version patch

# 3. 태그 푸시
git push origin main --tags

# 4. .env 로드 및 릴리스
Get-Content .env | ForEach-Object {
    if ($_ -match "^(.+)=(.+)$") {
        [System.Environment]::SetEnvironmentVariable($matches[1], $matches[2])
    }
}
npm run dist -- --publish=always
```

## 주의사항

- `.env` 파일은 절대 Git에 커밋하지 마세요
- GitHub Personal Access Token은 `repo` 권한이 필요합니다
- 릴리스가 완료되면 GitHub 저장소의 Releases 페이지에서 확인할 수 있습니다
- 자동 업데이트는 사용자가 앱을 실행할 때 자동으로 확인됩니다

## 문제 해결

### "Git working directory not clean" 오류
```cmd
git status
git add .
git commit -m "your message"
```

### 토큰 권한 오류
GitHub에서 Personal Access Token의 `repo` 권한을 확인하세요.

### 빌드 실패
```cmd
npm run clean
npm install
```