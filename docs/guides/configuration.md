# 엔진 설정 (Engine Configuration)

자체 머신에서 Griptape Nodes 엔진을 실행할 때 환경 설정을 관리할 수 있는 유틸리티가 제공됩니다. 더 복잡한 프로젝트를 구축 및 관리하거나 팀원과 프로젝트를 공유할 때 설정이 어떻게 로드되는지 이해하는 것은 매우 중요합니다.

> 설치 과정에서 `gtn init`이 자동으로 실행되었습니다.

> 특정 설정을 찾고 계신가요? [설정 레퍼런스](../reference/configuration_reference.md)에는 카테고리별로 그룹화된 모든 설정의 유형, 기본값, 환경 변수 및 설명이 나와 있습니다.

## 에디터에서 설정 편집하기

설정을 변경하는 권장 방법은 에디터에 내장된 **환경설정 에디터(Configuration Editor)**를 사용하는 것입니다:

1. 에디터 헤더의 **Settings** 메뉴를 열고 **All Settings**를 선택합니다. 동일한 하위 메뉴에는 해당 카테고리로 환경설정 에디터를 바로 여는 직접 항목(**Engine Settings** 또는 **Library Settings** 등)도 있습니다.
1. 왼쪽 사이드바에서 카테고리(**Editor Settings**, **Engine Settings**, **File System**, **Libraries**, **Library Settings**, **MCP Servers**, **API Keys & Secrets**)를 선택하거나 상단의 검색 상자를 사용하여 이름으로 설정을 필터링합니다.
1. 값을 변경합니다. 환경설정 에디터가 자동으로 구성 파일에 값을 기록합니다.

일부 설정은 엔진이 재시작된 후에만 적용됩니다. `static_server_base_url`이 한 예입니다([정적 파일 서버 구성](#정적-파일-서버-구성-static-file-server-configuration) 참조).

이 페이지의 나머지 부분에서는 환경설정 에디터가 작성하는 파일 및 환경 변수와 이들이 병합되는 방식을 설명합니다. 설정을 자동화하거나, 헤드리스(headless) 환경에서 실행하거나, 팀과 설정을 공유할 때만 이 파일들을 직접 편집하세요.

## 설정 로딩 (Configuration Loading)

Griptape Nodes는 특정 검색 순서를 사용하여 환경 변수 및 구성 파일에서 설정을 로드합니다. 이 프로세스를 이해하는 것이 설정 관리의 핵심입니다.

1. **환경 변수 (`.env`)**
    환경 변수는 API 키와 같은 민감한 비밀 정보를 안전하게 저장하는 데 사용됩니다. Griptape Nodes는 env 파일을 자동으로 로드하여 이러한 비밀 정보를 애플리케이션에서 사용할 수 있도록 합니다.

    - 기본 `.env` 파일은 시스템 전체 사용자 구성 디렉터리에서 로드됩니다: `xdg_config_home() / "griptape_nodes" / ".env"` (일반적으로 `~/.config/griptape_nodes/.env`).
    - 이 파일은 `GT_CLOUD_API_KEY`, `OPENAI_API_KEY`와 같은 비밀 정보를 위한 것입니다.

    > 이러한 파일과 직접 상호 작용할 필요는 없습니다. Griptape Nodes는 Settings 대화 상자를 통해 환경 변수를 관리합니다.

1. **구성 파일 (`griptape_nodes_config.json`)**
    구성 파일에는 Node Libraries를 찾을 위치와 같은 Griptape Nodes 작동에 중요한 정보와 Griptape Nodes 환경을 맞춤 설정하기 위한 사용자 기본 설정이 포함되어 있습니다.

    - 구성 파일이 발견되지 않으면 Griptape Nodes는 내장된 기본값을 사용하여 실행됩니다.
    - 구성 파일은 항상 JSON 형식(`griptape_nodes_config.json`)입니다. 설정은 최대 3개의 해당 파일과 런타임 오버라이드 및 환경 변수에서 로드되며 우선순위에 따라 병합됩니다:
    - **로드 순서 (낮은 번호가 먼저 로드되며, 높은 번호가 이를 오버라이드함):**
        1. **내장 기본값 (Built-in defaults)** — 애플리케이션에 내장된 기본값.
        1. **사용자 구성 (User config)** — `~/.config/griptape_nodes/griptape_nodes_config.json`. 이 머신의 전역 설정.
        1. **프로젝트 인접 구성 (Project-adjacent config)** — `<project_dir>/griptape_nodes_config.json`. 프로젝트가 활성화 상태로 설정될 때 로드됩니다. 프로젝트 파일과 함께 공유 기본값을 배포하는 데 사용합니다.
        1. **작업 공간 구성 (Workspace config)** — `<workspace_dir>/griptape_nodes_config.json`. 작업 공간이 확인(resolved)된 후 로드됩니다. 공유 프로젝트 구성보다 우선하는 사용자별 오버라이드에 사용합니다. 작업 공간 디렉터리가 프로젝트 디렉터리와 같으면 이 파일은 프로젝트 인접 구성과 동일하며 두 번 로드되지 않습니다.
        1. **프로젝트별 작업 공간 오버라이드 (Per-project workspace override)** — 활성 프로젝트의 경로가 사용자 구성의 `project_workspaces` 매핑에 있는 키와 일치할 때 해당 작업 공간 디렉터리가 적용됩니다. 이는 `workspace_directory`만 설정하여 위의 구성 파일의 모든 값을 오버라이드합니다. 이를 담고 있는 파일이 없으므로 `gtn self info`에서 `runtime` 레이어로 나타나며, 활성화되어 있는 동안에는 `workspace_directory`에 대한 설정 편집으로 이를 변경할 수 없습니다. 프로젝트 템플릿 자체의 `workspace_dir` 및 상위 프로젝트에서 상속된 작업 공간도 동일한 방식으로 적용됩니다. 이 중 아무것도 선언하지 않은 프로젝트를 열면 사용자 구성에 이미 지정된 값이 고정되며, 이는 여전히 사용자 구성으로 보고되고 편집 가능한 상태로 유지되어 다음에 프로젝트를 열 때 적용됩니다. 자세한 내용은 [작업 공간](projects/workspace.md#프로젝트별-작업-공간-오버라이드-per-project-workspace-overrides)을 참조하세요.
        1. **환경 변수 (Environment variables)** — `GTN_CONFIG_*` 접두사(가장 높은 우선순위). 아래를 참조하세요.
    - **오버라이드 우선순위:** 나중에 로드된 파일의 설정이 이전에 로드된 파일의 설정을 오버라이드합니다.

1. **기본값 및 병합 (Defaults and Merging)**
    Griptape Nodes는 기본 작업 공간 디렉터리를 포함하여 다양한 옵션에 대한 기본 설정값을 내장하고 있습니다. 이러한 기본값은 검색된 구성 파일에서 로드된 설정에 의해 오버라이드되지 않는 한 사용됩니다.

    - 처음 발견된 구성 파일에서 로드된 설정은 내장 기본값을 오버라이드합니다.
    - 검색 경로에서 구성 파일이 발견되지 않으면 애플리케이션은 내장 기본값만 사용합니다.
    - 주요 기본값 중 하나는 `workspace_directory`로, 로드된 구성 파일에 지정되지 않은 경우 기본값은 `<current_working_directory>/GriptapeNodes`입니다.
    - 빈 값은 해당 파일에서 해당 설정을 지정하지 않았음을 의미합니다. 설정을 비우는 것은 해당 파일에서 설정을 제거하는 것과 같으므로 목록의 다음 파일(또는 내장 기본값)이 대신 적용됩니다. 즉, 항목을 수동으로 삭제할 필요가 없습니다.

1. **런타임 관리 (`ConfigManager`)**
    초기 설정이 로드된 후 `ConfigManager`는 최종 확인된 구성, 특히 작업 공간 디렉터리를 사용하여 런타임 작업을 처리합니다. 등록된 워크플로우와 같은 사용자별 변경 사항을 작업 공간 내의 구성 파일에 다시 저장하는 역할을 담당합니다.

    - 설정이 로드되면 `ConfigManager`는 최종 확인된 `workspace_directory`를 사용합니다.
    - 런타임에 수행된 수정 사항(예: 커스텀 워크플로우 등록)은 일반적으로 `ConfigManager`에 의해 이 확인된 `workspace_directory` 내에 위치한 `griptape_nodes_config.json` 파일에 저장됩니다.

## 로딩 예시 (Loading Examples)

다음은 구성 파일이 검색되고 로드되는 방식을 설명하는 몇 가지 시나리오입니다:

**시나리오 1: 기본값 사용**

- `gtn init`을 실행하고 기본 설정을 수락합니다.

- `gtn init`은 `~/.config/griptape_nodes/griptape_nodes_config.json` 및 `~/.config/griptape_nodes/.env`를 생성합니다. `.json` 파일 내의 `workspace_directory`를 `<current_directory_where_init_was_run>/GriptapeNodes`를 가리키도록 설정합니다.

- 나중에 `/home/user/my_project/`에서 `gtn`을 실행합니다.

- **파일 구조:**

    ```
    /home/user/
        my_project/          <-- 'gtn' 실행 시 CWD
            GriptapeNodes/   <-- 기본 작업 공간 (런타임에 저장된 구성을 포함할 수 있음)
            my_flow.graph.json
        .config/
            griptape_nodes/
                .env                     # 환경 변수 로드용
                griptape_nodes_config.json # workspace_directory = /home/user/my_project/GriptapeNodes 포함
    ```

- **로딩 프로세스:**

    1. 내장 기본값을 로드합니다.
    1. `~/.config/griptape_nodes/griptape_nodes_config.json`을 로드하여(발견됨!) 기본값 위에 병합합니다.
    1. **결과:** `workspace_directory`가 `/home/user/my_project/GriptapeNodes`로 설정됩니다. `ConfigManager`가 관리하는 후속 런타임 변경 사항은 `/home/user/my_project/GriptapeNodes/griptape_nodes_config.json`에 저장됩니다.

**시나리오 2: 커스텀 작업 공간**

- `gtn init --workspace-directory /data/gtn_work`를 실행합니다.

- `gtn init`은 `~/.config/griptape_nodes/griptape_nodes_config.json`(`workspace_directory = "/data/gtn_work"` 설정) 및 `~/.config/griptape_nodes/.env`를 생성합니다.

- 작업 공간별 설정을 저장하기 위해 수동으로 `/data/gtn_work/griptape_nodes_config.json`을 생성할 수 있습니다.

- `/home/user/some_dir/`에서 `gtn`을 실행합니다.

- **파일 구조:**

    ```
    /home/user/
        some_dir/            <-- 'gtn' 실행 시 CWD
        .config/
            griptape_nodes/
                .env                     # 환경 변수 로드용
                griptape_nodes_config.json # workspace_directory = /data/gtn_work 포함
    /data/
        gtn_work/            <-- 커스텀 작업 공간
            griptape_nodes_config.json # 작업 공간 구성 (런타임 변경 사항도 여기에 저장됨)
            project_flows/
    ```

- **로딩 프로세스:**

    1. 내장 기본값을 로드합니다.
    1. `~/.config/griptape_nodes/griptape_nodes_config.json`을 로드합니다(발견됨!). 이렇게 하면 `workspace_directory`가 `/data/gtn_work`로 설정됩니다.
    1. 작업 공간을 확인하고 `/data/gtn_work/griptape_nodes_config.json`을 작업 공간 구성으로 로드하여 사용자 구성 위에 병합합니다.
    1. **결과:** `workspace_directory`는 `/data/gtn_work`가 됩니다. 작업 공간 구성의 설정은 이 작업 공간에 대해 사용자 구성을 오버라이드합니다. 런타임 변경 사항은 `/data/gtn_work/griptape_nodes_config.json`에 다시 저장됩니다.

**시나리오 3: 사용자 구성 없음 (내장 기본값)**

- `gtn init`을 실행하지 않았거나 `~/.config/griptape_nodes/`를 삭제했습니다.

- 활성화된 프로젝트 없이 `/home/user/my_project/`에서 `gtn`을 실행합니다.

- **파일 구조:**

    ```
    /home/user/
        my_project/          <-- 'gtn' 실행 시 CWD
            GriptapeNodes/   <-- 기본 작업 공간 위치
            my_flow.graph.json
    ```

- **로딩 프로세스:**

    1. 내장 기본값을 로드합니다.
    1. `~/.config/griptape_nodes/griptape_nodes_config.json`을 확인합니다(찾을 수 없다고 가정).
    1. 활성 프로젝트가 없으므로 프로젝트 인접 구성이 로드되지 않습니다.
    1. **결과:** 애플리케이션이 내장 기본값으로 실행됩니다. `workspace_directory`는 기본값인 `<current_working_directory>/GriptapeNodes` = `/home/user/my_project/GriptapeNodes`로 폴백되며, 런타임 변경 사항은 해당 작업 공간 내에 생성된 `griptape_nodes_config.json`에 저장됩니다.

## 환경 변수 오버라이드 (Environment Variable Overrides)

구성 값은 `GTN_CONFIG_` 접두사가 붙은 환경 변수를 사용하여 설정하거나 오버라이드할 수 있습니다. 키는 대문자로 된 구성 설정 이름입니다:

```
GTN_CONFIG_<SETTING_NAME>=<value>
```

중첩된 설정(`worker`, `agent`, `library`와 같은 객체 내에 있는 설정)은 경로의 각 레벨을 대문자로 구분하는 이중 밑줄(`__`)로 접근합니다:

```
GTN_CONFIG_<PARENT>__<SUB_KEY>=<value>
```

설정 이름에 이미 밑줄이 포함되어 있으므로 단일 밑줄 대신 이중 밑줄이 필요합니다. `GTN_CONFIG_WORKER_HEARTBEAT_TIMEOUT_S`는 최상위 설정인 `worker_heartbeat_timeout_s`와 `worker.heartbeat_timeout_s` 사이에 모호할 수 있습니다. 어떤 설정 이름에도 `__`가 포함되어 있지 않으므로 경로가 한 단계 내려가는 위치를 명확하게 표시합니다.

환경 변수 오버라이드는 **가장 높은 우선순위**를 가집니다. 사용자 구성 파일, 프로젝트 인접 구성 파일, 프로젝트별 작업 공간 오버라이드 및 내장 기본값보다 우선합니다.

예시:

| 설정 | 환경 변수 |
| ---- | --------- |
| `workspace_directory` | `GTN_CONFIG_WORKSPACE_DIRECTORY` |
| `libraries_directory` | `GTN_CONFIG_LIBRARIES_DIRECTORY` |
| `project_file` | `GTN_CONFIG_PROJECT_FILE` |
| `log_level` | `GTN_CONFIG_LOG_LEVEL` |
| `storage_backend` | `GTN_CONFIG_STORAGE_BACKEND` |
| `worker.heartbeat_timeout_s` | `GTN_CONFIG_WORKER__HEARTBEAT_TIMEOUT_S` |
| `library.lazy_node_loading` | `GTN_CONFIG_LIBRARY__LAZY_NODE_LOADING` |
| `agent.system_prompt` | `GTN_CONFIG_AGENT__SYSTEM_PROMPT` |

이 기능은 구성 파일을 수정하지 않고 구성을 주입하려는 스크립팅 환경, 컨테이너 및 CI/CD 파이프라인에 유용합니다:

```bash
GTN_CONFIG_PROJECT_FILE=/shared/studio-project.yml gtn
GTN_CONFIG_WORKER__HEARTBEAT_TIMEOUT_S=30 gtn
GTN_CONFIG_LIBRARY__LAZY_NODE_LOADING=false gtn
GTN_CONFIG_AGENT__SYSTEM_PROMPT="Answer tersely." gtn
```

값은 적용되기 전에 설정의 선언된 유형으로 변환되므로 부울(boolean) 설정의 `false`는 문자열 `"false"`가 아니라 `False`를 의미하며, 숫자 설정의 `30`은 텍스트가 아니라 숫자 `30`을 의미합니다. 이 변환은 `GTN_CONFIG_ARTIFACTS__SOME_KEY`와 같은 매핑 값 설정의 항목에는 적용되지 않습니다. 해당 값은 무엇이든 수락하도록 유형이 지정되어 있으므로 작성된 그대로 전달되며, 부울 또는 숫자 매핑 항목에는 대신 구성 파일이 필요합니다.

> **두 가지 제한 사항:**
>
> 1. **리스트 값 설정 및 대소문자를 구분하는 키:** 리스트에는 `Settings` 모델이 허용하는 문자열 형식이 없으므로 `app_events.on_app_initialization_complete.libraries_to_register` 또는 `mcp_servers`와 같이 리스트를 보유하는 설정은 환경 변수에서 설정할 수 없습니다. *매핑* 값 설정은 다릅니다. 항목은 `GTN_CONFIG_<NAME>__<KEY>=<value>`(예: `GTN_CONFIG_ARTIFACTS__SOME_KEY=1`)로 설정할 수 있습니다. 그러나 전체 변수 이름은 구성 키가 되기 전에 소문자로 변환되므로 키가 이미 소문자인 항목에만 접근할 수 있습니다. 이 때문에 `artifacts`는 이 방식으로 사용할 수 있지만 `project_workspaces`(키는 대소문자를 구분하는 프로젝트 ID 또는 파일 경로) 및 `secrets_to_register`(대문자 비밀 이름)는 실제로 신뢰할 수 없으며 선언된 설정 외부의 대소문자 구분 경로(예: `nodes.<LibraryName>.<SECRET_NAME>`)는 전혀 접근할 수 없습니다. 이러한 항목에는 `griptape_nodes_config.json` 파일을 편집하세요.
> 1. **파싱할 수 없는 값은 대개 구성 파일로 폴백되지만 네 가지 설정은 예외입니다.** 설정 유형에 맞지 않는 값(예: `GTN_CONFIG_MAX_NODES_IN_PARALLEL=not-a-number`)은 경고와 함께 무시되고 구성 파일 레이어가 대신 값을 제공합니다. `log_level`, `workflow_execution_mode`, `thread_storage_backend`, `library.dependency_install_behavior`는 예외입니다. 이 중 하나에 대해 인식할 수 없는 값은 경고나 구성 파일 폴백 없이 조용히 해당 설정의 내장 기본값이 됩니다.
>
> [설정 레퍼런스](../reference/configuration_reference.md)에는 중첩된 각 하위 키의 전체 `__` 경로를 포함하여 환경 변수가 있는 모든 설정의 정확한 환경 변수가 나와 있습니다.

### 재귀 탐색 깊이 (`discovery_max_depth`)

`projects_to_register`, `libraries_to_register` 또는 `workflows_to_register`가 디렉터리를 가리킬 때 엔진은 시작 시 관련 파일(각각 프로젝트 파일, 라이브러리 매니페스트 및 워크플로우 파일)을 해당 디렉터리에서 재귀적으로 검색합니다. 지나치게 깊은 트리(또는 심볼릭 링크 루프)가 부팅 시퀀스를 지연시키지 않도록 검색 깊이가 제한됩니다. `discovery_max_depth` 설정은 해당 상한을 제어합니다. 기본값은 등록된 디렉터리 아래 **5**단계 디렉터리 레벨이며, 일반적인 레이아웃을 충분히 지원합니다.

일반 설정이므로 모든 구성 파일에서 설정하거나 `GTN_CONFIG_DISCOVERY_MAX_DEPTH` 환경 변수로 오버라이드할 수 있습니다:

```bash
GTN_CONFIG_DISCOVERY_MAX_DEPTH=20 gtn   # 더 깊게 중첩된 레이아웃 검색
```

값 `0`은 최상위 디렉터리만 검색합니다(하위 디렉터리 없음).

## 작업 공간 디렉터리 (Workspace Directory)

`gtn init`을 실행하는 동안 작업 공간 디렉터리(Workspace Directory)를 지정합니다. 이는 프로젝트, 저장된 플로우 및 프로젝트별 설정의 루트입니다.

`gtn init`은 `<current_working_directory>/GriptapeNodes`를 기본값으로 제안할 수 있지만 원하는 위치를 선택할 수 있습니다. Griptape Nodes는 사용자가 제공한 정확한 경로를 사용하며, 이 경로는 시스템 `griptape_nodes_config.json`에 저장됩니다.

하드코딩된 `GriptapeNodes` 하위 디렉터리 내에서 자동으로 검색하지 **않으며**, 오직 구성된 경로에만 의존합니다.

### 작업 공간에서 라이브러리 분리하기

기본적으로 다운로드된 라이브러리는 작업 공간 **내부**의 `libraries/` 폴더에 위치합니다. `libraries_directory`가 `workspace_directory`를 기준으로 확인되는 상대 경로이기 때문입니다. 작업 공간이 느리거나 원격 드라이브(네트워크 공유, 마운트된 볼륨)에 있는 경우 엔진이 디스크에서 라이브러리 코드를 자주 읽기 때문에 라이브러리를 거기에 두면 성능이 저하될 수 있습니다.

작업 공간(프로젝트, 워크플로우, 생성된 에셋)을 원격 드라이브에 유지하면서 빠른 로컬 스토리지의 **절대(absolute)** 경로로 `libraries_directory`를 지정할 수 있습니다. `libraries_directory`가 절대 경로인 경우 그대로 사용되며 라이브러리에 대한 작업 공간 위치는 무시됩니다:

```json
{
    "workspace_directory": "/Volumes/team-share/GriptapeNodes",
    "libraries_directory": "/Users/me/.griptape-nodes-libraries"
}
```

환경 변수를 통해 동일하게 설정할 수도 있습니다:

```bash
GTN_CONFIG_LIBRARIES_DIRECTORY=/Users/me/.griptape-nodes-libraries gtn
```

동일한 상대 경로 대 절대 경로 규칙이 `sandbox_library_directory` 및 `static_files_directory`에도 적용되므로 작업 공간이 원격에 유지되는 동안 이들 중 어느 것이든 로컬 스토리지로 독립적으로 재배치할 수 있습니다.

여기서 `libraries_directory`는 머신 전체의 기본값입니다. 개별 프로젝트는 프로젝트 파일의 자체 [`libraries_dir`](projects/projects.md#라이브러리-디렉터리-libraries-directory) 필드로 이를 오버라이드할 수 있으며, 이 필드는 이 구성 값보다 우선하며 프로젝트와 함께 이동합니다(자식 프로젝트에 상속됨). 전체 엔진에 대해 라이브러리를 재배치하려면 config 설정을 사용하고, 특정 프로젝트 또는 프로젝트 트리에 자체 공유 라이브러리 위치가 필요한 경우 프로젝트 필드를 사용하세요.

## 엔진이 게시하는 변수 (Variables the Engine Publishes)

위의 모든 변수는 **사용자**가 설정하는 변수입니다. 엔진은 시작 시 자체 환경에 **사용자가 읽을 수 있는** 변수 하나를 게시합니다:

| 변수 | 값 |
| ---- | -- |
| `GTN_DEFAULT_LIBRARIES_ROOT` | 기본적으로 라이브러리가 설치되는 디렉터리의 절대 경로 |

이 변수는 사용자가 설정하지 않습니다. 이 변수의 목적은 머신별로 경로를 하드코딩하지 않고도 프로젝트 파일이 이 엔진이 라이브러리를 유지하는 위치를 가리킬 수 있도록 하는 것입니다:

```yaml
libraries_dir: "${GTN_DEFAULT_LIBRARIES_ROOT}/shared"
```

이는 `griptape-nodes init`이 표준 라이브러리를 설치하는 위치와 동일한 위치로 확인되므로 프로젝트가 자체 복사본을 다운로드하는 대신 기존 라이브러리 트리를 공유할 수 있습니다. 값은 항상 절대 경로이므로, *상대* 값을 작업 공간이 아닌 프로젝트 파일 자체 디렉터리에 고정하는 `libraries_dir`에서 안전하게 사용할 수 있습니다.

게시된 값은 `libraries_directory` 및 `GTN_CONFIG_LIBRARIES_DIRECTORY`를 포함하여 이 엔진이 라이브러리를 유지하는 위치를 설명하는 설정을 반영합니다. 이 값을 읽는 필드 자체가 프로젝트의 `libraries_dir`이므로 의도적으로 프로젝트 자체의 `libraries_dir`을 따르지 **않습니다**. 또한 시작 시 한 번 계산되므로 해당 프로젝트를 열 때 `libraries_directory`의 위치를 다시 지정하는 프로젝트 인접 구성 또는 작업 공간 `griptape_nodes_config.json`은 여기에 반영되지 않습니다.

!!! warning

    설정된 적이 없는 변수를 지정하는 프로젝트 파일은 **로드 시점에 거부**되며 프로젝트가 열리지 않습니다. 따라서 `${GTN_DEFAULT_LIBRARIES_ROOT}`를 사용하는 프로젝트 파일에는 이를 게시하는 엔진 버전이 필요합니다.

## 정적 파일 서버 구성 (Static File Server Configuration)

Griptape Nodes를 실행할 때 로컬 정적 파일 서버는 워크플로우에서 생성된 미디어 에셋(이미지, 비디오, 오디오)을 호스팅합니다. `static_server_base_url` 설정은 이러한 파일에 대한 링크를 생성할 때 사용되는 기본 URL을 제어합니다. 기본적으로 `http://localhost:8124`를 사용하지만 터널, 프록시를 사용하거나 컨테이너에 배포할 때 이를 오버라이드할 수 있습니다.

### 이 설정을 오버라이드해야 하는 경우

다음 시나리오에서는 `static_server_base_url`을 구성해야 합니다:

- **터널링 서비스**: ngrok, cloudflare tunnels 또는 유사한 서비스를 사용하여 로컬 서버를 외부에 노출할 때
- **Docker/Kubernetes**: 내부 주소가 외부 액세스 포인트와 다른 컨테이너에서 실행할 때
- **역방향 프록시(Reverse Proxy)**: nginx, Apache 또는 기타 역방향 프록시 뒤에서 실행할 때
- **원격 개발**: 원격 머신에서 작업하고 로컬 브라우저에서 UI에 액세스할 때
- **팀 협업**: 생성된 미디어에 액세스해야 하는 팀원과 실행 중인 인스턴스를 공유할 때

### 구성 방법

정적 서버 기본 URL은 Griptape Nodes UI Settings 대화 상자의 `static_server_base_url` 설정을 사용하여 구성됩니다. 명시적으로 설정하지 않으면 기본값은 `http://localhost:8124`입니다(또는 `STATIC_SERVER_HOST` 및 `STATIC_SERVER_PORT` 환경 변수가 설정된 경우 이를 따름). 기본값으로 돌아가려면 필드를 지우세요. 빈 값은 설정되지 않은 것으로 인식됩니다.

이 설정을 업데이트한 후 변경 사항을 적용하려면 Griptape Nodes 엔진을 재시작해야 합니다.

### 예시 시나리오

**시나리오 1: ngrok을 사용한 로컬 개발**

생성된 미디어 파일에 액세스해야 하는 웹훅 연동을 테스트하고 있습니다.

1. ngrok 터널 시작: `ngrok http 8124`
1. 생성된 URL 복사(예: `https://abc123.ngrok.app`)
1. Griptape Nodes UI Settings 대화 상자 열기
1. ngrok URL로 `static_server_base_url` 설정 업데이트
1. Griptape Nodes 엔진 재시작: `gtn`

이제 워크플로우가 미디어를 생성할 때:

- 로컬 액세스: 터널 URL을 통해 작동
- 외부 서비스: ngrok URL을 통해 미디어를 가져올 수 있음
- CORS: 터널 URL에 대해 자동으로 구성됨
