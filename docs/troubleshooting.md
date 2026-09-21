# 문제 해결 (Troubleshooting)

이 페이지는 사용자가 가장 자주 겪는 문제와 현상, 그 원인 및 해결 방법을 정리한 문서입니다. 여기에 없는 문제가 발생한 경우 [FAQ](faq.md)를 확인하거나 [FAQ 하단](faq.md#피드백을-제공하거나-질문을-하려면-어디로-가야-하나요)의 소통 채널을 통해 문의해 주세요.

## 에디터에 이미지나 비디오가 표시되지 않음

**증상**

- Load Image, Save Image 또는 미디어 미리보기 노드에 이미지 대신 빈 영역이 표시됩니다.
- 파일이 디스크(예: `{outputs}/images/...`)에 분명히 존재하지만 에디터에 표시되지 않습니다.
- 새 이미지를 업로드할 때 다음과 유사한 오류가 발생합니다:

    ```
    Error: CreateStaticFileUploadUrl Failed
    Description: Failed to create presigned URL for file ...: Client error
    '404 Not Found' for url 'http://localhost:8124/static-upload-urls'
    ```

**원인**

에디터의 미디어는 엔진이 포트 `8124`에서 시작하는 로컬 **정적 파일 서버(static file server)**를 통해 제공됩니다. 해당 포트가 이미 사용 중인 경우(주로 **이전에 실행된 두 번째 또는 잔여 Griptape Nodes 엔진이 계속 실행 중인 경우**), 새 엔진의 정적 서버는 OS가 할당한 다른 포트로 대체 실행됩니다. 이로 인해 미디어 요청이 두 엔진에 분산되어, 잔여 엔진이 기본 포트를 점유하고 실제 작업 중인 엔진은 다른 포트에서 서비스하므로 미리보기가 로드되지 않고 `404` 오류와 함께 업로드가 실패합니다.

**해결 방법**

1. 먼저 에디터를 새로고침합니다(Windows/Linux: Ctrl+R, macOS: Cmd+R). 단순 표시 오류가 해결될 수 있습니다.
1. 미디어가 여전히 표시되지 않으면 **엔진이 하나만 실행 중인지 확인**하세요. Griptape Nodes를 완전히 종료한 후 남아 있는 엔진 프로세스를 확인합니다:
    - **Windows**: 작업 관리자를 열고 남아 있는 `Python` 프로세스를 찾아 종료합니다.
    - **macOS / Linux**: 터미널에서 `pgrep -fl griptape`를 실행(또는 엔진을 실행 중인 `python` 프로세스 검색)하여 잔여 프로세스를 중지합니다.
1. 잔여 프로세스를 찾거나 중지할 수 없는 경우 **컴퓨터를 재부팅**하세요. 포트를 점유하고 있는 잔여 엔진이 확실하게 정리됩니다.
1. Griptape Nodes를 다시 시작합니다. 정상적인 상태에서는 단일 엔진 프로세스만 실행되며 미디어가 정상적으로 표시됩니다.

!!! tip

    재부팅 후 워크플로우를 다시 열기 전에 엔진 프로세스가 하나만 있는지 확인하세요. 이전 세션(특히 업데이트 후)의 잔여 엔진 하나가 이 문제의 가장 일반적인 원인입니다.

!!! note "원격 머신에서 엔진을 실행 중인가요?"

    엔진이 에디터와 다른 머신에서 실행 중이거나(터널 또는 역방향 프록시 뒤에 있는 경우), 에디터가 올바른 주소를 가리킬 때까지 미디어가 표시되지 않는 것은 정상입니다. [정적 파일 서버 구성](guides/configuration.md#static-file-server-configuration)에 설명된 대로 `static_server_base_url`을 설정하세요.

## 가져온 이미지나 비디오가 0바이트로 표시됨

**증상**

- 가져온 이미지와 비디오가 `{inputs}/images/` 또는 `{inputs}/videos/`에 **0바이트**(파일 탐색기에서 `0 KB`)로 저장됩니다.
- 에디터에 미디어가 표시되지 않으며 워크플로우를 다시 열어도 미디어가 나타나지 않습니다.
- Griptape Nodes를 종료하고 다시 열어도 해결되지 않습니다.

**원인**

파일을 프로젝트로 가져오는 작업은 두 단계로 진행됩니다. 엔진이 먼저 빈 파일을 생성하여 대상 파일 이름을 예약한 다음, 에디터가 포트 `8124`에서 실행 중인 로컬 정적 파일 서버로 파일 내용을 전송합니다. 두 번째 단계가 완료되지 않으면 예약된 빈 파일만 남게 됩니다.

이 문제는 해당 머신의 브라우저 수준 결함으로 확인되었으며 Windows 10에서만 발견되었습니다. 애플리케이션을 다시 시작해도 해결되지 않습니다.

**해결 방법**

- 컴퓨터를 재부팅하세요. Griptape Nodes만 다시 시작하는 것으로는 해결되지 않습니다.

## "Address already in use" / 엔진이 시작되지 않음

**증상**

시작 시 다음과 유사한 오류가 표시됩니다:

```
The 'websocket_direct' driver could not start: its address is already in use.
Another Griptape Nodes engine is probably already running.
Stop the other engine (or change this driver's port) and try again.
```

**원인**

다른 Griptape Nodes 엔진이 이미 실행 중이며 이 엔진에 필요한 포트를 점유하고 있습니다.

**해결 방법**

1. 다른 엔진을 중지합니다. 다른 모든 Griptape Nodes 창을 닫고 [위의 미디어 섹션](#에디터에-이미지나-비디오가-표시되지-않음)의 단계에 따라 잔여 엔진 프로세스를 확인하여 종료합니다.
1. 의도적으로 한 머신에서 여러 엔진을 실행하려는 경우 [한 머신에서 여러 엔진 실행하기](#한-머신에서-여러-엔진-실행하기)를 참조하세요.

## 한 머신에서 여러 엔진 실행하기

**증상**

동일한 머신에서 두 개 이상의 엔진이 실행 중일 때 이상하고 관련 없어 보이는 오류가 다수 발생합니다. 엉뚱한 엔진이 요청에 응답하거나(또는 두 번 응답), 에디터 세션 간에 워크플로우와 상태가 뒤섞이거나, 에디터에서 엔진들이 하나의 엔진처럼 보이거나, [주소 충돌 오류](#address-already-in-use-엔진이-시작되지-않음) 같은 포트 오류가 발생하거나, [미디어 로드 실패](#에디터에-이미지나-비디오가-표시되지-않음) 현상이 나타납니다.

**원인**

두 가지 개별 문제가 복합적으로 작용합니다:

- **공유 ID (Shared identity)**: `GTN_ENGINE_ID`가 설정되지 않은 경우 머신에서 실행되는 모든 엔진이 동일한 기본 엔진 ID로 처리됩니다. 동일한 ID를 공유하는 엔진은 동일한 요청을 수신하고 세션 상태를 공유하므로 둘 다 특정 요청에 응답하려고 시도합니다. 이것이 수많은 기이한 오류를 유발합니다.
- **포트 충돌 (Port conflicts)**: 첫 번째 엔진이 기본 포트(정적 파일 서버용 `8124` 등)를 점유하고, 이후 엔진은 다른 포트로 대체 실행되어 기본 포트를 가리키는 모든 연결이 중단됩니다.

**해결 방법**

추가 엔진마다 **고유한 ID와 고유한 포트**를 부여하세요:

```bash
GTN_ENGINE_ID=second-engine STATIC_SERVER_PORT=9000 GTN_MCP_SERVER_PORT=9928 gtn engine
```

여러 엔진을 실행할 의도가 없었다면 [미디어 섹션](#에디터에-이미지나-비디오가-표시되지-않음)의 단계에 따라 불필요한 엔진을 찾아 종료하세요.

## "No sessions available" — 라이선스 사용자의 엔진 시작 실패

**증상**

Griptape Cloud 로그인 대신 라이선스로 활성화한 상태에서 실행 시 엔진이 `No sessions available`과 같은 오류와 함께 라이선스 할당에 실패합니다.

**원인**

조직에 고정된 수의 라이선스 세션(좌석, seats) 풀이 있습니다. 좌석은 엔진이 실행되는 동안 점유되며 엔진이 정상 종료될 때 해제됩니다. `No sessions available`은 풀의 모든 좌석이 현재 점유 중임을 의미합니다. 정상적인 사용(모든 사용자가 사용 중)이거나 **비정상 잔여 세션(stale session)** 때문일 수 있습니다. 충돌이 발생했거나 강제 종료되었거나 백그라운드에서 분리되어 계속 실행 중인 엔진이 좌석을 계속 점유(및 갱신)하고 있는 상태입니다.

**해결 방법**

1. [미디어 섹션](#에디터에-이미지나-비디오가-표시되지-않음)의 단계를 참조하여 충돌이나 강제 종료 후 머신에 남아 있는 분리된 엔진이 있는지 확인하고 종료하여 좌석을 해제합니다.
1. 좌석이 해제되지 않는 경우 조직 소유자가 해제할 수 있습니다: [Admin Dashboard](enterprise/admin_dashboard.md#세션-sessions)에서 **Sessions** 모달을 열고 비정상 세션을 **Release**하여 좌석을 확보합니다.
1. 만료될 때까지 기다릴 수도 있습니다. 갱신이 중단되면 세션이 타임아웃되어 자동으로 해제되므로 몇 분 후 다시 시도해도 해결됩니다.

!!! note

    유사한 오류인 `No session pool configured`는 조직에 라이선스 세션 풀이 설정되어 있지 않음을 의미합니다. Griptape Nodes 라이선스 관리자에게 문의하세요.

## 에디터 화면이 검게 변하거나 아무것도 표시되지 않음

**증상**

컴퓨터가 유휴 상태였거나 절전 모드에서 깨어난 후, 또는 일시적인 네트워크 중단 후 에디터 창이 검게 변하거나 빈 화면이 됩니다.

**해결 방법**

- Ctrl+Shift+R (Windows/Linux) 또는 Cmd+Shift+R (macOS)을 눌러 에디터를 강력 새로고침합니다. 에디터를 다시 로드하고 엔진에 다시 연결합니다.

## 라이브러리 또는 노드가 누락되거나 다른 엔진의 오류가 표시됨

**증상**

- 라이브러리가 표시되지 않거나 예상했던 노드(예: Agent 노드)가 사라집니다.
- 에디터에 현재 보고 있는 것과 다른 엔진이나 워크플로우를 참조하는 오류가 표시됩니다.

**원인**

대개 다음 두 가지 중 하나입니다:

- **라이브러리 로드 실패**: 종속성 누락, 손상된 노드 파일, 임포트 오류 등으로 인해 라이브러리 로드에 실패하면 해당 노드가 표시되지 않습니다. **로그가 가장 확실한 확인 수단입니다.** 엔진 로그를 내보내거나 열어 시작 시 라이브러리 로드 관련 오류가 있는지 확인하세요.
- **Libraries To Register 설정 불일치**: 엔진은 **Libraries To Register** 설정(**Configuration Editor → Libraries → Library Registration**, `griptape_nodes_config.json`의 `app_events.on_app_initialization_complete.libraries_to_register`에 저장됨)에 나열된 라이브러리만 로드합니다. 라이브러리가 해당 목록에 없거나 비활성화되어 있거나 항목이 유효하지 않으면 노드가 나타나지 않습니다.
- 생각했던 것과 다른 엔진에 연결되어 해당 엔진의 라이브러리와 오류가 표시되는 경우일 수도 있습니다.

**해결 방법**

1. **로그를 먼저 확인하세요.** 시작 시 라이브러리가 로드되는 동안 발생한 오류를 찾습니다. 보고된 오류에는 대개 라이브러리 이름과 실패 원인이 명시됩니다. [엔진 로그 내보내기](#엔진-로그-내보내기)를 참조하세요.
1. 에디터가 연결된 엔진을 확인합니다. 여러 머신에 엔진이 있는 경우 에디터가 잘못된 엔진에 연결되었을 수 있습니다.
1. **Configuration Editor**를 열고 **Libraries** 뷰로 이동하여 **Library Registration → Libraries To Register**를 확인합니다. 원하는 라이브러리가 누락되었거나 꺼져 있거나 잘못된 경로를 가리키고 있다면 항목을 수정하거나 **Manage → Library Management → Add Library**를 통해 다시 추가하세요. [라이브러리 전환 및 제거](guides/libraries.md#toggling-and-removing-libraries) 및 [라이브러리 설치](guides/editor/managing_models_and_libraries.md#installing-a-library)를 참조하세요.
1. 설치 또는 로드에 실패한 라이브러리가 있는지 필터를 **Errors**로 설정한 상태에서 **Libraries** 패널을 확인합니다. ["라이브러리를 설치했지만 노드가 보이지 않음"](guides/libraries.md#i-installed-the-library-but-i-dont-see-its-nodes)을 참조하세요.
1. 라이브러리가 최신 상태인지 확인합니다. **Manage → Library Management**를 열고 라이브러리를 펼친 후 **Check for Updates**를 클릭하고 업데이트가 제공되면 **Update**를 클릭합니다. [라이브러리 업데이트](guides/editor/managing_models_and_libraries.md#updating-a-library)를 참조하세요. 엔진 자체를 업데이트하려면 [FAQ](faq.md#griptape-nodes를-업데이트하려면-어떻게-해야-하나요)를 참조하세요.

## "failed to locate pyvenv.cfg" / 엔진이 시작되지 않음

**증상**

실행 시 엔진이 다음과 같은 오류와 함께 시작되지 않습니다:

```
failed to locate pyvenv.cfg: The system cannot find the file specified.
```

**원인**

이전 제거 작업이 완전히 완료되지 않아 Griptape Nodes의 가상 환경이 손상된 상태로 남아 있습니다.

**해결 방법**

1. 손상된 설치 상태를 정리하기 위해 Griptape Nodes를 다시 제거합니다:

    ```bash
    griptape-nodes self uninstall
    ```

    `griptape-nodes` 명령어도 동일한 환경에서 실행되므로 손상된 가상 환경으로 인해 제거 명령어 실행 자체가 차단될 수도 있습니다. 제거 시도 시 동일한 오류가 발생하면 [Griptape Nodes 제거](uninstalling.md#수동-엔진-설치)의 단계를 따라 수동으로 설치 파일을 삭제하세요.

1. [설치](installation.md) 지침에 따라 다시 설치합니다.

## "Attempted to create a Flow with a parent 'None'" / 대부분 무해함

**증상**

워크플로우를 로드하거나 빌드하는 중에 다음과 같은 오류가 표시됩니다:

```
Attempted to create a Flow with a parent 'None', but no parent with that name could be found.
```

**원인**

알려진 간헐적 버그입니다. 거의 모든 경우에 무해하며 작업에 영향을 주지 않습니다.

**해결 방법**

1. 일반적으로 무시하고 작업을 계속 진행할 수 있습니다.
1. 문제가 지속되면 엔진을 다시 시작하면 해결됩니다.
1. 재현이 가능한 경우 발생 정황과 함께 [버그를 등록](https://github.com/griptape-ai/griptape-nodes/issues/new?template=bug_report.yml&title=Attempted%20to%20create%20flow%20with%20a%20parent%20%27None%27)해 주시면 감사하겠습니다.

## "ssl.SSLCertVerificationError" / 엔진이 실행되지 않음

**증상**

Griptape Nodes 실행 시 다음과 같은 오류가 표시됩니다:

```
ssl.SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: self-signed certificate in certificate chain (_ssl.c:1000)
```

**원인**

머신의 Python 설치 환경에서 검증된 SSL 인증서에 접근할 수 없습니다.

**해결 방법**

1. [python.org](https://www.python.org/downloads/) 설치 프로그램을 사용하여 Python을 다시 설치합니다. Griptape Nodes는 Python 3.12가 필요합니다.
1. 설치 마지막 단계에서 **Install Certificates**를 선택합니다.
    - 설치 프로그램에서 제공하지 않는 경우 `/Applications/Python\ 3.12/Install\ Certificates.command`를 실행합니다.

## 엔진 로그 내보내기

이슈를 보고하거나 직접 디버깅할 때 엔진 로그는 가장 먼저 확인해야 하는 항목입니다. 로그를 가져오는 위치는 엔진 실행 방식에 따라 다릅니다.

### 데스크톱 애플리케이션에서 가져오기

데스크톱 애플리케이션은 관리하는 로컬 엔진에 대한 자체 로그 파일을 유지하며 현재 세션뿐만 아니라 특정 시간 범위의 로그도 내보낼 수 있습니다. 문제가 발생한 지 시간이 지났거나 엔진 재시작 전후에 걸쳐 있는 경우 특히 유용합니다.

1. 헤더에서 **Engine**(엔진 상태를 표시하는 버튼)을 클릭하여 엔진 팝오버를 엽니다.
1. **Managed Engine** 아래에서 **Logs**를 클릭하여 엔진 로그 창을 엽니다.
1. **Export**를 클릭합니다.
1. **Export Logs** 대화상자에서 다음 중 하나를 선택합니다:
    - **Current Engine Session** — 엔진이 마지막으로 시작된 이후의 로그.
    - **Time Range** — 특정 타임스탬프 사이의 로그. **From** 시간과 함께 **To** 시간 또는 **Now** 체크박스를 지정합니다. 방금 문제가 발생한 경우 전체 세션보다 최근 30분 정도를 내보내는 것이 더 유용합니다.
1. `.txt` 파일을 저장할 위치를 선택합니다.

!!! note

    내보내기를 사용하려면 데스크톱 애플리케이션의 [앱 설정](guides/desktop/app_settings.md#logging-and-diagnostics)에 있는 **Write engine logs to file** 설정이 필요합니다. 기본적으로 활성화되어 있으며, **Export** 버튼이 비활성화되어 있는 경우 옆의 **Manage** 링크를 클릭하여 해당 설정으로 이동할 수 있습니다.

### 터미널에서 가져오기

수동으로 엔진을 실행하는 경우(`gtn` 또는 `gtn engine`) 로그가 해당 터미널에 직접 출력됩니다. 위로 스크롤하여 관련 부분을 복사하세요.

로그에 충분한 세부 정보가 표시되지 않으면 엔진 로그 레벨을 높이세요: Configuration Editor(**Settings → All Settings**)를 열고 "log level"을 검색하여 `DEBUG`로 설정한 후 문제를 재현합니다([에디터에서 설정 편집](guides/configuration.md#editing-settings-in-the-editor) 참조). 에디터가 연결되지 않은 헤드리스 모드로 실행할 때는 환경 변수로 설정할 수 있습니다:

```bash
GTN_CONFIG_LOG_LEVEL=DEBUG gtn
```
