# AI 자동화 실전 캠프 - 사전 준비 가이드

본 문서는 캠프 참여 전 필요한 프로그램 설치 방법을 안내합니다.

---

## 1. Claude Max 플랜 구독 (필수)

캠프에서 **병렬 에이전트 활용법**을 실습하려면 반드시 **Claude Max 플랜**이 필요합니다.

### 플랜 비교

| 플랜 | 월 요금 | 5시간당 Claude Code 프롬프트 | 실습 가능 여부 |
|------|---------|----------------------------|--------------|
| Pro | $20 | 10~40개 | 병렬 에이전트 실습 불가 |
| **Max 5x (권장)** | **$100** | **50~200개** | **실습 가능** |
| Max 20x | $200 | 200~800개 | 실습 가능 (여유로움) |

> **중요**: Pro 플랜($20)으로는 5분 만에 5시간 토큰 한도가 바닥나서 실습이 중단됩니다.
> 반드시 **Max 플랜($100 이상)**으로 업그레이드해주세요.

### 구독 방법

1. https://claude.ai 접속
2. 로그인 (이메일 또는 Google 계정)
3. 좌측 하단 이름/아이콘 클릭 → **Settings**
4. **Billing** 탭 → **Upgrade plan** 클릭
5. **Max** 플랜 선택 후 결제 정보 입력

---

# Mac 사용자 가이드

## 2. 터미널 열기

Mac에서 터미널을 여는 방법:

**방법 1: Spotlight 검색 (가장 빠름)**
1. `Cmd + Space` 키를 동시에 누르기
2. "터미널" 또는 "Terminal" 입력
3. Enter 키 누르기

**방법 2: Finder에서 찾기**
1. Finder 열기
2. 응용 프로그램 → 유틸리티 → 터미널

---

## 3. Node.js 설치 (Mac)

Claude Code 설치 전에 Node.js가 필요합니다.

### 방법 1: 공식 설치 파일 (초보자 추천)

1. https://nodejs.org 접속
2. **LTS** 버전 (v24.x) 다운로드 버튼 클릭
3. 다운로드된 `.pkg` 파일 더블클릭
4. 설치 마법사 안내에 따라 **Continue** → **Install** → **Close**

### 방법 2: Homebrew (개발자 추천)

터미널에서 다음 명령어 실행:

```bash
# Homebrew 설치 (아직 없다면)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Node.js 설치
brew install node
```

### 설치 확인

```bash
node --version
npm --version
```

버전 번호가 출력되면 성공입니다.

---

## 4. Claude Code 설치 (Mac)

### 권장 방법: 네이티브 설치

터미널에서 다음 명령어를 복사하여 붙여넣기 후 Enter:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### 대체 방법: Homebrew

```bash
brew install --cask claude-code
```

### 설치 확인

```bash
claude --version
```

### 첫 실행

```bash
claude
```

브라우저가 열리면 Claude 계정으로 로그인합니다.

---

## 5. Antigravity 설치 (Mac)

Antigravity는 Google DeepMind가 만든 AI 코딩 에디터입니다 (VS Code 기반).

### 다운로드 및 설치

1. https://antigravity.google/download 접속
2. **macOS** 버전 다운로드 (Apple Silicon 또는 Intel 선택)
3. 다운로드된 `.dmg` 파일 더블클릭
4. Antigravity 아이콘을 **Applications** 폴더로 드래그
5. 응용 프로그램에서 Antigravity 실행
6. Google 계정(Gmail)으로 로그인

### Antigravity 내부 터미널 열기

| 방법 | 단축키/설명 |
|------|------------|
| 단축키 | `` Ctrl + ` `` (백틱 키, 숫자 1 왼쪽) |
| 메뉴 | 상단 **Terminal** → **New Terminal** |
| 명령 팔레트 | `Cmd + Shift + P` → "Terminal: Create New Terminal" 검색 |

---

## 6. 문제 해결 (Mac)

### "개발자를 확인할 수 없습니다" 경고

1. 시스템 환경설정 → 보안 및 개인 정보 보호 → 일반
2. "확인 없이 열기" 클릭

### "node: command not found" 오류

터미널을 완전히 닫고 새로 열어보세요. 그래도 안 되면:

```bash
# zsh 사용자 (최신 Mac)
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# bash 사용자
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bash_profile
source ~/.bash_profile
```

---

# Windows 사용자 가이드

## 2. PowerShell 열기

Windows에서 PowerShell을 여는 방법:

**방법 1: 검색으로 열기 (가장 쉬움)**
1. 키보드에서 `Windows 키` 누르기 (또는 시작 버튼 클릭)
2. "PowerShell" 입력
3. **Windows PowerShell** 클릭 (파란색 아이콘)

**방법 2: 시작 메뉴에서 열기**
1. 시작 버튼 **우클릭**
2. **Windows PowerShell** 또는 **터미널** 선택

> **팁**: "명령 프롬프트(cmd)"보다 "PowerShell"을 사용하세요.

---

## 3. Node.js 설치 (Windows)

### 설치 단계

1. https://nodejs.org 접속
2. **LTS** 버전 (v24.x) 다운로드 버튼 클릭
3. 다운로드된 `.msi` 파일 더블클릭
4. 설치 마법사 진행:

```
[Next] 클릭
↓
"I accept the terms..." 체크 → [Next]
↓
설치 경로 기본값 유지 → [Next]
↓
Custom Setup에서 모든 항목 체크 확인 (특히 "Add to PATH") → [Next]
↓
"Automatically install the necessary tools" 체크 → [Next]
↓
[Install] 클릭
↓
[Finish] 클릭
```

### 설치 확인

PowerShell을 **새로 열고** 다음 명령어 실행:

```powershell
node --version
npm --version
```

버전 번호가 출력되면 성공입니다.

---

## 4. Windows 권한 오류 해결

Windows에서 설치 시 다음과 같은 오류가 발생할 수 있습니다:

```
이 시스템에서 스크립트를 실행할 수 없으므로...
cannot be loaded because running scripts is disabled on this system
```

### 해결 방법 (관리자 권한 PowerShell 필요)

#### Step 1: 관리자 권한으로 PowerShell 열기

1. `Windows 키` 누르기
2. "PowerShell" 검색
3. **Windows PowerShell** 위에서 **마우스 우클릭**
4. **"관리자 권한으로 실행"** 클릭
5. "이 앱이 디바이스를 변경하도록 허용하시겠어요?" → **예** 클릭

> **확인**: 창 제목에 **[관리자]** 또는 **Administrator**가 보이면 성공

#### Step 2: 권한 설정 명령어 실행

**현재 사용자에게만 적용 (권장)**:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

**컴퓨터 전체에 영구 적용 (매번 설정하기 귀찮을 때)**:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine
```

실행 후 `Y` 입력하고 Enter

### 옵션 설명

| 옵션 | 설명 |
|------|------|
| `RemoteSigned` | 로컬 스크립트 허용, 인터넷 다운로드 스크립트는 서명 필요 **(권장)** |
| `AllSigned` | 모든 스크립트에 서명 필요 (더 안전하지만 불편) |
| `CurrentUser` | 현재 사용자에게만 적용 |
| `LocalMachine` | 모든 사용자에게 영구 적용 |

---

## 5. Claude Code 설치 (Windows)

### 권장 방법: 네이티브 설치

**관리자 권한 PowerShell**에서 다음 명령어 실행:

```powershell
irm https://claude.ai/install.ps1 | iex
```

### 대체 방법: WinGet

```powershell
winget install Anthropic.ClaudeCode
```

### 설치 확인

PowerShell을 **새로 열고**:

```powershell
claude --version
```

### 첫 실행

```powershell
claude
```

브라우저가 열리면 Claude 계정으로 로그인합니다.

### "claude 명령을 찾을 수 없음" 오류 시

1. PowerShell을 완전히 닫고 새로 열기
2. 그래도 안 되면 컴퓨터 재시작
3. 여전히 안 되면 PATH 수동 추가:
   - `Win + R` → `sysdm.cpl` 입력 → Enter
   - **고급** 탭 → **환경 변수**
   - **Path** 선택 → **편집** → **새로 만들기**
   - `%USERPROFILE%\.local\bin` 입력 → **확인**

---

## 6. Antigravity 설치 (Windows)

### 다운로드 및 설치

1. https://antigravity.google/download 접속
2. **Windows** 버전 다운로드 (대부분 x64)
3. 다운로드된 `.exe` 파일 더블클릭
4. Windows Defender SmartScreen 경고 시:
   - **"자세한 정보"** 클릭
   - **"실행"** 클릭
5. 설치 마법사 따라 진행
6. Google 계정(Gmail)으로 로그인

### Antigravity 내부 터미널 열기

| 방법 | 단축키/설명 |
|------|------------|
| 단축키 | `` Ctrl + ` `` (백틱 키, 숫자 1 왼쪽) |
| 메뉴 | 상단 **Terminal** → **New Terminal** |
| 명령 팔레트 | `Ctrl + Shift + P` → "Terminal: Create New Terminal" 검색 |

### Antigravity 터미널에서 권한 오류 발생 시

Antigravity 내부 터미널에서도 스크립트 실행 오류가 발생할 수 있습니다.

**해결 방법 1: Antigravity 터미널에서 직접 실행**
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

**해결 방법 2: 영구 설정 (앞서 설명한 LocalMachine 설정)**

이미 관리자 권한 PowerShell에서 다음을 실행했다면 Antigravity에서도 자동 적용됩니다:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine
```

---

## 7. 문제 해결 FAQ

### Q: "npm을 찾을 수 없습니다" 오류가 뜹니다

**A**: Node.js가 설치되지 않았습니다. 위의 Node.js 설치 섹션을 따라 설치 후 터미널을 새로 열어주세요.

### Q: 설치는 됐는데 `claude` 명령어가 안 됩니다

**A**: 터미널을 완전히 닫고 새로 열어보세요. 그래도 안 되면 컴퓨터를 재시작해주세요.

### Q: PowerShell 정책 변경이 안 됩니다

**A**:
- 관리자 권한으로 실행했는지 확인하세요 (창 제목에 [관리자] 표시)
- 회사 컴퓨터의 경우 IT 부서의 정책으로 제한되어 있을 수 있습니다. IT 부서에 문의하세요.

### Q: 회사 컴퓨터에서 그룹 정책으로 막혀 있습니다

**A**: IT 부서에 "PowerShell 스크립트 실행 권한이 필요합니다"라고 요청하세요. 그룹 정책(MachinePolicy)으로 설정된 경우 사용자가 우회할 수 없습니다.

### Q: Antigravity 로그인이 안 됩니다

**A**: Gmail 계정이 필요합니다. 회사 이메일이 아닌 개인 Gmail로 시도해보세요.

---

## 설치 완료 체크리스트

### 공통
- [ ] Claude Max 플랜 구독 완료 ($100 이상)

### Mac 사용자
- [ ] Node.js 설치 완료 (`node --version` 확인)
- [ ] Claude Code 설치 완료 (`claude --version` 확인)
- [ ] Antigravity 설치 완료

### Windows 사용자
- [ ] Node.js 설치 완료 (`node --version` 확인)
- [ ] PowerShell 스크립트 실행 권한 설정 완료
- [ ] Claude Code 설치 완료 (`claude --version` 확인)
- [ ] Antigravity 설치 완료

---

## 참고 링크

| 항목 | URL |
|------|-----|
| Claude 구독 | https://claude.ai |
| Node.js 다운로드 | https://nodejs.org |
| Claude Code 문서 | https://code.claude.com/docs/ko/setup |
| Antigravity 다운로드 | https://antigravity.google/download |
