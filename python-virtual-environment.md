# Python 패키지 및 가상환경 가이드

Python 패키지 설치와 관리 방법, 가상환경의 필요성 및 프로젝트별 개발 환경을 구성하는 방법을 정리한 문서입니다.

## 1. 패키지(Package)

패키지는 특정 기능을 쉽게 사용할 수 있도록 미리 만들어 배포한 코드 묶음입니다.
<br>필요한 패키지를 설치한 뒤 `import`하여 내 코드에서 사용할 수 있습니다.<br>
예를 들어 `requests` 패키지를 사용하면 HTTP 요청을 쉽게 보낼 수 있습니다.

```python
import requests

response = requests.get("https://example.com")
print(response.status_code)
```

## 2. pip와 PyPI

### 2-1. pip란?

`pip`는 Python 패키지를 설치하고 관리하는 도구입니다.

**PyPI(Python Package Index)** 는 Python 패키지들이 등록되어 있는 온라인 저장소입니다.
<br>다음 명령어를 실행하면 `pip`가 패키지를 찾아 현재 Python 환경에 설치합니다.

```bash
python -m pip install requests
```

### 2-2. 패키지 설치 흐름

1. 개발자가 `pip install` 명령어를 실행합니다.
2. `pip`가 PyPI에서 필요한 패키지를 찾습니다.
3. 패키지와 필요한 의존 패키지를 내려받습니다.
4. 현재 활성화된 Python 환경에 설치합니다.

### 2-3. 패키지 의존성(Dependency)

패키지가 정상적으로 동작하기 위해 다른 패키지가 필요할 수 있습니다. 이 관계를 **의존성**이라고 합니다.
<br>예를 들어 A 패키지가 B 패키지를 필요로 한다면, A를 설치할 때 B도 함께 설치될 수 있습니다.
<br>`pip`는 패키지에 선언된 의존성을 확인하고 필요한 패키지를 함께 설치합니다.

### 2-4. pip 주요 명령어

```bash
# 현재 pip 버전 확인
python -m pip --version

# pip 업그레이드
python -m pip install --upgrade pip

# 패키지 설치
python -m pip install requests

# 특정 버전 설치
python -m pip install requests==2.31.0

# 특정 버전 이상 설치
python -m pip install "requests>=2.20"

# 패키지 업그레이드
python -m pip install --upgrade requests

# 설치된 패키지 목록 확인
python -m pip list

# 특정 패키지의 상세 정보 확인
python -m pip show requests

# 패키지 삭제
python -m pip uninstall requests
```

> `python -m pip`는 현재 지정된 Python 인터프리터를 통해 pip를 실행하는 방식입니다. 여러 Python 환경을 사용하는 경우 패키지가 엉뚱한 환경에 설치되는 일을 줄이는 데 도움이 됩니다.

## 3. 패키지 버전 관리와 requirements.txt

### 3-1. 패키지 버전 관리

패키지는 버전에 따라 기능이나 동작 방식이 달라질 수 있습니다.
<br>따라서 프로젝트에 사용하는 패키지 버전을 관리해야 다른 컴퓨터에서도 비슷한 개발 환경을 구성할 수 있습니다.

```bash
# 정확히 지정한 버전
python -m pip install requests==2.31.0

# 지정한 버전 이상
python -m pip install "requests>=2.20"
```

- `==`: 지정한 버전 사용
- `>=`: 지정한 버전 이상 사용

정확한 버전을 고정하면 환경 재현에 유리하지만, 프로젝트에 필요한 버전과 호환성을 함께 고려해야 합니다.

### 3-2. requirements.txt란?

`requirements.txt`는 프로젝트에서 사용하는 Python 패키지와 버전을 기록하는 파일입니다.
<br>팀원과 동일한 개발 환경을 구성하거나 다른 컴퓨터에서 프로젝트를 재현할 때 사용합니다.

**현재 환경의 패키지 목록 저장**

```bash
python -m pip freeze > requirements.txt
```

`pip freeze`는 현재 Python 환경에 설치된 패키지와 버전을 출력합니다. `>`는 출력 결과를 파일에 저장하며, 기존 파일이 있다면 내용을 덮어씁니다.

**requirements.txt에 기록된 패키지 설치**

```bash
python -m pip install -r requirements.txt
```

`-r`은 지정한 파일을 읽어 그 안에 기록된 패키지를 설치하라는 옵션입니다.

예시:

```text
requests==2.31.0
numpy==1.26.4
```

> `pip freeze`를 실행하기 전에는 올바른 가상환경이 활성화되어 있는지 확인합니다. 전역 환경에서 실행하면 다른 프로젝트의 패키지까지 기록될 수 있습니다.

## 4. 가상환경(Virtual Environment)

### 4-1. 가상환경이 필요한 이유

일반 Python 환경에 패키지를 설치하면 여러 프로젝트가 같은 패키지 공간을 공유할 수 있습니다.
<br>그런데 프로젝트마다 필요한 패키지 버전이 다르면 충돌이 발생할 수 있습니다.

예를 들어 다음 상황을 가정해 보겠습니다.

- 프로젝트 A: `requests` 2.31.0 필요
- 프로젝트 B: 다른 버전의 `requests` 필요

두 프로젝트가 같은 Python 환경을 공유하면 패키지 버전을 관리하기 어려워집니다.
<br>이 문제를 줄이기 위해 프로젝트별로 독립적인 패키지 공간을 구성합니다. 이것이 **가상환경**입니다.

### 4-2. 가상환경의 특징

- 프로젝트별로 독립적인 패키지 설치 공간을 가집니다.
- 한 프로젝트의 패키지 설치 및 변경이 다른 프로젝트에 미치는 영향을 줄입니다.
- 프로젝트마다 서로 다른 패키지 버전을 사용할 수 있습니다.
- 팀원과 환경을 공유할 때 패키지 목록을 별도로 관리할 수 있습니다.

가상환경은 Python 실행 환경을 격리하기 위한 방법이며, Python 자체를 완전히 별도로 설치하는 것과는 다릅니다.

### 4-3. 패키지 설치 위치

패키지는 현재 사용 중인 Python 환경에 따라 다른 위치에 설치됩니다.

- **일반 Python 환경:** 해당 Python 설치 경로의 `site-packages`
- **가상환경:** 프로젝트의 `.venv` 아래에 있는 `site-packages`

가상환경을 활성화하면 `python`과 `pip` 명령어가 해당 가상환경의 실행 파일을 사용하도록 경로가 설정됩니다.

## 5. 가상환경 생성 및 활성화

### 5-1. 가상환경 생성

프로젝트 루트 디렉터리에서 실행합니다.

```bash
python -m venv .venv
```

- `python -m venv`: Python의 가상환경 생성 모듈을 실행합니다.
- `.venv`: 생성할 가상환경 폴더 이름입니다.

이미 정상적으로 생성된 `.venv`가 있다면 다시 생성할 필요는 없습니다.

> Ubuntu 등 일부 Linux 환경에서는 가상환경 생성에 필요한 패키지가 별도로 설치되어 있어야 할 수 있습니다. 예를 들어 `python3-venv` 또는 사용 중인 Python 버전에 맞는 `python3.12-venv` 패키지가 필요할 수 있습니다.

### 5-2. 가상환경 활성화

운영체제와 터미널에 따라 명령어가 다릅니다.

**Windows PowerShell**

```powershell
.\.venv\Scripts\Activate.ps1
```

**Windows 명령 프롬프트(CMD)**

```bat
.venv\Scripts\activate.bat
```

**Windows Git Bash**

```bash
source .venv/Scripts/activate
```

**Linux / macOS**

```bash
source .venv/bin/activate
```

활성화되면 터미널 프롬프트에 보통 `(.venv)`가 표시됩니다.

다음 명령어로 실제 사용 중인 Python 경로를 확인할 수도 있습니다.

```bash
python -c "import sys; print(sys.executable)"
```

출력 경로에 프로젝트의 `.venv`가 포함되어 있다면 해당 가상환경의 Python을 사용하고 있는 것입니다.

### 5-3. 가상환경 종료

```bash
deactivate
```

현재 활성화된 가상환경에서 빠져나옵니다.

가상환경 폴더나 설치한 패키지가 삭제되는 것은 아닙니다.

## 6. VS Code 인터프리터 설정

가상환경을 만들었다면 VS Code가 해당 환경의 Python을 사용하도록 설정합니다.

1. VS Code에서 프로젝트 폴더를 엽니다.
2. `Ctrl + Shift + P`를 누릅니다.
3. `Python: Select Interpreter`를 검색합니다.
4. 프로젝트의 `.venv`에 해당하는 Python 인터프리터를 선택합니다.

이후 해당 환경에 맞게 코드를 실행하고 패키지를 설치합니다.

> 터미널에서 가상환경을 활성화하는 것과 VS Code의 Python 인터프리터를 선택하는 것은 서로 관련 있지만 별개의 설정입니다. 둘 다 올바른 환경을 사용하고 있는지 확인하는 것이 좋습니다.

## 7. 프로젝트에서 사용하는 순서

### 7-1. 새로운 Python 프로젝트를 시작할 때

1. 프로젝트 폴더 생성
2. 가상환경 생성
3. 가상환경 활성화
4. 필요한 패키지 설치
5. 패키지 목록을 `requirements.txt`에 기록
6. 코드 작성 및 테스트

패키지를 추가하거나 버전을 변경했다면 필요한 경우 `requirements.txt`도 갱신합니다.

### 7-2. 다른 사람이 만든 프로젝트를 실행할 때

팀원이 기존 프로젝트를 공유했다면 다음 순서로 진행합니다.

1. GitHub 저장소 Clone
2. 프로젝트 폴더로 이동
3. 가상환경 생성
4. 가상환경 활성화
5. `requirements.txt`의 패키지 설치
6. VS Code 인터프리터 설정
7. 프로젝트 실행 및 테스트

예시:

```bash
git clone <저장소_URL>
cd <저장소명>

python -m venv .venv
```

가상환경 활성화 명령어는 운영체제와 터미널에 맞게 선택합니다. 활성화한 뒤 다음 명령어를 실행합니다.

```bash
python -m pip install -r requirements.txt
```

## 8. Git과 가상환경을 함께 사용할 때

### 8-1. `.venv`는 GitHub에 올리지 않기

가상환경 폴더는 각자의 컴퓨터에서 생성하며 GitHub에 공유하지 않습니다.

가상환경에는 설치된 패키지와 실행 파일 등이 포함되어 있고, 운영체제와 Python 설치 경로 등의 환경에 영향을 받기 때문입니다.

프로젝트 루트의 `.gitignore`에 다음 내용을 추가합니다.

```gitignore
# Python virtual environment
.venv/
```

### 8-2. 공유할 것은 requirements.txt

팀원끼리 가상환경 폴더 자체를 공유하는 대신, 패키지 목록을 기록한 `requirements.txt`를 GitHub에 올립니다.

팀원은 이 파일을 바탕으로 자신의 컴퓨터에서 가상환경을 만들고 필요한 패키지를 설치합니다.

```bash
python -m pip freeze > requirements.txt
git add requirements.txt
git commit -m "Update Python dependencies"
```

위 예시는 현재 가상환경의 패키지 목록을 갱신하고 변경사항을 커밋하는 과정입니다.

> **핵심:** `.venv`는 공유하지 않고, `requirements.txt`를 공유합니다. 또한 가상환경은 브랜치마다 만드는 것이 아니라 각자의 컴퓨터에서 프로젝트마다 하나씩 구성하면 됩니다.

---

## 정리

| 개념 | 역할 |
|---|---|
| Package | 특정 기능을 제공하는 코드 묶음 |
| pip | Python 패키지 설치 및 관리 도구 |
| PyPI | Python 패키지가 등록된 온라인 저장소 |
| Dependency | 패키지가 동작하기 위해 필요한 다른 패키지 |
| Virtual Environment | 프로젝트별로 독립된 Python 패키지 환경을 구성하는 방법 |
| `site-packages` | Python 패키지가 설치되는 디렉터리 |
| `requirements.txt` | 프로젝트의 패키지 및 버전 목록을 기록하는 파일 |
| `.gitignore` | Git에서 추적하지 않을 파일과 디렉터리를 지정하는 파일 |

**실무에서 기억할 원칙**

1. 프로젝트마다 가상환경을 구성합니다.
2. 패키지는 활성화된 가상환경에 설치합니다.
3. `requirements.txt`로 팀원 간 의존성을 공유합니다.
4. `.venv`는 GitHub에 올리지 않습니다.
5. 패키지 버전을 변경했다면 테스트하고 의존성 목록도 갱신합니다.