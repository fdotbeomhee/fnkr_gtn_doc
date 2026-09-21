# Griptape Nodes 설치

Griptape Nodes를 실행하는 방법에는 두 가지가 있습니다:

- **[Griptape Nodes Desktop](#griptape-nodes-desktop-권장)** (권장) — 엔진과 에디터가 통합된 완전 관리형 데스크톱 애플리케이션입니다. 로그인 처리, API 키 발급, 자동 업데이트를 모두 지원하며 터미널 명령어가 필요 없습니다.
- **[수동 엔진 설치](#고급-수동-엔진-설치)** — 웹 기반 에디터와 연동하여 명령줄에서 직접 엔진을 설치하고 실행하려는 파워 유저를 위한 방식입니다.

조직에서 Griptape 계정 대신 **라이선스 키**를 제공받은 경우, 아래의 [라이선스로 활성화](#라이선스로-활성화)를 참조하세요.

## Griptape Nodes Desktop (권장)

Griptape Nodes Desktop은 엔진과 에디터를 모두 포함하는 단일 애플리케이션입니다. 엔진을 자동으로 설치 및 관리하고, 로그인을 처리하며, Griptape API 키를 자동으로 프로비저닝하므로 수동으로 설정할 것이 없습니다.

### 1. 다운로드 및 설치

[griptapenodes.com](https://griptapenodes.com)에서 운영체제에 맞는 Griptape Nodes Desktop을 다운로드하세요. macOS, Windows, Linux용으로 제공됩니다.

### 2. 로그인

애플리케이션을 실행하고 **Login or Sign-Up**을 클릭하여 Griptape 계정으로 로그인합니다.

<!-- screenshot: the desktop app login screen showing the Login or Sign-Up button and the Activate with a License button -->

> 이미 [Griptape Cloud](https://cloud.griptape.ai)에 가입하셨다면 기존 계정 정보로 바로 로그인할 수 있습니다!

조직에서 Griptape 계정 대신 라이선스 키를 발급한 경우, 대신 **Activate with a License**를 클릭하세요. 자세한 내용은 [라이선스로 활성화](#라이선스로-활성화)를 참조하세요.

### 3. 워크스페이스 선택

처음 실행하면 *워크스페이스 디렉토리(workspace directory)*를 선택하라는 메시지가 표시됩니다. 워크스페이스 디렉토리는 Griptape Nodes가 [프로젝트 파일](./glossary.md#project-files)과 [생성된 에셋](./glossary.md#generated-assets)을 저장하는 위치입니다. 기본값을 그대로 사용하거나 원하는 위치를 선택할 수 있습니다.

<!-- screenshot: the desktop app first-run workspace setup step with the default workspace directory shown -->

모든 설정이 완료되었습니다! 애플리케이션이 엔진 설치, API 키 생성, 에디터 실행 등 나머지 작업을 모두 처리합니다. 이제 [튜토리얼](tutorials/index.md)을 시작할 준비가 되었습니다.

워크스페이스 변경, 라이선스 관리, 애플리케이션 업데이트 방식 설정 등은 나중에 [앱 설정](guides/desktop/app_settings.md)에서 언제든지 조정할 수 있습니다.

## 고급: 수동 엔진 설치

엔진을 직접 관리하는 것을 선호하시나요? 이 방식에서는 명령줄에서 엔진을 설치하고 웹 브라우저([https://nodes.griptape.ai](https://nodes.griptape.ai))에서 에디터를 사용합니다.

Editor와 Engine은 서로 분리되어 이벤트 서비스를 통해 통신하므로, 엔진이 브라우저와 동일한 머신에서 실행될 필요는 없습니다. 노트북보다 더 많은 리소스가 필요한 경우 별도의 머신에서 엔진을 실행할 수 있으며, 아래 지침은 두 방식 모두에 동일하게 적용됩니다.

### 1. 회원가입 또는 로그인

시작하려면 [https://griptapenodes.com](https://griptapenodes.com)을 방문하여 로그인 버튼을 클릭하세요.

> 이미 [Griptape Cloud](https://cloud.griptape.ai)에 가입하셨다면 기존 계정 정보를 그대로 사용할 수 있습니다!

로그인하면 엔진 설정 대화상자와 함께 에디터가 열립니다(연결된 엔진이 없을 때 자동으로 표시됨). **Get Started** 탭을 선택하고 **Install Engine Manually** 섹션을 펼친 후 아래 단계를 따르세요.

<!-- screenshot: the engine setup dialog on the Get Started tab, showing the Griptape Nodes Desktop download card and the Install Engine Manually accordion expanded -->

### 2. 엔진 설치

1. 아직 설치하지 않았다면 [uv](https://docs.astral.sh/uv/getting-started/installation/)를 설치합니다.

1. 다음 명령어를 실행하여 Griptape Nodes Engine을 설치합니다:

    ```bash
    uv tool install griptape-nodes
    ```

설치 후 터미널에서 `griptape-nodes`(또는 줄임말 `gtn`)를 *처음* 실행하면 일련의 설정 질문이 단계별로 안내됩니다.

### 3. 환경 설정

**첫 번째**, Griptape API Key를 입력하라는 메시지가 표시됩니다. 이 키를 통해 엔진이 에디터와 통신할 수 있습니다.

1. 웹 브라우저의 에디터 탭으로 돌아갑니다. 엔진 설정 대화상자의 **Get Started** 탭 아래 **Install Engine Manually** 섹션에서 **Generate an API Key** 단계를 엽니다.

1. **Generate API Key** 버튼을 클릭합니다. 버튼 위치에 새 키가 표시됩니다.

1. 키를 복사하여 터미널 프롬프트에 붙여넣습니다. 이 키는 브라우저에 한 번만 표시되므로 다음 단계로 넘어가기 전에 반드시 복사하세요.

<!-- screenshot: the Generate an API Key step in the Install Engine Manually section, with a generated key shown in place of the button -->

```
╭─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ Griptape API Key                                                                                                        │
│         A Griptape API Key is needed to proceed.                                                                        │
│         This key allows the Griptape Nodes Engine to communicate with the Griptape Nodes Editor.                        │
│         In order to get your key, return to the https://nodes.griptape.ai tab in your browser and click the button      │
│         "Generate API Key".                                                                                             │
│         Once the key is generated, copy and paste its value here to proceed.                                            │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
Griptape API Key (YOUR-KEY-HERE):
```

!!! info

    이전에 `gtn init`을 실행한 적이 있다면 이 대화상자에 기존 키가 표시될 수 있습니다. Enter 키를 눌러 그대로 수락하거나 필요에 따라 다른 값을 입력할 수 있습니다.

**두 번째**, *워크스페이스 디렉토리(workspace directory)*를 설정하라는 메시지가 표시됩니다. 워크스페이스 디렉토리는 엔진이 [프로젝트 파일](./glossary.md#project-files) 및 [생성된 에셋](./glossary.md#generated-assets)을 저장하는 위치입니다. 또한 Griptape Nodes의 [.env](./glossary.md#.env) 파일 및 Griptape Nodes [비밀 키(secret keys)](./glossary.md#secret-keys) 파일도 포함됩니다.

```
╭───────────────────────────────────────────────────────────────────╮
│ Workspace Directory                                               │
│     Select the workspace directory. This is the location where    │
│     Griptape Nodes will store your saved workflows.               │
│     You may enter a custom directory or press Return to accept    │
│     the default workspace directory                               │
╰───────────────────────────────────────────────────────────────────╯
Workspace Directory (/Users/user/Documents/GriptapeNodes)
```

Enter 키를 누르면 기본값(`<현재_작업_디렉토리>/GriptapeNodes`, 여기서 `<현재_작업_디렉토리>`는 `gtn` 명령어를 실행한 디렉토리)이 사용됩니다. 또는 원하는 다른 경로를 직접 지정할 수도 있습니다.

**마지막으로**, 몇 가지 선택적 구성 단계가 안내됩니다. 각 단계는 기본값을 선택하여 건너뛸 수 있으며, 나중에 언제든지 다시 설정할 수 있습니다:

- **Storage backend** — 정적 파일을 로컬에 저장(기본값)할지 Griptape Cloud 버킷에 저장할지 선택합니다.
- **Griptape Cloud bucket** — 여러 머신 간에 워크플로우와 에셋을 동기화하는 데 사용됩니다.
- **Hugging Face token** — Hugging Face Hub에서 접근 제한(gated) 모델을 다운로드하려는 경우에만 필요합니다.
- **Additional libraries** — 선택적으로 Diffusers 및 Griptape Cloud 노드 라이브러리를 설치합니다.

> 향후 변경 사항이 필요한 경우 언제든지 `gtn init` 명령어를 사용하여 이 설정 마법사로 돌아올 수 있습니다.

### 4. 엔진 시작

이제 설치가 완료되었으며 첫 번째 워크플로우를 만들거나 샘플 워크플로우를 사용해 볼 준비가 되었습니다. 시작하려면 터미널에서 `griptape-nodes`(또는 `gtn`)를 실행한 다음 브라우저로 돌아갑니다. [https://nodes.griptape.ai](https://nodes.griptape.ai) 브라우저 탭이 업데이트되어 빈 캔버스에서 시작할 수 있는 *Create from scratch*와 함께 실험해 볼 수 있는 다양한 샘플 Griptape Nodes 워크플로우가 표시됩니다!

<!-- screenshot: the editor after the engine connects, showing Create from scratch and the sample workflows -->

## 라이선스로 활성화

조직에서는 개별 Griptape 계정 대신 라이선스 키를 기반으로 Griptape Nodes를 운영할 수 있습니다. 조직 관리자로부터 라이선스 키를 전달받은 경우 별도의 회원가입이 필요하지 않습니다:

1. 위에서 설명한 대로 [Griptape Nodes Desktop](#griptape-nodes-desktop-권장)을 설치합니다.
1. 로그인 화면에서 로그인 대신 **Activate with a License**를 클릭합니다.
1. 라이선스 키를 붙여넣고 활성화합니다.

조직에서 온프레미스 [Admin Server](enterprise/admin_server.md)를 운영하는 경우 활성화 중에 해당 서버를 지정하게 됩니다. 라이선스 활성화 과정의 전체 단계는 [Admin Server 사용하기](enterprise/using_the_admin_server.md)를 참조하세요.

라이선스 키 발급 및 관리를 담당하는 조직 관리자는 [Admin Dashboard](enterprise/admin_dashboard.md) 가이드부터 살펴보세요. 두 배포 모델에서 각 구성 요소가 어떻게 결합되는지 보여주는 다이어그램은 [아키텍처](architecture.md)를 참조하세요.

## 다음 단계

이제 Griptape Nodes에서 실제로 작업하는 방법을 배워보겠습니다! [시작하기](tutorials/index.md)

Griptape Nodes를 제거하려면 [Griptape Nodes 제거](uninstalling.md)를 참조하세요.
