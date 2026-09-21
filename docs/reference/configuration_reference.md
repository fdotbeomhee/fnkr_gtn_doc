# 설정 레퍼런스 (Configuration Reference)

카테고리별로 분류된 모든 Griptape Nodes 엔진 설정 항목입니다. 각 설정은 모든 `griptape_nodes_config.json` 파일에 지정할 수 있습니다(로드 순서는 [엔진 구성](../guides/configuration.md) 참조). 중첩된 설정의 스칼라 하위 키를 위한 `GTN_CONFIG_<NAME>__<SUB_KEY>` 형태 및 매핑 값 설정의 항목을 위한 `GTN_CONFIG_<NAME>__<KEY>` 형태를 포함하여 `GTN_CONFIG_*` 환경 변수가 지원되는 설정은 환경 변수를 통해 재정의할 수도 있습니다. 리스트 값 설정은 설정 파일에서 직접 편집해야 합니다. 매핑 키는 대소문자를 구분하여 일치하지만 전체 변수 이름은 소문자로 변환되므로 이미 소문자인 키만 환경 변수에서 접근할 수 있습니다(자세한 내용은 가이드 참조).

## 파일 시스템 (File System)

애플리케이션의 디렉토리 및 파일 경로 설정

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `workspace_directory` | string | `<현재_작업_디렉토리>/GriptapeNodes` | `GTN_CONFIG_WORKSPACE_DIRECTORY` | 프로젝트, 워크플로우 및 생성된 에셋의 루트 디렉토리입니다. 기본값은 현재 작업 디렉토리 아래의 GriptapeNodes 폴더입니다. 다른 파일 시스템 경로(libraries_directory, static_files_directory, sandbox_library_directory, synced_workflows_directory)는 절대 경로로 설정되지 않은 한 이 디렉토리를 기준으로 상대적으로 해석됩니다. |
| `static_files_directory` | string | `"staticfiles"` | `GTN_CONFIG_STATIC_FILES_DIRECTORY` | 워크스페이스 디렉토리를 기준으로 한 정적 파일 디렉토리의 상대 경로입니다. |
| `sandbox_library_directory` | string | `"sandbox_library"` | `GTN_CONFIG_SANDBOX_LIBRARY_DIRECTORY` | 샌드박스 라이브러리 디렉토리의 경로입니다(노드 개발 시 유용). 상대 경로는 워크스페이스 디렉토리를 기준으로 해석되며 절대 경로는 그대로 사용됩니다. |
| `libraries_directory` | string | `"libraries"` | `GTN_CONFIG_LIBRARIES_DIRECTORY` | 다운로드된 라이브러리 디렉토리의 경로입니다. 재귀적으로 발견되는 모든 griptape_nodes_library.json 파일이 시작 시 자동 감지됩니다. 상대 경로는 워크스페이스 디렉토리를 기준으로 해석되며 절대 경로는 그대로 사용됩니다. 프로젝트 템플릿의 `libraries_dir` 필드를 통해 이 위치를 재정의할 수 있으며 상위 프로젝트의 라이브러리 설치 위치를 공유할 수 있습니다. |
| `ffmpeg_directory` | string | `""` | `GTN_CONFIG_FFMPEG_DIRECTORY` | 엔진이 처음 사용할 때 다운로드하는 ffmpeg/ffprobe 바이너리를 보관하는 디렉토리의 절대 경로입니다. 다른 디렉토리 설정과 달리 워크스페이스를 기준으로 해석되지 않습니다. ffmpeg 캐시는 머신에 귀속되므로 모든 워크스페이스와 프로젝트에서 공유됩니다. 상대 경로는 경고와 함께 무시됩니다. 빈 값(기본값)은 `<XDG_DATA_HOME>/griptape_nodes/ffmpeg`를 의미합니다. 다운로드 대신 자체 바이너리를 제공하려면 ffmpeg, ffprobe 및 빈 `installed.crumb` 파일이 포함된 `bin/<platform>/` 디렉토리를 지정하세요. |
| `synced_workflows_directory` | string | `"synced_workflows"` | `GTN_CONFIG_SYNCED_WORKFLOWS_DIRECTORY` | 워크스페이스 디렉토리를 기준으로 한 동기화된 워크플로우 디렉토리의 상대 경로입니다. |
| `enable_workspace_file_watching` | boolean | `true` | `GTN_CONFIG_ENABLE_WORKSPACE_FILE_WATCHING` | 동기화된 워크플로우 디렉토리에 대한 파일 감시(file watching) 활성화 여부 |

## 애플리케이션 이벤트 (Application Events)

애플리케이션 수명 주기 이벤트 구성

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `app_events` | object | (중첩 객체) | `GTN_CONFIG_APP_EVENTS__ON_APP_INITIALIZATION_COMPLETE__REQUIRES_ENGINE` | 중첩 설정입니다. 나열된 하위 키는 환경 변수에서 설정할 수 있으며 모든 하위 키는 설정 파일에서 직접 편집할 수 있습니다. |

## 실행 (Execution)

워크플로우 실행 및 처리 설정

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `log_level` | `CRITICAL`, `ERROR`, `WARNING`, `INFO`, `DEBUG` 중 하나 | `"INFO"` | `GTN_CONFIG_LOG_LEVEL` | 엔진의 로깅 상세도입니다. 가장 낮은 수준부터 높은 수준 순으로 CRITICAL, ERROR, WARNING, INFO, DEBUG 중 하나입니다. |
| `workflow_execution_mode` | `sequential`, `parallel` 중 하나 | `"sequential"` | `GTN_CONFIG_WORKFLOW_EXECUTION_MODE` | 노드 처리를 위한 워크플로우 실행 모드입니다. SEQUENTIAL 모드는 max_nodes_in_parallel=1을 사용하여 노드를 한 번에 하나씩 실행합니다. PARALLEL 모드는 설정된 max_nodes_in_parallel 값을 사용합니다. |
| `max_nodes_in_parallel` | integer | `5` | `GTN_CONFIG_MAX_NODES_IN_PARALLEL` | 병렬 실행 시 한 번에 실행되는 최대 노드 수입니다. |
| `worker` | object | (중첩 객체) | `GTN_CONFIG_WORKER__HEARTBEAT_INTERVAL_S`, `GTN_CONFIG_WORKER__HEARTBEAT_TIMEOUT_S`, `GTN_CONFIG_WORKER__HEARTBEAT_STARTUP_GRACE_S` | 워커 프로세스 관련 중첩 설정입니다. |

## 스토리지 (Storage)

데이터 스토리지 및 지속성 구성

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `storage_backend` | `local`, `gtc` 중 하나 | `"local"` | `GTN_CONFIG_STORAGE_BACKEND` | 워크플로우 데이터 및 생성된 에셋을 유지하는 데 사용되는 백엔드입니다. 'local'은 워크스페이스 아래 로컬 파일 시스템에 파일을 저장하고 'gtc'는 Griptape Cloud 스토리지를 사용합니다. |
| `auto_inject_workflow_metadata` | boolean | `true` | `GTN_CONFIG_AUTO_INJECT_WORKFLOW_METADATA` | 지원되는 형식의 저장 파일에 워크플로우 메타데이터를 자동으로 주입할지 여부 |
| `thread_storage_backend` | `"local"` (상수) | `"local"` | `GTN_CONFIG_THREAD_STORAGE_BACKEND` | 대화 스레드의 스토리지 백엔드입니다. 'local'(파일 시스템)만 지원됩니다. |

## 시스템 요구사항 (System Requirements)

시스템 리소스 요구사항 및 제한

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `minimum_disk_space_gb_libraries` | number | `10.0` | `GTN_CONFIG_MINIMUM_DISK_SPACE_GB_LIBRARIES` | 라이브러리 설치 및 가상 환경 작업에 필요한 최소 디스크 공간(GB) |
| `minimum_disk_space_gb_workflows` | number | `1.0` | `GTN_CONFIG_MINIMUM_DISK_SPACE_GB_WORKFLOWS` | 워크플로우 저장에 필요한 최소 디스크 공간(GB) |
| `discovery_max_depth` | integer | `5` | `GTN_CONFIG_DISCOVERY_MAX_DEPTH` | 등록된 항목이 파일을 재귀적으로 검색하기 위해 디렉토리를 가리킬 때 엔진이 탐색하는 최대 디렉토리 깊이입니다. 0은 최상위 디렉토리만 검색하며 중첩된 레벨마다 1씩 증가합니다. |

## MCP 서버 (MCP Servers)

Model Context Protocol 서버 구성

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `mcp_servers` | array | `[]` | 해당 없음 (설정 파일 편집) | Model Context Protocol 서버 구성 목록 |

## 정적 서버 (Static Server)

미디어 에셋 제공을 위한 정적 파일 서버 구성

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `static_server_base_url` | string | `null` | `GTN_CONFIG_STATIC_SERVER_BASE_URL` | 정적 서버의 기본 URL입니다. 설정하지 않으면 서버의 호스트/포트에서 파생됩니다. 터널(ngrok, cloudflare) 또는 역방향 프록시 뒤에 서버를 배치할 때 파생된 URL을 재정의하기 위해 설정합니다. |

## 아티팩트 (Artifacts)

아티팩트 공급자 및 미리보기 생성 설정

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `artifacts` | object | `{}` | `GTN_CONFIG_ARTIFACTS__<KEY>` | 이미지 및 기타 미디어 파일에 대한 미리보기가 생성되는 방식을 제어합니다. |

## 프로젝트 (Projects)

프로젝트 템플릿 구성 및 등록

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `project_file` | string | `null` | `GTN_CONFIG_PROJECT_FILE` | 엔진 시작 시 처음에 로드할 프로젝트 파일(`griptape-nodes-project.yml`)의 경로입니다. 설정된 경우 기본 위치인 `<workspace_directory>/griptape-nodes-project.yml`을 재정의합니다. |
| `project_workspaces` | object | `{}` | `GTN_CONFIG_PROJECT_WORKSPACES__<KEY>` | 프로젝트 식별자를 워크스페이스 디렉토리 재정의에 매핑합니다. |

## 에이전트 (Agent)

에이전트 동작 및 시스템 프롬프트

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `agent` | object | (중첩 객체) | `GTN_CONFIG_AGENT__SYSTEM_PROMPT` | 에이전트 동작 및 기본 시스템 프롬프트 관련 중첩 설정입니다. |

## 라이브러리 (Libraries)

라이브러리 관리 및 종속성 설치 설정

| 설정 | 유형 | 기본값 | 환경 변수 | 설명 |
| --- | --- | --- | --- | --- |
| `library` | object | (중첩 객체) | `GTN_CONFIG_LIBRARY__DEPENDENCY_INSTALL_BEHAVIOR`, `GTN_CONFIG_LIBRARY__LAZY_NODE_LOADING`, `GTN_CONFIG_LIBRARY__MINIMUM_RELEASE_AGE` | 종속성 설치 동작, 지연 노드 로딩, 최소 릴리즈 기간 등 라이브러리 관리 관련 설정입니다. |
