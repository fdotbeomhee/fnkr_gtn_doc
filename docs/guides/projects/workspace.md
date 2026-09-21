# 작업 공간 (Workspace)

작업 공간(Workspace)은 모든 작업에 대한 루트 디렉터리입니다. 상대 파일 경로가 확인되는 시작점입니다.

## 구성 파일 (Config files)

엔진 구성은 `griptape_nodes_config.json`이라는 파일에 저장됩니다. 다음 세 가지 위치가 의미를 가집니다:

| 파일 | 목적 |
| --- | --- |
| `~/.config/griptape_nodes/griptape_nodes_config.json` | 사용자 구성 — 이 머신의 전역 설정 |
| `<project_dir>/griptape_nodes_config.json` | 프로젝트 인접 구성 — 프로젝트 파일과 함께 배포되는 공유 기본값 |
| `<workspace_dir>/griptape_nodes_config.json` | 작업 공간 구성 — 활성 프로젝트에 대한 사용자별 오버라이드 |

## 구성 확인 순서 (Config resolution order)

설정은 다음 순서로 확인됩니다(나중에 나오는 항목이 우선함):

1. 내장 기본값 (Built-in defaults)
1. 사용자 구성 (`~/.config/griptape_nodes/griptape_nodes_config.json`)
1. 프로젝트 인접 구성 (`<project_dir>/griptape_nodes_config.json`)
1. 작업 공간 구성 (`<workspace_dir>/griptape_nodes_config.json`)
1. 환경 변수 (`GTN_CONFIG_*`)

작업 공간 디렉터리가 프로젝트 디렉터리와 동일한 경우(자체 포함 프로젝트), 레이어 3과 4는 동일한 파일을 가리키며 한 번만 로드됩니다.

## 작업 공간 확인 (Workspace resolution)

프로젝트가 로드될 때 해당 작업 공간 디렉터리는 **가장 높은 우선순위부터** 다음 소스에 의해 결정되며, 값을 제공하는 첫 번째 소스가 우선권을 가집니다:

1. 프로젝트 자체의 `workspace_dir` 필드(아래의 [프로젝트 선언 작업 공간](#프로젝트-선언-작업-공간-workspace_dir) 및 [`workspace_dir` 필드](projects.md#작업-공간-디렉터리-workspace-directory) 레퍼런스 참조)
1. 사용자 구성의 `project_workspaces` 항목(아래의 [프로젝트별 작업 공간 오버라이드](#프로젝트별-작업-공간-오버라이드-per-project-workspace-overrides) 참조)
1. 환경 변수 `GTN_CONFIG_WORKSPACE_DIRECTORY`
1. 프로젝트 인접 구성의 `workspace_directory` 키
1. 명시적인 [상위 프로젝트 체인](projects.md#상위-프로젝트-parent-projects)을 탐색하여 얻은 가장 가까운 조상의 확인된 작업 공간
1. 사용자 구성의 전역 `workspace_directory`, 없으면 프로젝트 파일이 포함된 디렉터리(자동 기본값)

프로젝트 파일이 관련되지 않은 경우 작업 공간은 전역 `workspace_directory`(또는 엔진 작업 디렉터리 아래의 내장 `GriptapeNodes/` 기본값)에서 가져옵니다.

## 프로젝트별 작업 공간 오버라이드 (Per-project workspace overrides)

사용자 구성의 `project_workspaces` 설정은 프로젝트 파일 경로를 작업 공간 디렉터리 오버라이드에 매핑합니다. 공유 프로젝트를 각 머신의 서로 다른 로컬 작업 공간으로 확인해야 할 때 이를 사용하세요.

```json
{
  "project_workspaces": {
    "//NAS/Projects/ProjectA/griptape-nodes-project.yml": "/Users/collin/ProjectA/",
    "//NAS/Projects/ProjectB/griptape-nodes-project.yml": "/Users/collin/ProjectB/"
  }
}
```

키는 프로젝트 파일에 대해 확인된 절대 경로입니다. 프로젝트가 로드될 때 확인된 경로가 키와 일치하면 해당 값이 작업 공간 디렉터리로 사용됩니다.

## 프로젝트 선언 작업 공간 (`workspace_dir`)

프로젝트는 `workspace_dir` 필드를 통해 프로젝트 파일에서 자체 작업 공간 디렉터리를 지정할 수 있습니다. 이는 **최우선** 작업 공간 소스로, 사용자별 `project_workspaces` 매핑, `GTN_CONFIG_WORKSPACE_DIRECTORY` 환경 변수, 프로젝트 인접 구성, 상위 상속 및 전역 기본값을 오버라이드합니다.

각 사용자의 머신 수준 구성에 관계없이 프로젝트가 항상 고정된 작업 공간으로 확인되어야 할 때 사용하세요. (모든 사용자가 자체 구성에서 설정해야 하는) `project_workspaces`와 달리 `workspace_dir`는 프로젝트 파일 내부에서 이동하므로 프로젝트가 작업 공간을 함께 전달합니다.

**v1** 프로젝트를 생성하면 생성 UI가 `workspace_dir: "./"`를 작성하므로 기본적으로 v1 프로젝트는 자체 포함(자체 폴더가 작업 공간)됩니다. 필드를 지우거나 생략하면 프로젝트가 상위 프로젝트 또는 전역 기본값에서 작업 공간을 상속받도록 되돌아갑니다. [스키마 버전](projects.md#스키마-버전-schema-versions)을 참조하세요.

값은 단일 경로 또는 플랫폼별 매핑일 수 있습니다:

```yaml
# 단일 경로: 절대 경로 또는 이 프로젝트 파일 디렉터리에 대한 상대 경로.
workspace_dir: "./workspace"

# 또는 플랫폼별 매핑: 활성 플랫폼의 키가 사용되며, 없으면 `default`로 대체됨.
workspace_dir:
  darwin: "/Volumes/fast/ProjectA"
  windows: "D:/ProjectA"
  default: "./workspace"
```

상대 경로는 프로젝트 파일이 포함된 디렉터리를 기준으로 확인되므로(`parent_project_path`가 확인되는 방식과 동일), `workspace_dir: "./workspace"`를 사용하는 프로젝트는 이식성을 유지합니다. 프로젝트 폴더를 이동하거나 복사해도 작업 공간이 따라옵니다. 대상 디렉터리에 `griptape_nodes_config.json`이 포함되어 있을 필요는 없으며, 빈 디렉터리도 유효합니다.

전체 스키마, 플랫폼별 대체 규칙 및 상위 프로젝트와의 동작 방식은 [`workspace_dir` 필드 레퍼런스](projects.md#작업-공간-디렉터리-workspace-directory)를 참조하세요.

## 예제 시나리오

### 시나리오 1: 단독 개발자, 프로젝트 파일 없음

프로젝트 파일이 없고 프로젝트 인접 구성이나 작업 공간 구성도 없습니다. 작업 공간은 사용자 구성 또는 기본 내장값에서 가져옵니다.

```
~/.config/griptape_nodes/
  griptape_nodes_config.json    <- workspace_directory: ~/GriptapeNodes

~/GriptapeNodes/                <- workspace
  griptape_nodes_config.json    <- 선택적 작업 공간 수준 오버라이드
```

### 시나리오 2: 자체 포함 이식 가능 프로젝트

프로젝트와 구성이 동일한 디렉터리에 있습니다. 작업 공간은 프로젝트 디렉터리로 자동 기본 설정됩니다. 폴더를 USB 드라이브나 다른 머신으로 이동해도 변경할 필요가 없습니다.

```
/My_Indie_Short/
  griptape-nodes-project.yml
  griptape_nodes_config.json    <- 프로젝트 인접 및 작업 공간 구성 (동일 파일)
  inputs/
  outputs/
```

### 시나리오 3: 공유 프로젝트, 사용자별 작업 공간

프로젝트 파일이 공유 네트워크 드라이브에 있습니다. 각 사용자는 자체 사용자 구성의 `project_workspaces`를 통해 이를 자체 로컬 작업 공간에 매핑합니다. 각 사용자의 작업 공간에는 개인 오버라이드를 위한 자체 `griptape_nodes_config.json`이 있을 수 있습니다.

```
//NAS/Projects/ProjectA/
  griptape-nodes-project.yml
  griptape_nodes_config.json    <- 공유 스튜디오 기본값 (예: 모델 환경설정)
```

Collin의 사용자 구성:

```json
{
  "project_workspaces": {
    "//NAS/Projects/ProjectA/griptape-nodes-project.yml": "/Users/collin/ProjectA/"
  }
}
```

James의 사용자 구성:

```json
{
  "project_workspaces": {
    "//NAS/Projects/ProjectA/griptape-nodes-project.yml": "C:\Projects\ProjectA\"
  }
}
```

각 사용자는 공유 프로젝트 인접 구성보다 우선하는 개인 오버라이드를 위해 로컬 작업 공간 디렉터리에 `griptape_nodes_config.json`을 배치할 수 있습니다.

### 시나리오 4: 스튜디오 지정 작업 공간

프로젝트 인접 구성이 공유 작업 공간을 설정합니다. `project_workspaces` 항목이 없는 아티스트는 모두 스튜디오 기본값을 받습니다. 공유 드라이브의 작업 공간 구성은 추가 공유 설정을 유지할 수 있습니다.

```
//NAS/Projects/ProjectA/
  griptape-nodes-project.yml
  griptape_nodes_config.json    <- workspace_directory: //NAS/Workspaces/ProjectA/

//NAS/Workspaces/ProjectA/
  griptape_nodes_config.json    <- 공유 작업 공간 수준 설정
```

렌더 팜 머신은 공유 파일을 건드리지 않고 `GTN_CONFIG_WORKSPACE_DIRECTORY`를 통해 작업 공간을 오버라이드할 수 있습니다.

### 시나리오 5: 공유 엔진 구성을 사용자가 오버라이드

스튜디오가 프로젝트 인접 구성에 `log_level: "WARNING"`을 제공합니다. 개발자가 로컬에서 `DEBUG`를 원합니다. 개발자는 작업 공간 구성에 오버라이드를 배치합니다 — 작업 공간 구성(레이어 4)이 프로젝트 인접 구성(레이어 3)보다 우선합니다.

```
/Users/dev/ProjectA/
  griptape_nodes_config.json    <- {"log_level": "DEBUG"}
```

### 시나리오 6: 여러 프로젝트, 서로 다른 작업 공간

`project_workspaces`는 각 공유 프로젝트를 별도의 로컬 작업 공간에 매핑합니다. 활성 프로젝트를 전환하면 작업 공간이 전환되고 작업 공간 구성이 다시 로드됩니다.

```json
{
  "project_workspaces": {
    "//NAS/ProjectA/griptape-nodes-project.yml": "/Users/dev/ProjectA/",
    "//NAS/ProjectB/griptape-nodes-project.yml": "/Users/dev/ProjectB/"
  }
}
```

### 시나리오 7: 작업 공간 자동 검색

Griptape Nodes는 시작 시 작업 공간 디렉터리에서 `griptape-nodes-project.yml`을 찾습니다. 발견되면 프로젝트가 자동으로 로드됩니다. 이는 시나리오 2와 동일합니다 — 작업 공간과 프로젝트 디렉터리가 동일하므로 단일 `griptape_nodes_config.json`이 프로젝트 인접 구성과 작업 공간 구성 역할을 모두 수행합니다.

### 시나리오 8: 프로젝트가 자체 작업 공간을 고정

항상 고정된 작업 공간으로 확인되어야 하는 프로젝트는 `workspace_dir`를 사용하여 직접 선언하므로 사용자별 `project_workspaces` 항목이 필요하지 않습니다. 상대 경로 값을 사용하면 머신 간에 프로젝트의 이식성이 유지됩니다.

```yaml
# griptape-nodes-project.yml
name: "ProjectA"
workspace_dir: "./workspace"
```

```
/ProjectA/
  griptape-nodes-project.yml    <- workspace_dir: ./workspace
  workspace/                    <- 확인된 작업 공간 (필요 시 생성됨)
```

`workspace_dir`는 최우선 소스이므로 모든 `project_workspaces` 매핑이나 환경 변수보다 우선합니다. `ProjectA/` 폴더를 어디로든 이동해도 작업 공간은 여전히 해당 `workspace/` 하위 디렉터리로 확인됩니다.

## 경로 확인 방법 (How paths resolve)

워크플로의 상대 파일 경로는 **작업 공간 디렉터리**를 기준으로 확인됩니다. 작업 공간이 `/Users/you/workspace/`이고 상황 매크로가 `outputs/render_001.png`로 확인되는 경우 최종 절대 경로는 `/Users/you/workspace/outputs/render_001.png`입니다.

프로젝트 파일에 작성된 상대 경로는 프로젝트 YAML이 포함된 폴더를 기준으로 확인되며, 위의 `workspace_dir: ./workspace` 예제가 작업 공간을 찾는 방식이 바로 이것입니다.

라이브러리는 예외입니다: 기본적으로 작업 공간 상대 `libraries` 디렉터리 아래에 설치되고 확인되지만, 프로젝트는 작업 공간과 무관하게 [`libraries_dir`](projects.md#라이브러리-디렉터리-libraries-directory) 필드를 사용하여 라이브러리를 재배치(하고 프로젝트 트리 전체에서 공유)할 수 있습니다.

**프로젝트 기본 디렉터리**(`griptape-nodes-project.yml`이 포함된 폴더)는 `{project_dir}` 내장 변수로 노출되지만 상대 경로의 확인 기준으로는 사용되지 않습니다. 경로 관리자가 절대 경로를 매크로 형식으로 다시 매핑하고 해당 경로가 프로젝트 폴더 내부이지만 명명된 디렉터리 외부에 속하는 경우 대체(fallback)로 사용됩니다.

## 작업 공간과 프로젝트 파일

Griptape Nodes가 시작되면 작업 공간 디렉터리에서 `griptape-nodes-project.yml`을 찾습니다. 발견되면 해당 파일이 시스템 기본값 위에 병합되어 활성 프로젝트 템플릿을 생성합니다. 찾을 수 없는 경우 시스템 기본값이 사용됩니다.

프로젝트 파일 및 병합 모델에 대한 자세한 내용은 [프로젝트](projects.md)를 참조하세요.

## 요약

| 설정 | 설명 |
| --- | --- |
| `workspace_dir` | 프로젝트 선언 작업 공간; 최우선 소스 |
| `workspace_directory` | 작업을 위한 루트 디렉터리 |
| `project_workspaces` | 사용자 구성의 프로젝트별 작업 공간 오버라이드 |
| `griptape-nodes-project.yml` | 선택적 프로젝트 템플릿 파일 |
| `<project_dir>/griptape_nodes_config.json` | 프로젝트와 함께 배포되는 선택적 공유 구성 |
| `<workspace_dir>/griptape_nodes_config.json` | 선택적 사용자별 작업 공간 구성 |
| `GTN_CONFIG_WORKSPACE_DIRECTORY` | 환경 변수 오버라이드 (최우선 순위) |\n