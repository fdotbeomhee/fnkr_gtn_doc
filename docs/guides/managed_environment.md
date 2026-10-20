# 관리되는 환경에서 실행하기 (Running in a Managed Environment) {: #running-in-a-managed-environment }

보통 엔진은 라이브러리를 직접 관리합니다. 라이브러리를 다운로드하고, 라이브러리마다 별도의 Python 환경을 만들고, 각 라이브러리에 필요한 패키지를 설치합니다. 일부 스튜디오는 다른 애플리케이션과 같은 도구(Rez나 Conda 같은 패키지 관리자, 컨테이너 이미지, 사내 런처)로 이 작업을 직접 수행하기를 원합니다. 이 페이지는 그런 환경을 설정하는 담당자를 위한 문서입니다. 아티스트라면 마지막의 [아티스트가 보는 것](#what-artists-see)에서 간단한 요약을 확인하세요.

관리되는 환경에서는 다음과 같이 동작합니다:

- 환경이 어떤 라이브러리를 어떤 버전으로 로드할지 결정합니다.
- 엔진은 라이브러리를 다운로드, 업데이트, 설치하지 않으며 Python 환경도 만들지 않습니다.
- 각 라이브러리의 워커 프로세스는 해당 라이브러리가 필요로 하는 환경 안에서 시작할 수 있습니다.

## 세 가지 설정 (The three settings) {: #the-three-settings }

| 항목 | 설정 방법 | 목적 |
| --- | --- | --- |
| `GTN_LIBRARY_PATHS` | 환경 변수 | 환경이 제공하는 라이브러리입니다. |
| `library.provisioned_by` | 설정, 또는 `GTN_CONFIG_LIBRARY__PROVISIONED_BY=environment` | 환경이 라이브러리와 해당 패키지를 제공한다고 엔진에 알립니다. |
| `worker.command_prefix` | 설정, 또는 `GTN_CONFIG_WORKER__COMMAND_PREFIX`(JSON 리스트) | 각 라이브러리의 워커를 해당 라이브러리의 환경 안에서 시작합니다. |

### `GTN_LIBRARY_PATHS`

라이브러리 매니페스트(`griptape_nodes_library.json` 파일) 또는 이를 찾을 폴더의 목록입니다. 항목은 시스템의 `PATH` 항목을 구분하는 방식(macOS와 Linux에서는 `:`, Windows에서는 `;`)으로 구분합니다:

```bash
export GTN_LIBRARY_PATHS="/studio/libs/lib_foo/griptape_nodes_library.json:/studio/libs/lib_bar"
```

각 항목은 `libraries_to_register`의 항목과 같이 처리되며, 이 라이브러리들은 `libraries_to_register`에 있는 라이브러리보다 먼저 로드됩니다. 상대 경로는 작업 공간을 기준으로 읽습니다. 이 설정은 어떤 방식으로 라이브러리를 제공하든 동작하며, `environment`로 설정했을 때만 로드를 이 목록으로 제한합니다. 엔진이 시작되는 환경에서 설정하세요. 프로젝트의 환경 설정으로는 이 값이 바뀌지 않습니다.

### `library.provisioned_by`

기본값인 `engine`은 엔진의 일반적인 동작입니다. 즉, 엔진이 라이브러리를 직접 준비합니다. 모든 라이브러리와 패키지를 이미 갖춘 환경 안에서 엔진을 시작할 때는 `environment`로 설정하세요:

```bash
export GTN_CONFIG_LIBRARY__PROVISIONED_BY=environment
```

`engine` 또는 `environment`가 아닌 다른 값은 `environment`로 취급되며 오류로 기록됩니다. 따라서 잘못 입력한 값 때문에 다운로드, 빌드, 설치가 실행되는 일은 없습니다. 엔진의 기본 준비 동작을 되돌리려면 값을 바르게 고치세요.

`environment`로 설정하면 다음과 같이 동작합니다:

- `GTN_LIBRARY_PATHS`에 있는 라이브러리만 로드됩니다. 구성된 다른 라이브러리(`libraries_to_register`의 항목과 Sandbox Library)는 "This library was not loaded because the engine is running in an environment that provides its libraries, and this library is not one of them."라는 문제와 함께 목록에 표시됩니다.
- `libraries_to_download`는 무시되며, 다운로드, 업데이트, 동기화는 전혀 일어나지 않습니다. 라이브러리를 다운로드, 업데이트, 전환, 동기화하라는 요청은 환경이 라이브러리를 관리한다는 메시지와 함께 실패합니다.
- 라이브러리의 업데이트를 확인하면 업데이트가 없다고 보고하며, 업데이트는 환경에서 온다고 알려줍니다. 이때 git 원격 저장소에는 접속하지 않습니다.
- Sandbox Library는 스캔되거나 로드되지 않으며, 파일에서 샌드박스 노드를 추가하면 환경이 라이브러리를 관리한다는 메시지와 함께 실패합니다. 단, 환경이 샌드박스를 허용하는 경우는 예외입니다([`library.sandbox_enabled`](#librarysandbox_enabled) 참조).
- `.venv` 또는 `.venv-exec` 폴더가 만들어지지 않으며, 이전 실행에서 남은 폴더도 사용하지 않습니다.
- 다른 라이브러리를 의존성으로 선언한 라이브러리는 `GTN_LIBRARY_PATHS`에 있는 라이브러리로만 충족됩니다. 환경이 해당 의존성을 제공하지 않으면, 라이브러리는 의존성을 다운로드하는 대신 누락된 의존성을 보고합니다. 엔진은 의존성의 저장소 이름을 각 라이브러리 경로의 폴더 이름과 비교합니다. 이때 대소문자를 무시하고 `-`와 `_`를 같은 것으로 취급합니다. 따라서 저장소 이름과 같은 폴더를 경로에 두세요. 예를 들어 `griptape-nodes-library-openexr` 저장소의 경우 `.../griptape_nodes_library_openexr/griptape_nodes_library.json`입니다.
- 아티스트의 설정 파일은 변경되지 않습니다. 로드되지 않은 라이브러리의 항목도 제거되지 않습니다.

### `library.sandbox_enabled`

일부 스튜디오는 아티스트가 자신만의 노드를 개발하도록 허용하면서도 나머지는 환경이 관리하기를 원합니다. `library.sandbox_enabled`(또는 `GTN_CONFIG_LIBRARY__SANDBOX_ENABLED`)는 Sandbox Library(**Settings → Library → Sandbox Settings**의 폴더)를 켜거나 끕니다:

| 값 | 엔진이 라이브러리를 준비할 때 | 환경 모드에서 |
| --- | --- | --- |
| 설정하지 않음(기본값) | 켜짐 | 꺼짐 |
| `true` | 켜짐 | 켜짐 |
| `false` | 꺼짐 | 꺼짐 |

아티스트가 환경 모드에서 샌드박스를 사용하도록 하려면 다음과 같이 설정합니다:

```bash
export GTN_CONFIG_LIBRARY__PROVISIONED_BY=environment
export GTN_CONFIG_LIBRARY__SANDBOX_ENABLED=true
```

이렇게 설정하면 다음과 같이 동작합니다:

- Sandbox Library를 스캔하고 로드하며, 샌드박스 노드를 추가할 수 있습니다.
- 샌드박스용 가상 환경은 만들어지지 않습니다. 샌드박스 노드가 가져오는(import) 모든 항목은 환경에 이미 있어야 하며, 누락된 항목을 가져오는 노드는 가져오기 오류를 보고합니다.
- 허용되는 것은 샌드박스뿐입니다. 환경이 목록에 넣지 않은 다른 라이브러리는 여전히 거부됩니다.

샌드박스를 끄면 Sandbox Library는 스캔되거나 로드되지 않으며, 샌드박스 노드를 추가하려고 하면 그 이유를 알려주는 메시지와 함께 실패합니다. 이 설정은 어떤 대소문자로든 `true` 또는 `false`를 받아들입니다. 환경 변수에서 다른 값이 들어오면 엔진 로그에 보고되고 무시되므로 기본값이 적용됩니다.

`ReloadSandboxLibraryRequest`는 Sandbox Library만 다시 로드하므로, 엔진을 다시 시작하지 않아도 새 노드 파일이나 변경된 노드 파일이 나타납니다. 다른 라이브러리는 로드된 상태를 유지하고 워커도 계속 실행됩니다. 에디터에서는 **Refresh Sandbox**가 이 요청을 보냅니다([griptape-vsl-gui#3130](https://github.com/griptape-ai/griptape-vsl-gui/pull/3130) 참조). 워크플로우에 이미 있는 노드는 노드를 다시 만들기 전까지 생성 당시의 버전을 유지합니다. 노드 파일 하나의 오타 등으로 다시 로드가 중간에 실패하면, 다시 로드가 성공할 때까지 샌드박스는 로드되지 않은 상태로 남습니다. 샌드박스 다시 로드와 모든 라이브러리 다시 로드는 동시에 실행되지 않습니다. 나중에 시작된 작업은 먼저 시작된 작업이 끝날 때까지 기다립니다. 샌드박스가 꺼져 있으면 이 요청은 그 이유를 알려주는 메시지와 함께 실패합니다.

### `worker.command_prefix`

자체 워커 프로세스에서 실행되는 라이브러리는 엔진 자체의 Python 인터프리터를 전체 경로로 지정하여 시작됩니다. 예: `/path/to/python -m griptape_nodes_app engine --library-name "Foo Library" ...`. `worker.command_prefix`는 이 명령 앞에 놓이는 단어 목록입니다. 따라서 도구가 먼저 환경을 준비한 다음 그 안에서 워커를 실행할 수 있습니다:

```bash
export GTN_CONFIG_WORKER__COMMAND_PREFIX='["env-tool", "run", "engine=={engine_version}", "python-{python_version}", "{library_request}", "--"]'
```

워커마다 다음 자리표시자가 채워집니다:

| 자리표시자 | 채워지는 값 |
| --- | --- |
| `{library_request}` | `GTN_LIBRARY_WORKER_REQUESTS`에 있는 라이브러리의 항목(아래 참조). |
| `{library_name}` | 매니페스트에 적힌 라이브러리 이름. |
| `{engine_version}` | 실행 중인 엔진의 버전(예: `0.103.0`). |
| `{python_version}` | 엔진이 실행되는 Python 버전(메이저.마이너 형식, 예: `3.12`). |

단어가 정확히 `{library_request}`인 경우, 항목을 공백으로 나눈 각 부분이 하나의 단어가 됩니다. 따라서 한 항목에 여러 패키지를 지정할 수 있습니다. 더 긴 단어 안에 있는 경우에는 텍스트로 치환됩니다.

접두사로 워커를 실행하는 Python 자체는 바꿀 수 없습니다. 항상 엔진 자체의 인터프리터가 사용됩니다. 각 워커의 환경은 그 Python을 위해 준비하세요. 예를 들어 도구에 `python-{python_version}`을 요청하면 됩니다. 그렇지 않으면 컴파일된 부분이 있는 패키지가 다른 Python 버전용으로 빌드되어 가져오기에 실패할 수 있습니다. 엔진 자체는 가상 환경이 아니라 준비된 환경에서 시작하세요. 그래야 워커가 그 가상 환경의 패키지를 가져오지 않습니다.

`GTN_LIBRARY_WORKER_REQUESTS`는 각 라이브러리의 워커에 필요한 항목을 지정합니다. 항목은 `<library name>=<request>` 형식이며, `PATH` 항목처럼 구분합니다. 여기서 라이브러리 이름은 라이브러리 매니페스트의 `name`입니다:

```bash
export GTN_LIBRARY_WORKER_REQUESTS="Foo Library=lib_foo==1.4.2:Bar Library=lib_bar==2.0.1"
```

항목을 `PATH` 항목처럼 구분하므로, macOS와 Linux에서는 요청에 `:`를, Windows에서는 `;`를 포함할 수 없습니다. `GTN_LIBRARY_PATHS`와 마찬가지로, 엔진이 시작되는 환경에서 설정하세요. 프로젝트의 환경 설정으로는 이 값이 바뀌지 않습니다.

접두사가 `{library_request}`를 사용하는데 라이브러리에 항목이 없는 경우:

- `library.provisioned_by`가 `environment`로 설정되어 있으면 해당 라이브러리의 워커는 시작되지 않습니다. 노드는 편집할 수 있지만, 실행하려고 하면 환경이 워커에 필요한 패키지를 알려주지 않는다는 메시지가 표시됩니다. 접두사 없이 워커를 시작하지 않습니다. 엔진 자체의 환경은 그 워커를 위해 준비되지 않았기 때문입니다.
- `engine`으로 설정되어 있으면, 접두사가 설정되지 않은 것처럼 접두사 없이 워커를 시작합니다.

접두사가 설정되어 있지만 사용할 수 없는 경우(예: JSON 단어 목록이 아닌 `GTN_CONFIG_WORKER__COMMAND_PREFIX`)에는 엔진 로그에 이유가 보고됩니다. `library.provisioned_by`가 `environment`로 설정되어 있으면, 접두사 없이 시작하지 않고 모든 워커를 이 이유와 함께 거부합니다.

워커는 엔진이 시작될 때의 환경 변수를 그대로 받으며, 엔진이 이름으로 설정하는 몇 가지 변수(`GTN_ENGINE_ID`, `GTN_ORCHESTRATOR_ENGINE_ID`, `PYTHONUNBUFFERED`, 정적 파일 서버 주소)가 추가됩니다. 엔진은 다른 변수를 복사하지 않으며, 도구의 변수도 바꾸지 않습니다. 따라서 워커의 환경에 무엇이 들어가는지는 도구가 결정합니다.

Windows 머신에서 이 접두사를 사용한다면 인수에 따옴표를 넣지 마세요. 일부 Windows 셸은 인수 안의 큰따옴표를 제거합니다.

## 예제 런처 (Example launcher) {: #example-launcher }

런처는 변수를 설정하고, 준비된 환경 안에서 엔진을 시작하기만 하면 됩니다:

```bash
#!/bin/sh
export GTN_LIBRARY_PATHS="/studio/libs/lib_foo/griptape_nodes_library.json:/studio/libs/lib_bar/griptape_nodes_library.json"
export GTN_LIBRARY_WORKER_REQUESTS="Foo Library=lib_foo==1.4.2:Bar Library=lib_bar==2.0.1"
export GTN_CONFIG_LIBRARY__PROVISIONED_BY=environment
export GTN_CONFIG_WORKER__COMMAND_PREFIX='["env-tool", "run", "engine=={engine_version}", "{library_request}", "--"]'
exec gtn
```

대부분의 패키지 관리자는 이 변수를 대신 설정할 수 있습니다. 각 라이브러리의 패키지가 `GTN_LIBRARY_PATHS`와 `GTN_LIBRARY_WORKER_REQUESTS`에 자신을 추가하고, 런치 패키지가 `library.provisioned_by`와 `worker.command_prefix`를 설정합니다.

## 아티스트가 보는 것 (What artists see) {: #what-artists-see }

- 에디터에 보이는 라이브러리는 스튜디오 환경이 제공하는 라이브러리입니다. **Settings → Library**에서 직접 추가한 라이브러리는 여전히 표시되지만, 로드되지 않았다고 표시되며 환경이 제공하지 않는다는 메모가 함께 나타납니다. 스튜디오 설정을 관리하는 담당자에게 해당 라이브러리를 추가해 달라고 요청하세요.
- 에디터에서 라이브러리를 설치, 업데이트, 또는 버전 전환하는 작업은 동작하지 않습니다. 이러한 변경은 스튜디오 환경에서 이루어집니다.
- Sandbox Library는 스튜디오가 허용하는 경우에만 동작합니다. 허용되면 새로 고침은 샌드박스만 다시 로드합니다. 스튜디오 라이브러리의 변경 사항을 반영하려면 앱을 다시 실행해야 합니다.
- 노드를 실행할 때 환경이 워커에 필요한 패키지를 알려주지 않아 워커를 시작할 수 없다는 메시지가 나타나면, 해당 라이브러리의 항목이 환경에 없는 것입니다. 노드를 편집하고 워크플로우를 저장하는 작업은 계속 가능합니다.

모든 설정과 해당 환경 변수는 [설정 레퍼런스](../reference/configuration_reference.md)에서 확인할 수 있습니다.
