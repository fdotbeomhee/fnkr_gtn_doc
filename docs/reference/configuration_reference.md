# 설정 레퍼런스 (Configuration Reference) {: #configuration-reference }

카테고리별로 분류된 모든 Griptape Nodes 엔진 설정 항목입니다. 각 설정은 모든 `griptape_nodes_config.json` 파일에 지정할 수 있습니다(로드 순서는 [엔진 구성](../guides/configuration.md) 참조). 중첩된 설정은 전체 점(.) 구분 키로 나열합니다. 이 형식은 `griptape-nodes config show`가 받는 형식이며 아래의 환경 변수 이름도 이 형식으로 만들어집니다. 설정 파일에서는 키의 각 부분을 JSON 객체로 중첩해야 합니다. 예를 들어 `worker.heartbeat_timeout_s`는 `{"worker": {"heartbeat_timeout_s": 30}}`입니다. `"worker.heartbeat_timeout_s"`처럼 평평한 키는 무시됩니다. `GTN_CONFIG_*` 환경 변수가 있는 설정은 환경 변수를 통해 재정의할 수도 있습니다. `GTN_CONFIG_<PATH>` 형식에서는 점 구분 키의 각 부분 사이에 `__`를 사용합니다. 리스트 값 설정은 설정 파일에서 직접 편집해야 하며, 환경 변수가 JSON 리스트를 받도록 표시된 경우에만 예외입니다. 매핑 키는 대소문자를 구분하여 일치하지만 전체 변수 이름은 소문자로 변환되므로 이미 소문자인 키만 환경 변수에서 접근할 수 있습니다(자세한 내용은 가이드 참조).

## 파일 시스템 (File System) {: #file-system }

애플리케이션의 디렉토리 및 파일 경로 설정

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `workspace_directory` | string | `<현재_작업_디렉토리>/GriptapeNodes` | `GTN_CONFIG_WORKSPACE_DIRECTORY` | 프로젝트, 워크플로우 및 생성된 에셋의 루트 디렉토리입니다. 기본값은 현재 작업 디렉토리 아래의 GriptapeNodes 폴더입니다. 다른 파일 시스템 경로(libraries_directory, static_files_directory, sandbox_library_directory, synced_workflows_directory)는 절대 경로로 설정되지 않은 한 이 디렉토리를 기준으로 상대적으로 해석됩니다. |
| `static_files_directory` | string | `"staticfiles"` | `GTN_CONFIG_STATIC_FILES_DIRECTORY` | 워크스페이스 디렉토리를 기준으로 한 정적 파일 디렉토리의 상대 경로입니다. |
| `sandbox_library_directory` | string | `"sandbox_library"` | `GTN_CONFIG_SANDBOX_LIBRARY_DIRECTORY` | 샌드박스 라이브러리 디렉토리의 경로입니다(노드 개발 시 유용). 상대 경로는 워크스페이스 디렉토리를 기준으로 해석되며 절대 경로는 그대로 사용됩니다. `~`와 경로 뒤쪽의 환경 변수는 확장됩니다. `$`로 시작하는 값은 대신 비밀 이름으로 조회됩니다. |
| `libraries_directory` | string | `"libraries"` | `GTN_CONFIG_LIBRARIES_DIRECTORY` | `libraries_to_download`에서 설치된 라이브러리가 설치되는 디렉토리의 경로입니다. 이곳에 설치된 라이브러리는 자동으로 로드됩니다. 다른 항목은 `libraries_to_register`(에디터의 **Add Library**)에 경로를 추가하기 전까지 로드되지 않습니다. 상대 경로는 워크스페이스 디렉토리를 기준으로 해석되며 절대 경로는 그대로 사용됩니다. 프로젝트는 프로젝트 템플릿의 `libraries_dir` 필드(상위 프로젝트 체인을 따라 상속 가능)로 이 위치를 재정의할 수 있습니다. 이 필드는 이 값보다 우선하므로 하위 프로젝트가 상위 프로젝트의 라이브러리 설치 위치를 공유할 수 있습니다. |
| `ffmpeg_directory` | string | `""` | `GTN_CONFIG_FFMPEG_DIRECTORY` | 엔진이 처음 사용할 때 다운로드하는 ffmpeg/ffprobe 바이너리를 보관하는 디렉토리의 절대 경로입니다. 다른 디렉토리 설정과 달리 워크스페이스를 기준으로 해석되지 않습니다. ffmpeg 캐시는 워크스페이스가 아니라 머신에 속하므로 모든 워크스페이스와 프로젝트에서 공유됩니다. 상대 경로는 경고와 함께 무시됩니다. 빈 값(기본값)은 엔진 데이터 디렉토리의 `ffmpeg` 폴더를 의미합니다. 이 폴더는 `<XDG_DATA_HOME>/griptape_nodes`이며, `GTN_ENGINE_DATA_DIR`이 설정된 경우 해당 경로입니다. 다운로드 대신 자체 바이너리를 제공하려면 ffmpeg, ffprobe 및 빈 `installed.crumb` 파일이 포함된 `bin/<platform>/` 디렉토리를 지정하세요. |
| `synced_workflows_directory` | string | `"synced_workflows"` | `GTN_CONFIG_SYNCED_WORKFLOWS_DIRECTORY` | 워크스페이스 디렉토리를 기준으로 한 동기화된 워크플로우 디렉토리의 상대 경로입니다. |
| `enable_workspace_file_watching` | boolean | `true` | `GTN_CONFIG_ENABLE_WORKSPACE_FILE_WATCHING` | 동기화된 워크플로우 디렉토리에 대한 파일 감시(file watching) 활성화 여부 |

## 애플리케이션 이벤트 (Application Events) {: #application-events }

애플리케이션 수명 주기 이벤트 구성

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `app_events` | object | (중첩 객체) | `GTN_CONFIG_APP_EVENTS__ON_APP_INITIALIZATION_COMPLETE__REQUIRES_ENGINE` | 중첩 설정입니다. 나열된 하위 키는 환경 변수에서 설정할 수 있으며 모든 하위 키는 설정 파일에서 직접 편집할 수 있습니다. |

## 실행 (Execution) {: #execution }

워크플로우 실행 및 처리 설정

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `log_level` | `CRITICAL`, `ERROR`, `WARNING`, `INFO`, `DEBUG` 중 하나 | `"INFO"` | `GTN_CONFIG_LOG_LEVEL` | 엔진의 로깅 상세도입니다. 가장 낮은 수준부터 높은 수준 순으로 CRITICAL, ERROR, WARNING, INFO, DEBUG 중 하나입니다. |
| `workflow_execution_mode` | `sequential`, `parallel` 중 하나 | `"sequential"` | `GTN_CONFIG_WORKFLOW_EXECUTION_MODE` | 노드 처리를 위한 워크플로우 실행 모드입니다. SEQUENTIAL 모드는 max_nodes_in_parallel=1을 사용하여 노드를 한 번에 하나씩 실행합니다. PARALLEL 모드는 설정된 max_nodes_in_parallel 값을 사용합니다. |
| `max_nodes_in_parallel` | integer | `5` | `GTN_CONFIG_MAX_NODES_IN_PARALLEL` | 병렬 실행 시 한 번에 실행되는 최대 노드 수입니다. |
| `worker.heartbeat_interval_s` | number | `5.0` | `GTN_CONFIG_WORKER__HEARTBEAT_INTERVAL_S` | 오케스트레이터가 보내는 워커 하트비트 확인 요청 사이의 간격(초)입니다. |
| `worker.heartbeat_timeout_s` | number | `15.0` | `GTN_CONFIG_WORKER__HEARTBEAT_TIMEOUT_S` | 워커가 하트비트에 응답하지 않은 채 이 시간(초)이 지나면 워커를 제거합니다. 워커는 오케스트레이터의 신호가 이 시간 동안 없으면 자체 종료하지만, 최소 30초 이후에만 종료합니다. 이는 확인 요청을 보내지 못할 만큼 바쁜 오케스트레이터를 종료된 것으로 잘못 판단하지 않기 위해서입니다. |
| `worker.library_load_timeout_s` | number | `600.0` | `GTN_CONFIG_WORKER__LIBRARY_LOAD_TIMEOUT_S` | 워커가 라이브러리를 로드하는 데 허용되는 시간(초)입니다. 노드를 실행할 때 라이브러리 워커의 로드 완료를 기다리는 시간과 프로젝트 전환 시 각 워커가 새 프로젝트를 적용하는 데 기다리는 시간을 제한합니다. 대형 라이브러리(예: torch, diffusers)를 처음 설치하는 경우 2분을 쉽게 넘을 수 있습니다. 하트비트에는 영향을 주지 않습니다. 하트비트는 `worker.heartbeat_timeout_s`를 참조하세요. |
| `worker.command_prefix` | array of string | `[]` | `GTN_CONFIG_WORKER__COMMAND_PREFIX` (JSON 리스트) | 라이브러리 워커 프로세스를 시작하는 명령 앞에 붙이는 단어 목록입니다. 다른 도구가 준비한 환경(예: 라이브러리 패키지를 해석하는 패키지 관리자)에서 워커를 실행할 수 있습니다. 기본값인 빈 목록이면 워커를 직접 시작합니다. 각 단어에는 `{library_request}`, `{library_name}`, `{engine_version}`, `{python_version}`을 포함할 수 있으며 워커마다 채워집니다. `{library_request}`는 `GTN_LIBRARY_WORKER_REQUESTS` 환경 변수에서 해당 라이브러리 항목이며, 정확히 `{library_request}`인 단어는 항목의 공백으로 구분된 부분마다 하나의 단어가 됩니다. `{library_name}`은 라이브러리 이름, `{engine_version}`은 이 엔진의 버전, `{python_version}`은 실행 중인 Python의 major.minor 버전입니다. 워커는 항상 이 엔진의 Python 인터프리터를 사용하므로, 접두사가 준비하는 환경은 해당 Python용이어야 합니다. 단어에 `{library_request}`가 있는데 라이브러리에 항목이 없으면 접두사 없이 워커를 시작합니다. 단, `library.provisioned_by`가 `environment`인 경우에는 워커를 시작하지 않고 라이브러리에 그 이유를 보고합니다. 환경 변수는 JSON 리스트를 받습니다. 예: `GTN_CONFIG_WORKER\_\_COMMAND_PREFIX='["env-tool", "run", "{library_request}", "--"]'` |

## 로깅 (Logging) {: #logging }

엔진 로그가 저장되는 위치와 보관 기간

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `logging.log_to_file` | boolean | `true` | `GTN_CONFIG_LOGGING__LOG_TO_FILE` | 콘솔과 함께 엔진 로그를 파일에도 기록합니다. 엔진 프로세스마다 파일을 하나씩 기록하며, 10 MB에 도달하면 교체하고 교체 파일 5개를 유지하므로 프로세스당 전체 크기가 제한됩니다. 현재 실행 중인 세션의 로그만 필요하다면 끄세요. |
| `logging.log_directory` | string | `""` | `GTN_CONFIG_LOGGING__LOG_DIRECTORY` | 엔진 로그 파일을 보관하는 디렉토리의 절대 경로입니다. `ffmpeg_directory`와 마찬가지로 워크스페이스 기준으로 해석되지 않습니다. 로그는 워크스페이스가 아니라 머신에 속하므로 모든 워크스페이스와 프로젝트가 하나의 위치를 공유합니다. 상대 경로는 경고와 함께 무시됩니다. 빈 값(기본값)이면 엔진 상태 디렉토리의 `logs` 폴더를 사용합니다. 이 폴더는 `<XDG_STATE_HOME>/griptape_nodes`이며, `GTN_ENGINE_STATE_DIR`이 설정된 경우 해당 경로입니다. |
| `logging.log_retention_days` | integer | `7` | `GTN_CONFIG_LOGGING__LOG_RETENTION_DAYS` | 이 일수 동안 기록되지 않은 엔진 로그 파일을 삭제합니다. 엔진이 시작할 때 확인하며, 로깅 설정이 바뀔 때마다 다시 확인합니다. 엔진이 현재 기록 중인 로그 파일은 아무리 오래되었어도 삭제되지 않습니다. 0으로 설정하면 로그 파일을 영구 보관합니다. |
| `logging.session_log_buffer_lines` | integer | `5000` | `GTN_CONFIG_LOGGING__SESSION_LOG_BUFFER_LINES` | 현재 세션에 대해 엔진이 메모리에 보관하는 최신 로그 줄 수입니다. 문제를 재현하지 않아도 문제 보고서에 방금 일어난 일이 포함됩니다. 이 줄에는 `log_level`이 허용하는 내용만 포함되므로, 보고서에 디버그 세부 정보가 필요하면 문제를 재현하기 전에 `log_level`을 `DEBUG`로 올리세요. 0으로 설정하면 비활성화되며, 이 경우 문제 보고서에는 로그 파일에 기록된 내용만 포함될 수 있습니다. |

## 스토리지 (Storage) {: #storage }

데이터 스토리지 및 지속성 구성

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `storage_backend` | `local`, `gtc` 중 하나 | `"local"` | `GTN_CONFIG_STORAGE_BACKEND` | 워크플로우 데이터 및 생성된 에셋을 유지하는 데 사용되는 백엔드입니다. 'local'은 워크스페이스 아래 로컬 파일 시스템에 파일을 저장하고 'gtc'는 Griptape Cloud 스토리지를 사용합니다. |
| `auto_inject_workflow_metadata` | boolean | `true` | `GTN_CONFIG_AUTO_INJECT_WORKFLOW_METADATA` | 지원되는 형식의 저장 파일에 워크플로우 메타데이터를 자동으로 주입할지 여부 |
| `thread_storage_backend` | `"local"` (상수) | `"local"` | `GTN_CONFIG_THREAD_STORAGE_BACKEND` | 대화 스레드의 스토리지 백엔드입니다. 'local'(파일 시스템)만 지원됩니다. |

## 시스템 요구사항 (System Requirements) {: #system-requirements }

시스템 리소스 요구사항 및 제한

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `minimum_disk_space_gb_libraries` | number | `10.0` | `GTN_CONFIG_MINIMUM_DISK_SPACE_GB_LIBRARIES` | 라이브러리 설치 및 가상 환경 작업에 필요한 최소 디스크 공간(GB) |
| `minimum_disk_space_gb_workflows` | number | `1.0` | `GTN_CONFIG_MINIMUM_DISK_SPACE_GB_WORKFLOWS` | 워크플로우 저장에 필요한 최소 디스크 공간(GB) |
| `discovery_max_depth` | integer | `5` | `GTN_CONFIG_DISCOVERY_MAX_DEPTH` | 등록된 항목이 파일을 재귀적으로 검색하기 위해 디렉토리를 가리킬 때 엔진이 탐색하는 최대 디렉토리 깊이입니다. 0은 최상위 디렉토리만 검색하며 중첩된 레벨마다 1씩 증가합니다. |

## MCP 서버 (MCP Servers) {: #mcp-servers }

Model Context Protocol 서버 구성

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `mcp_servers` | array of [MCPServerConfig](#mcpserverconfig) | `[]` | 해당 없음 (설정 파일 편집) | Model Context Protocol 서버 구성 목록 |

## 정적 서버 (Static Server) {: #static-server }

미디어 에셋 제공을 위한 정적 파일 서버 구성

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `static_server_base_url` | string | `null` | `GTN_CONFIG_STATIC_SERVER_BASE_URL` | 정적 서버의 기본 URL입니다. 설정하지 않으면 서버의 호스트/포트에서 파생됩니다. 터널(ngrok, cloudflare) 또는 역방향 프록시 뒤에 서버를 배치할 때 파생된 URL을 재정의하기 위해 설정합니다. |

## 아티팩트 (Artifacts) {: #artifacts }

아티팩트 공급자 및 미리보기 생성 설정

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `artifacts` | object | `{}` | `GTN_CONFIG_ARTIFACTS__<KEY>` | 이미지 및 기타 미디어 파일에 대한 미리보기가 생성되는 방식을 제어합니다. |

## 프로젝트 (Projects) {: #projects }

프로젝트 템플릿 구성 및 등록

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `project_file` | string | `null` | `GTN_CONFIG_PROJECT_FILE` | 엔진 시작 시 처음에 로드할 프로젝트 파일(`griptape-nodes-project.yml`)의 경로입니다. 설정된 경우 기본 위치인 `<workspace_directory>/griptape-nodes-project.yml`을 재정의합니다. 경로가 존재하지 않으면 워크스페이스 기본값으로 대체됩니다. 특수 값 `<system-defaults>`는 엔진이 워크스페이스 기본값 대체를 하지 않고 의도적으로 시스템 기본값을 사용함을 뜻합니다(워크스페이스의 `griptape-nodes-project.yml`이 자동 검색되지 않습니다). 엔진이 의도적으로 시스템 기본값을 사용하는 경우 이 값을 저장합니다. |
| `project_workspaces` | object | `{}` | `GTN_CONFIG_PROJECT_WORKSPACES__<KEY>` | 프로젝트 식별자를 워크스페이스 디렉토리 재정의에 매핑합니다. 키는 프로젝트 ID 또는 프로젝트 파일 경로일 수 있습니다. 먼저 로드된 프로젝트 ID와 비교하고, 일치하는 항목이 없으면 프로젝트 파일 경로로 간주합니다. 프로젝트가 로드될 때 키와 일치하면 프로젝트 인접 설정이나 자동 기본값 대신 해당 값을 워크스페이스 디렉토리로 사용합니다. |

## 에이전트 (Agent) {: #agent }

에이전트 동작 및 시스템 프롬프트

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `agent.system_prompt` | string | `""` | `GTN_CONFIG_AGENT__SYSTEM_PROMPT` | 에이전트의 기본 제공 시스템 프롬프트 뒤에 추가되는 텍스트입니다. 말투, 선호하는 패턴 또는 도메인 맥락을 맞춤 설정하는 데 사용합니다. |

## 라이브러리 (Libraries) {: #libraries }

라이브러리 관리 및 종속성 설치 설정

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `library.provisioned_by` | `engine`, `environment` 중 하나 | `"engine"` | `GTN_CONFIG_LIBRARY__PROVISIONED_BY` | 라이브러리와 Python 종속성을 제공하는 주체입니다. `engine`(기본값)은 엔진이 라이브러리를 다운로드하고, 라이브러리마다 가상 환경을 만들며, 종속성을 설치하게 합니다. `environment`는 다른 도구가 이미 준비한 환경에서 시작한 엔진용입니다. 이 경우 엔진은 `GTN_LIBRARY_PATHS` 환경 변수에 나열된 라이브러리만 로드하며, 가상 환경을 만들거나 라이브러리를 다운로드, 업데이트 또는 설치하지 않습니다. 그 밖의 설정된 라이브러리(`libraries_to_register` 항목과 `library.sandbox_enabled`가 true가 아니면 샌드박스 라이브러리)는 환경이 제공하지 않는 것으로 표시됩니다. 이때 라이브러리 종속성은 환경이 제공하는 라이브러리로만 충족됩니다. 다른 값은 `environment`로 취급되며 오류로 보고됩니다. 따라서 잘못 입력한 값이 다운로드나 빌드를 일으키지 않습니다. |
| `library.sandbox_enabled` | boolean | `null` | `GTN_CONFIG_LIBRARY__SANDBOX_ENABLED` | 샌드박스 라이브러리(`sandbox_library_directory`)를 검색하고 로드할지, 샌드박스 노드를 추가할 수 있을지 여부입니다. 설정하지 않으면(기본값) `library.provisioned_by`가 `engine`일 때 켜지고 `environment`일 때 꺼집니다. `true`는 두 모드 모두에서 켭니다. `environment` 모드에서는 샌드박스용 가상 환경을 만들지 않으므로, 샌드박스 노드가 가져오는 모든 항목이 이미 환경에 있어야 합니다. 환경이 제공하지 않는 다른 라이브러리는 여전히 거부됩니다. `false`는 두 모드 모두에서 끕니다. |
| `library.dependency_install_behavior` | `always`, `never` 중 하나 | `"always"` | `GTN_CONFIG_LIBRARY__DEPENDENCY_INSTALL_BEHAVIOR` | 라이브러리 매니페스트에 선언된 종속성의 자동 설치를 제어합니다. `always`는 등록할 때 종속성을 다운로드하고 설치합니다. `never`는 설치를 건너뛰며, 필요한 종속성이 없으면 라이브러리를 저하된(degraded) 상태로 표시합니다. |
| `library.lazy_node_loading` | boolean | `true` | `GTN_CONFIG_LIBRARY__LAZY_NODE_LOADING` | `true`(기본값)이면 노드의 Python 모듈은 시작할 때가 아니라 해당 유형의 노드가 처음 생성될 때(또는 introspection 등으로 유형을 확인할 때) 지연 가져오기됩니다. 노드가 많거나 무거운 라이브러리의 엔진 시작 속도가 빨라집니다. 단점은 깨진 노드의 가져오기 오류가 해당 유형이 처음 생성될 때까지 보고되지 않는다는 것입니다. `false`이면 엔진이 시작할 때 모든 노드의 Python 모듈을 가져오므로, 가져오기 오류가 캔버스에 배치되기 전에 라이브러리 문제로 즉시 나타납니다. 노드를 작성하면서 이 검사를 원하면 이 값을 설정하세요. 샌드박스 라이브러리의 노드는 이 설정과 관계없이 항상 즉시 로드됩니다. |
| `library.minimum_release_age` | number | `0.0` | `GTN_CONFIG_LIBRARY__MINIMUM_RELEASE_AGE` | 라이브러리 업데이트를 적용하기 전에 대상 릴리즈의 최소 경과 시간(시간 단위)입니다. 0(기본값)이면 업데이트를 사용할 수 있는 즉시 적용합니다. 0보다 크면 업데이트가 이동할 커밋이 이 시간 이상 지나기 전까지 업데이트를 보류합니다. 방금 푸시된 릴리즈를 잘못된 릴리즈인지 확인하고 철회할 시간이 없는 상태에서 자동으로 적용하는 일을 막기 위한 설정입니다. 대상 커밋의 경과 시간을 확인할 수 없으면(예: 원격 타임스탬프를 읽을 수 없음) 업데이트를 허용하고 경고를 기록합니다. 메타데이터 문제로 업데이트가 영구적으로 막히지 않게 하기 위함입니다. 경과 시간은 대상 커밋의 git committer 타임스탬프로 측정하며, 이는 릴리즈가 게시된 시각과 같지 않을 수 있습니다. rebase, cherry-pick, 날짜가 과거로 지정된 커밋(`GIT_COMMITTER_DATE`), 강제로 이동된 태그는 실제 게시 시각과 다른 경과 시간을 보고할 수 있습니다. |

## 베타 기능 (Beta Features) {: #beta-features }

끄거나 켤 수 있는 실험 기능

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `beta_features` | object | `{}` | `GTN_CONFIG_BETA_FEATURES__<KEY>` | 기능 id별로 켜거나 끈 실험 기능입니다. 에디터의 Beta 설정 페이지가 이 값을 기록합니다. 이 맵에 없는 기능은 기본값을 사용합니다. 모든 키를 허용하므로 에디터 전용 기능은 엔진 릴리즈 없이 추가할 수 있습니다. `enabled` 키는 전역 스위치입니다. `false`로 설정하면 각 기능의 값을 바꾸지 않고 모든 에디터, 엔진 및 라이브러리 베타 기능을 끕니다. |
| `library_beta_features` | object | `{}` | `GTN_CONFIG_LIBRARY_BETA_FEATURES__<KEY>` | 노드 라이브러리가 정의한 실험 기능을 켜거나 끕니다. 라이브러리 이름을 소문자로 바꾸고 공백과 구두점을 밑줄로 바꾼 값으로 키를 구분한 후, 기능 id로 다시 구분합니다. 이 맵에 없는 기능은 기본값을 사용합니다. |

## 항목 유형 (Entry Types) {: #entry-types }

리스트 또는 매핑 값 설정 안에 나타나는 객체의 필드입니다. 이 필드는 설정 전체가 아니라 개별 항목에 속하므로 점 구분 키나 환경 변수가 없습니다. 항목과 함께 설정 파일에서 편집하세요.

### LibraryDownload

`libraries_to_download`의 라이브러리 항목입니다. 엔진은 이 항목을 지정된 버전으로 준비합니다.

| 필드 | 유형 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `git_url` | string | 필수 | `url@ref` 형식의 Git 소스입니다. 전체 URL 또는 `user/repo` 축약형을 사용하며, 선택적으로 `@branch\|tag\|commit` 접미사를 붙일 수 있습니다(예: `griptape-ai/griptape-nodes-library-standard@v2.0`). |
| `version` | string | `null` | 설치된 라이브러리가 만족해야 하는 PEP 440 버전 지정자입니다(예: `>=1.2,\<2`). `None`이면 소스로만 고정합니다. |
| `name` | string | `null` | 라이브러리 매니페스트의 `name`과 일치하는 라이브러리 이름입니다. 설정하면 이름으로 설치된 버전을 찾아 다시 다운로드가 필요한지 판단합니다. |

### LibraryRegistration

`libraries_to_register`의 라이브러리 항목이며 선택적 메타데이터를 포함할 수 있습니다.

| 필드 | 유형 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `path` | string | 필수 | `griptape_nodes_library.json` 파일의 경로 또는 재귀적으로 검색할 디렉토리의 경로입니다. |
| `enabled` | boolean | `true` | `false`이면 라이브러리는 설정에 남아 있지만 시작할 때 로드되지 않습니다. |
| `worker_mode_override` | `ORCHESTRATOR`, `WORKER` 중 하나 | `null` | 이전 버전과의 호환성을 위해 허용됩니다. 더 이상 라이브러리의 실행 위치에 영향을 주지 않습니다. 라이브러리의 노드는 `pip_dependencies_exec`를 선언하면 워커에서 실행됩니다. |

### PerPlatformProjectPath

프로젝트 YAML 경로를 플랫폼별로 지정하는 매핑입니다.

| 필드 | 유형 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `linux` | string | `null` | Linux에서 사용하는 값입니다. |
| `darwin` | string | `null` | macOS에서 사용하는 값입니다. |
| `windows` | string | `null` | Windows에서 사용하는 값입니다. |
| `default` | string | `null` | 현재 플랫폼의 키가 설정되지 않았을 때 사용하는 대체 값입니다. |

### MCPServerConfig

단일 MCP 서버의 설정입니다.

| 필드 | 유형 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `name` | string | 필수 | MCP 서버의 고유 이름 또는 식별자입니다. |
| `enabled` | boolean | `true` | 이 MCP 서버를 사용할지 여부입니다. |
| `transport` | string | `"stdio"` | 전송 유형입니다: `stdio`, `sse`, `streamable_http`, `websocket` 중 하나입니다. |
| `command` | string | `null` | MCP 서버를 시작하는 명령입니다(`stdio`에 필수). |
| `args` | array of string | `[]` | MCP 서버 명령에 전달할 인수입니다(`stdio`). |
| `env` | object | `{}` | MCP 서버의 환경 변수입니다(`stdio`). |
| `cwd` | string | `null` | MCP 서버의 작업 디렉토리입니다(`stdio`). |
| `encoding` | string | `"utf-8"` | `stdio` 통신의 텍스트 인코딩입니다. |
| `encoding_error_handler` | string | `"strict"` | `stdio`의 인코딩 오류 처리기입니다. |
| `url` | string | `null` | HTTP 기반 연결의 URL입니다(`sse`, `streamable_http`, `websocket`). |
| `headers` | object | `null` | HTTP 기반 연결의 HTTP 헤더입니다. |
| `timeout` | number | `null` | HTTP 시간 제한(초)입니다. |
| `sse_read_timeout` | number | `null` | SSE 읽기 시간 제한(초)입니다. |
| `terminate_on_close` | boolean | `true` | 닫을 때 세션을 종료할지 여부입니다(`streamable_http`). |
| `description` | string | `null` | MCP 서버가 제공하는 기능에 대한 선택 설명입니다. |
| `capabilities` | array of string | `[]` | MCP 서버가 제공하는 기능 목록입니다. |
| `rules` | string | `null` | 이 MCP 서버에 대한 선택적 규칙을 하나의 문자열로 작성합니다. |
