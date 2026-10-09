# 협업 가이드

VS Code, Git, GitHub를 이용해 팀 프로젝트를 협업하는 방법을 정리한 가이드입니다.

이 가이드에서는 각자 작업 브랜치에서 개발하고, **Pull Request(PR)를 통해 `main` 브랜치에 병합하는 방식**을 사용합니다.

## 협업 진행 흐름

1. GitHub 저장소 생성 및 팀원 초대
2. VS Code에서 저장소 Clone
3. 역할 분담 후 각자 작업 브랜치 생성
4. 각자 작업 및 Commit, Push
5. Pull Request 생성 및 코드 검토
6. Merge 후 최신 코드 동기화 및 테스트

> **핵심 원칙:** `main` 브랜치에서 직접 개발하지 않습니다. 각자 별도 브랜치에서 작업한 뒤 PR로 검토하고 병합합니다.

---

## 1. GitHub 저장소 준비

### 1-1. 저장소 생성

GitHub에서 새 저장소를 생성합니다.
이미 팀원이 저장소를 생성해 두었다면 1-2로 넘어가면 됩니다. 

- 저장소 이름 설정
- 필요에 따라 공개 범위(Public/Private) 설정
- 처음 시작한다면 README 파일을 함께 생성해 초기 커밋 만들기

저장소 생성 후 팀원을 협업자로 초대합니다. 일반적으로 저장소의 `Settings`에서 `Collaborators` 관련 메뉴를 찾을 수 있습니다.

### 1-2. Git 사용자 정보 설정

사용자 정보는 협업 시 누가 커밋을 했는지 확인할 수 있는 지표이기 때문에 한 번 더 config 명령어로 확인해봅시다.

설정 확인:

```bash
git config --global --list
```

만약 사용자 정보가 자신과 다르다면 아래 명령어로 수정합니다.

```bash
git config --global user.name "이름"
git config --global user.email "이메일"
```

> `user.name`과 `user.email`은 커밋 작성자 정보입니다. GitHub 로그인 인증 정보와는 별개입니다.

### 1-3. 저장소 Clone


**터미널에서 Clone**

VS Code 터미널에서 다음 명령어를 실행합니다.

```bash
git clone https://github.com/your-account/your-repository.git
cd your-repository
code .
```

위 URL의 `your-account/your-repository` 부분은 실제 GitHub 계정명과 저장소명으로 바꿉니다. `cd your-repository`의 폴더명도 실제 저장소명으로 바꿉니다. <br> `code .` 명령어가 인식되지 않으면 VS Code에서 직접 `File → Open Folder`로 Clone한 폴더를 열면 됩니다.

---

## 2. 작업 브랜치 생성

`main`에서 직접 작업하지 않고 역할에 맞는 작업 브랜치를 만듭니다.

예시:

- 나: API 기능 구현 → `feat/api`
- 팀원: API 문서 작성 → `docs/api`

<br>

**터미널에서 생성**

저장소를 처음 Clone한 뒤에는 아래 코드대로 최신 `main`에서 새 브랜치를 생성합니다.

```bash
git switch main # 로컬 main 브랜치로 이동
git pull --ff-only origin main # GitHub의 최신 main을 로컬 main에 반영
git switch -c feat/api # 최신 main을 기준으로 feat/api 브랜치를 생성하고 이동
```

현재 브랜치 확인:

```bash
git branch
```

현재 작업 중인 브랜치 앞에는 `*` 표시가 붙습니다.

이미 존재하는 브랜치로 이동할 때는 다음 명령어를 사용합니다.

```bash
git switch feat/api
```

> 브랜치를 분리하면 `main` 브랜치를 건들지 않고, 각자 작업을 독립적으로 진행할 수 있습니다. `feat/`는 기능 개발, `fix/`는 버그 수정, `docs/`는 문서 작업 <br> 이런 식으로 작명 규칙을 정하고 작명하면 좋습니다.

---

## 3. 가상환경 설정

저장소를 clone하고 작업 브랜치를 생성한 직후, 프로젝트 코드를 실행하거나, 프로젝트에 필요한 라이브러리를 설치하기 전에 가상환경을 만드는 것이 좋습니다. <br> 아래 예시는 파이썬을 기준으로 가상환경 설정 방법을 설명합니다.

### 3-1. Python 가상환경 설정

Python 프로젝트에서는 라이브러리 충돌을 방지하기 위해 가상환경을 사용합니다.

**1. 가상환경 생성**

프로젝트 루트 디렉토리에서 실행합니다.<br>
이미 가상환경 (.venv 폴더)가 프로젝트 디렉토리에 존재한다면 이 과정을 생략합니다. 
```bash
python -m venv .venv
```

**2. 가상환경 활성화**

Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Linux / macOS:

```bash
source .venv/bin/activate
```

**3. VS Code 인터프리터 설정**

`Ctrl + Shift + P` → `Python: Select Interpreter` → `.venv` 선택

**4. 라이브러리 설치**

`requirements.txt`가 디렉토리에 이미 있다면 다음 명령어로 설치합시다.

```bash
python -m pip install -r requirements.txt
```

파일이 없다면 필요한 라이브러리를 설치하고, 팀원도 동일한 환경을 구성할 수 있도록 의존성 목록을 관리합니다.

**5. `.gitignore` 확인**

가상환경은 GitHub에 올리지 않도록 `.gitignore`에 다음 항목을 추가합니다.

```gitignore
.venv/
```

가상환경은 브랜치마다 만드는 것이 아니라, 각자의 컴퓨터에서 프로젝트마다 한 번씩 생성합니다. 팀원도 각자 가상환경을 만들고 동일한 의존성 목록을 사용하면 됩니다.

가상환경에 대한 자세한 내용은 아래 참조 링크를 확인하세요.
> [python-virtual-environment - 패키지 버전관리](./python-virtual-environment.md#3-패키지-버전-관리와-requirementstxt)

---

## 4. 파일 작성 및 테스트

VS Code에서 프로젝트 파일을 만들거나 기존 파일을 수정합니다. 예를 들어 Python 파일을 만들었다면 터미널에서 다음과 같이 실행할 수 있습니다.

```bash
python main.py
```

환경에 따라 `python3 main.py`를 사용해야 할 수도 있습니다. 실제 프로젝트에서는 언어나 프레임워크에 맞는 실행 및 테스트 명령어를 사용합니다.

### `.gitignore` 설정

GitHub에 올릴 필요가 없는 파일이나 폴더는 프로젝트 루트의 `.gitignore`에 등록합니다.<br>
특히 비밀번호, API키, 실제 환경 변수 파일 등 민감한 정보는 커밋하기 전, 반드시 .gitignore에 등록 후, 커밋합니다

예시:

```gitignore
# 환경 변수 및 비밀 정보
.env

# Python
__pycache__/
*.py[cod]
.venv/

# Node.js
node_modules/

# 빌드 결과물
build/
dist/

# 로그
*.log
```

프로젝트에서 사용하는 언어와 도구에 맞게 항목을 조정합니다.

> `.gitignore`는 Git이 아직 추적하지 않는 파일에 주로 적용됩니다. 이미 추적 중인 파일은 `.gitignore`에 추가하는 것만으로 추적이 중단되지 않습니다. 민감한 정보는 반드시 커밋하기 전에 `.gitignore`에 등록하세요.

---

## 5. 변경사항 기록 및 Push

코드 작성과 테스트를 마쳤다면 변경사항을 확인하고 Git에 기록합니다.

### 5-1. 변경사항 확인



```bash
git status
```

커밋하기 전에 변경 내용을 검토하고, 실수로 비밀 정보나 불필요한 파일이 포함되지 않았는지 확인합니다.

### 5-2. Stage 및 Commit

파일 하나만 추가하려면 파일명을 지정합니다. 예를 들어 `main.py`를 추가하려면 다음과 같이 입력합니다.

```bash
git add main.py
```

변경 파일을 모두 추가하려면:

```bash
git add .
```

그다음 커밋합니다.

```bash
git commit -m "Implement API functionality"
```

Commit은 변경사항을 **로컬 Git 기록에 저장**하는 과정입니다. GitHub에 업로드되는 것은 아닙니다.

커밋 메시지 예시:

- `Add API endpoint`
- `Fix response validation`
- `Update API documentation`

### 5-3. GitHub에 Push

작업 브랜치를 원격 저장소에 올립니다.

```bash
git push -u origin feat/api
```

- `origin`: 원격 저장소의 별칭
- `feat/api`: 올릴 브랜치 이름
- `-u`: 로컬 브랜치와 원격 브랜치를 연결해 이후 명령어를 간단하게 사용할 수 있도록 설정

최초 Push 이후에는 보통 다음 명령어만 실행하면 됩니다.

```bash
git push
```

VS Code에서는 Source Control 메뉴의 `Push` 또는 `Publish Branch`를 이용할 수도 있습니다. 최초 게시 시 `Publish Branch`가 표시될 수 있습니다.

### 5-4. GitHub 인증 문제

Push 또는 Clone 중 로그인 요청이 나타나면 안내에 따라 GitHub 계정으로 인증합니다. VS Code에서 GitHub 로그인을 요청할 수도 있습니다.

GitHub는 일반 계정 비밀번호를 Git의 HTTPS 인증 비밀번호로 사용하는 방식을 지원하지 않습니다. <br>인증이 반복해서 실패한다면 VS Code의 계정 로그인 상태와 Git *자격 증명 관리 설정*을 확인합니다. GitHub CLI가 설치되어 있다면 브라우저 인증으로 해도 됩니다. <br>아래 토글들을 클릭하면 자세한 설명이 나옵니다.

<details>
<summary>자격 증명 관리자 설정 방법</summary>

<br><br>

### Git 자격 증명 관리 설정

Git 자격 증명 관리자는 GitHub 로그인 정보를 안전하게 저장하고, 이후 Push나 Pull을 할 때 인증 정보를 자동으로 사용할 수 있도록 도와주는 도구입니다.

Windows에서는 Git for Windows에 포함된 **Git Credential Manager(GCM)** 를 사용할 수 있습니다.

#### 1. GitHub 저장소 연결 확인

VS Code 터미널에서 실행합니다.

```bash
git remote -v
```

`https://github.com/`으로 시작하는 저장소 주소가 표시되는지 확인합니다.

#### 2. 자격 증명 관리자 설정 확인

```bash
git config --global credential.helper
```

출력 결과에 `manager`가 표시되면 일반적으로 설정되어 있는 것입니다.

아무것도 표시되지 않는다면 다음 명령어로 설정합니다.

```bash
git config --global credential.helper manager
```

#### 3. GitHub 인증하기

다음 명령어를 실행합니다.

```bash
git push
```

인증이 필요하면 브라우저가 열리거나 로그인 안내가 표시될 수 있습니다.

1. GitHub 계정으로 로그인합니다.
2. 필요한 권한을 승인합니다.
3. VS Code로 돌아와 Push가 완료되었는지 확인합니다.

인증 정보가 정상적으로 저장되면 이후에는 매번 로그인하지 않고 Git을 사용할 수 있습니다.

#### 4. 인증이 계속 실패하는 경우

이전에 저장한 GitHub 계정 정보가 잘못되었을 수 있습니다.

1. Windows 시작 메뉴에서 `자격 증명 관리자`를 검색합니다.
2. `Windows 자격 증명`을 선택합니다.
3. `git:https://github.com` 등 GitHub 관련 항목을 찾습니다.
4. 해당 GitHub 항목만 제거합니다.
5. VS Code 터미널에서 `git push`를 다시 실행합니다.
6. 브라우저가 안내되면 올바른 GitHub 계정으로 인증합니다.

관련 항목만 삭제해야 하며, 다른 서비스의 자격 증명은 삭제하지 않습니다.

> **주의:** GitHub 계정 비밀번호, 액세스 토큰, 일회용 인증 코드를 다른 사람과 공유하지 마세요. GitHub 계정 비밀번호를 일반 Git HTTPS 인증 비밀번호로 입력하는 방식은 지원되지 않습니다.

<br><br><br>

</details>



<details>
<summary>GitHub CLI로 인증하는 방법</summary>

<br><br>

```bash
gh auth login
```

안내에 따라 `GitHub.com`과 HTTPS 및 브라우저 로그인을 선택하고, 터미널에 표시되는 일회용 코드를 GitHub의 공식 기기 로그인 페이지에서 입력합니다.

인증 코드는 일회용이더라도 다른 사람에게 공유하지 마세요. 인증 상태는 다음 명령어로 확인할 수 있습니다.

```bash
gh auth status
```
> GitHub CLI(`gh`)는 선택 사항입니다. 설치되어 있지 않다면 VS Code가 안내하는 로그인 절차를 이용하면 됩니다.

<br><br><br>

</details>

---

## 6. Pull Request(PR) 생성 및 코드 검토

작업 브랜치에서 모든 작업을 완료하고 이제 `main` 에 합칠 일만 남았다면, GitHub에서 Pull Request를 생성합니다.

예시:

- API 구현: `feat/api` → `main`
- API 문서: `docs/api` → `main`

### 6-1. Pull Request란?

Pull Request(PR)는 내 작업 내용을 검토한 뒤 해당 브랜치에 병합해주세요~ 라는 기능입니다. 팀원은 변경 내용을 확인하고 의견을 남기거나 수정을 요청할 수 있습니다.<br> PR 절차를 익혀두면 나중에 협업할 때 도움이 되니 미리 PR 협업 방법을 써서 익혀봅시다.

### 6-2. PR 생성 방법

1. GitHub 저장소의 `Pull requests` 탭으로 이동합니다.
2. `New pull request`를 선택합니다.
3. 브랜치를 다음과 같이 설정합니다.
   - **base:** `main` — 변경사항을 합칠 대상 브랜치
   - **compare:** `feat/api` — 내가 작업한 브랜치
4. 제목과 변경 내용, 테스트 결과를 작성합니다.
5. `Create pull request`를 선택합니다.
6. 팀원에게 코드 검토를 요청하고 의견을 반영합니다.

PR 설명에는 다음 내용을 포함하면 검토하기 쉽습니다.

- 무엇을 변경했는가?
- 왜 변경했는가?
- 어떤 테스트를 수행했는가?
- 검토자가 확인해야 할 사항은 무엇인가?

코드 검토가 완료되고 병합 조건을 충족하면 `main`에 Merge합니다.

---

## 7. 만약 팀원의 PR이 나보다 먼저 병합된 경우와 `main` 반영 및 최종 동기화

나보다 팀원의 PR이 먼저 병합되었다면, 내 브랜치는 공통 작업 브랜치보다 뒤처질 수 있습니다. 그래서 먼저 최신 `main` 을 본인의 브랜치에 반영하는 것이 좋습니다. <br><br> 만약에 팀원의 PR이 먼저 병합되지 않았다면 7-1 ~ 7-4 과정을 무조건 실행할 필요 없이 바로 7-5부터 진행하면 됩니다.<br>
7-1 ~ 7-4는 다른 사람의 변경사항을 내 브랜치에 반영할 필요가 있을 때 수행하는 과정입니다.

### 7-1. 원격 저장소의 최신 정보 가져오기

```bash
git fetch origin
```

`fetch`는 원격 저장소의 최신 브랜치 정보를 가져옵니다. 내 작업 브랜치의 코드를 변경하지는 않습니다.

### 7-2. 작업 브랜치로 이동

```bash
git switch feat/api
```

내 작업 브랜치로 이동합니다. 이후 실행하는 병합 명령어는 이 브랜치를 기준으로 적용됩니다. `feat/api` 부분에는 본인의 작업 브랜치명을 작성합니다.<br>작업 중인 변경사항이 있다면 먼저 커밋하거나 안전하게 보관한 후 브랜치를 전환합니다.

### 7-3. 최신 `main`을 작업 브랜치에 병합

```bash
git merge origin/main
```

현재 브랜치는 `feat/api` 이므로 `origin/main` 의 변경 사항을 내 브랜치에 병합합니다.<br>
여기서 내 브랜치가 `main` 에 합쳐지는 것이 아니라 `main` 의 변경사항만 내 브랜치로 가져오는 것입니다.
충돌이 발생하지 않으면 병합은 자동으로 완료됩니다.

충돌이 발생하면 `git status`로 충돌이 발생한 파일을 확인하고, VS Code에서 해당 파일을 열어 어느 코드를 유지할지 정리합니다.

정리할 때는 팀원들과 논의한 후 결정하는 것이 좋습니다. 수정한 파일에서 충돌 표시(`<<<<<<<`, `=======`, `>>>>>>>`)를 모두 제거한 뒤 다음 명령어를 실행합니다.

```bash
git add <수정한_파일>
git status
git commit
```

여러 파일에 충돌이 있다면 해당 파일들을 각각 Stage합니다. `git add`는 충돌이 해결되었음을 Git에 알리는 과정이며, `git commit`을 실행하면 병합이 최종 완료됩니다.

> **참고: Vim 편집기가 실행되는 경우**
>
> 커밋하고 나면 뭔가 어렵고 복잡한 창이 뜰 수도 있습니다.<br>
> 이때 기존 메시지를 그대로 사용하려면 다음 순서대로 입력합니다.
>
> 1. `Esc` 누르기
> 2. `:wq` 입력하기
> 3. `Enter` 누르기
>
> `:wq`는 내용을 저장하고 편집기를 종료하는 명령어입니다. 편집기가 종료되면 커밋이 진행되고 병합이 마무리됩니다.

### 7-4. 업데이트한 작업 브랜치 Push

```bash
git push
```

최신 `main` 의 변경사항이 반영된 `feat/api` 를 GitHub에 올립니다. <br> 만약 이미 PR을 만들어 두고 병합하지 않은 상태라면 해당 PR에 새로운 변경사항이 자동으로 반영됩니다. <br> 아직 PR을 생성하지 않았다! 그러면 `feat/api`를 `main`에 병합하는 PR을 생성합니다.<br> 팀원들의 코드 검토가 완료되고 병합 조건을 충족하면, **GitHub의 PR 페이지에서 `Merge pull request` 버튼을 눌러 `main`에 병합합니다.**

> PR을 병합할 때는 별도로 VS Code에서 `git merge`나 `git push`를 실행할 필요가 없습니다. GitHub에서 병합한 이후에는 로컬 `main`을 최신 상태로 동기화하면 됩니다.

### 7-5. 로컬 `main` 최신화

PR이 병합된 후 로컬 `main`을 최신 상태로 업데이트합니다. 내 컴퓨터의 로컬 `main` 은 아직 옛날 상태일 수도 있기 때문입니다.

```bash
git switch main
git pull --ff-only origin main
```

`--ff-only`는 Fast-forward 방식으로만 업데이트하도록 제한합니다. 이 방식으로 업데이트할 수 없다면 원인을 확인하고, 로컬 `main`에 별도 커밋이 있는지 점검합니다.

### 7-6. Git 기록 확인

```bash
git log --oneline --graph --all --decorate -10
```

브랜치가 어떻게 분기되었고 어떤 커밋이 병합되었는지 확인할 수 있습니다. VS Code의 Source Control Graph에서도 커밋 이력을 시각적으로 확인할 수 있습니다.

---

## 8. 자주 사용하는 명령어 요약

| 작업 | 명령어 | 설명 |
|---|---|---|
| Git 설치 확인 | `git --version` | Git이 설치되어 있는지 확인 |
| 저장소 복제 | `git clone https://github.com/your-account/your-repository.git` | URL을 실제 저장소 주소로 바꿔 원격 저장소 복제 |
| 브랜치 생성 | `git switch -c feat/api` | 새 브랜치를 만들고 이동 |
| 브랜치 확인 | `git branch` | 로컬 브랜치 목록 확인 |
| 변경사항 확인 | `git status` | 파일 변경 상태 확인 |
| Stage | `git add main.py` | 예시 파일의 변경사항을 커밋 대상으로 선택 |
| Commit | `git commit -m "메시지"` | 로컬 작업 기록 저장 |
| Push | `git push -u origin feat/api` | 작업 브랜치를 원격 저장소에 게시 |
| 원격 정보 갱신 | `git fetch origin` | 원격 브랜치 정보 가져오기 |
| 최신 `main` 병합 | `git merge origin/main` | 현재 브랜치에 최신 `main` 반영 |
| `main` 최신화 | `git pull --ff-only origin main` | 로컬 `main`을 원격 상태로 업데이트 |
| 이력 확인 | `git log --oneline --graph --all --decorate -10` | 커밋 및 브랜치 흐름 확인 |

---

## 마무리

협업할 때는 각자 작업 브랜치에서 개발하고, Commit과 Push로 변경사항을 공유한 다음, Pull Request를 통해 검토와 병합을 진행합니다.

**반드시 기억할 원칙은 세 가지입니다.**

1. `main`에서 직접 작업하지 않기
2. 작업 단위로 Commit하고 원격 브랜치에 Push하기
3. 다른 팀원의 변경사항을 확인하고 PR을 통해 병합하기

VS Code의 Source Control 화면과 Git 명령어는 같은 저장소를 사용하므로, 익숙한 방법을 사용하면 됩니다. 다만 Commit은 로컬에 기록하는 작업이고, Push는 원격 저장소에 업로드하는 작업이라는 차이를 기억해야 합니다.