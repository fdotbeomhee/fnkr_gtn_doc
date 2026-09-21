# 환경 및 내장 변수 (Environment & Builtin Variables)

## 환경 (Environment)

프로젝트 파일의 `environment` 섹션은 사용자 정의 키-값 쌍을 저장합니다. 이러한 값은 매크로 및 디렉터리의 `path_macro` 필드에서 사용할 수 있습니다.

```yaml
environment:
  RENDER_STYLE: "realistic"
  CLIENT_CODE: "ACME"
```

### 오버레이 동작

프로젝트 파일의 환경 항목은 시스템 기본값 위에 병합됩니다. 키가 기본값에 존재하는 경우 지정한 값이 이를 대체합니다. 새로운 키는 추가됩니다.

### 환경 값에서 다른 변수 참조하기

환경 값 자체도 매크로입니다. 값은 `{NAME}`을 사용하여 내장 변수, 디렉터리, 다른 프로젝트 환경 변수 또는 셸 환경 변수를 참조할 수 있습니다:

Griptape Nodes를 시작한 셸에 `SHARED_DRIVE=/mnt/renders`가 내보내기(export)되어 있다고 가정해 보겠습니다. 그러면 다음과 같이 작성할 수 있습니다:

```yaml
directories:
  outputs:
    # 셸 환경 변수를 직접 참조 — `environment:` 아래에 선언할 필요 없음
    path_macro: "{SHARED_DRIVE}/outputs"

environment:
  CLIENT_CODE: "ACME"
  # 내장 변수 참조
  PROJECT_RENDERS: "{project_dir}/renders"
  # 셸 환경 변수, 프로젝트 환경 변수 및 리터럴 텍스트 구성
  CLIENT_RENDERS: "{SHARED_DRIVE}/{CLIENT_CODE}/renders"
```

참조는 [변수 우선순위](#변수-우선순위-variable-priority)의 우선순위에 따라 재귀적으로 확인됩니다. 순환(예: `A: "{B}"` 및 `B: "{A}"`)은 감지되어 매크로 확인 오류로 표시됩니다.

#### 레거시 `$VAR` 구문

이전 버전과의 호환성을 위해 **정확히** `$NAME`인 환경 값(주변 텍스트, 접미사, 기타 매크로 없음)은 **값이 매크로에 의해 사용될 때** 운영체제 환경을 사용하여 확장됩니다:

```yaml
environment:
  OUTPUT_ROOT: "$RENDER_FARM_SHARE"  # 매크로에서 작동: {OUTPUT_ROOT} -> /mnt/renders
```

중요한 제한 사항 — `$VAR` 형식은 적용 범위가 좁고 알려진 한계가 있습니다:

- **전체 값만 가능.** 추가된 내용(예: `"$SHARED_DRIVE/outputs"`)이나 인접한 텍스트는 확장되지 **않으며** 리터럴 문자열로 처리됩니다. 매크로 확인 시 후행 문자열 전체를 시크릿 이름으로 조회하다 실패합니다.
- **프로세스가 아닌 매크로 전용.** `$VAR`는 매크로 확인 시에만 확장됩니다. 값이 `os.environ`에 기록될 때는 확장되지 **않으므로**, 하위 프로세스 및 `os.environ.get("OUTPUT_ROOT")`를 호출하는 노드는 확장된 값이 아닌 리터럴 문자열 `"$RENDER_FARM_SHARE"`를 보게 됩니다.
- **구성할 수 없음.** `$` 접두사가 붙은 값은 다른 프로젝트 환경 변수, 내장 변수 또는 디렉터리를 참조할 수 없습니다.

모든 새 프로젝트에서는 `{NAME}` 형식을 사용하는 것이 좋습니다. 깔끔하게 구성되고, 매크로 **및** `os.environ`에서 일관되게 확장되며, `environment` 값과 디렉터리 `path_macro` 필드 모두에서 작동합니다.

## 내장 변수 (Builtin variables)

내장 변수는 모든 매크로에서 자동으로 사용할 수 있습니다. 사용자가 정의하지 않으며 시스템이 런타임에 값을 제공합니다. 오버라이드할 수 없습니다.

| 변수 | 유형 | 설명 |
| --- | --- | --- |
| `project_dir` | directory | 프로젝트 기본 디렉터리의 절대 경로(`griptape-nodes-project.yml`이 포함된 폴더, 또는 프로젝트 파일이 없을 때는 작업 공간 디렉터리) |
| `workspace_dir` | directory | 작업 공간 디렉터리의 절대 경로(명시적인 작업 공간이 구성되지 않은 경우 프로젝트 디렉터리로 기본 설정됨. [작업 공간](workspace.md#구성-확인-순서-config-resolution-order) 참조) |
| `workflow_name` | string | 현재 실행 중인 워크플로의 이름 |
| `workflow_dir` | directory | 현재 워크플로 파일이 포함된 디렉터리의 절대 경로. 아직 저장되지 않은 워크플로의 경우 에디터가 폴더를 제공했을 때 해당 워크플로가 생성된 폴더 |
| `static_files_dir` | string | 정적 파일 하위 디렉터리의 이름 (설정에서 가져오며 기본값은 `staticfiles`) |

### 내장 변수 확인 방식

내장 변수는 프로젝트 파일이 로드될 때가 아니라 매크로가 평가되는 순간에 확인됩니다. 이는 다음을 의미합니다:

- `workflow_name`과 `workflow_dir`는 현재 실행 중인 워크플로를 반영합니다.
- `project_dir`는 로드된 프로젝트 파일의 실제 경로를 반영합니다.
- `workspace_dir`는 명시적인 작업 공간이 구성되지 않은 경우 프로젝트 디렉터리를 반영하고, 구성된 경우 프로젝트 인접 구성 또는 환경 변수의 값을 반영합니다.

내장 변수가 필수이지만 확인할 수 없는 경우(예: 실행 중인 워크플로가 없을 때 `workflow_name`), 매크로 확인이 오류로 실패합니다. 변수가 선택 사항(`?`로 표시됨)인 경우 해당 블록은 오류 없이 자동으로 생략됩니다.

### 상황 매크로의 내장 변수

`save_static_file` 상황은 `workflow_dir`와 `static_files_dir`를 사용합니다:

```
{workflow_dir?:/}{static_files_dir}/{file_name_base}.{file_extension}
```

`workflow_dir`를 사용할 수 있는 경우 정적 파일은 워크플로 폴더의 하위 디렉터리로 들어갑니다. 그렇지 않은 경우 `{workflow_dir?:/}` 블록이 생략되고 경로는 작업 공간 상대 경로가 됩니다.

### 저장되지 않은 워크플로

저장된 적이 없는 워크플로에는 파일이 없으므로 `workflow_dir`를 파생할 디렉터리가 없습니다. 폴더를 탐색하는 동안 워크플로를 생성하면 에디터가 엔진에 해당 폴더가 무엇인지 알려주고, `workflow_dir`는 워크플로가 저장될 때까지 해당 폴더로 응답합니다. 처음 저장하기 전에 생성한 파일은 작업 공간 루트가 아닌 해당 폴더에 저장됩니다.

워크플로가 저장되면 `workflow_dir`는 저장된 파일의 디렉터리로 전환됩니다(다른 폴더에 저장한 경우 다른 위치일 수 있음). 파일 자체는 작성된 위치에 그대로 유지되지만 `{workflow_dir}`를 기반으로 빌드된 저장된 참조는 이제 새 폴더로 확인되므로 저장 전에 생성된 출력이 바이트 단위로는 디스크에 있더라도 노드에서 누락된 것처럼 보일 수 있습니다.

에디터가 폴더를 제공하지 않은 경우 `workflow_dir`는 처음 저장할 때까지 사용할 수 없는 상태로 유지되며 위에서 설명한 대로 `{workflow_dir?:/}`는 생략됩니다.

## 변수 우선순위 (Variable priority)

매크로가 확인될 때 변수는 다음 소스에서 우선순위 순서대로 제공됩니다:

1. **내장 변수 (Builtin variables)** — 항상 우선하며 다른 소스에 의해 오버라이드될 수 없습니다.
1. **디렉터리 이름 (Directory names)** — 프로젝트의 디렉터리 정의에서 확인되며 호출자가 제공한 변수로 오버라이드될 수 없습니다.
1. **호출자 제공 변수 (Caller-supplied variables)** — 경로 확인을 요청하는 노드 또는 작업에서 전달한 값입니다.
1. **파생 변수 (Derived variables)** — 위의 변수와 프로젝트 상태에서 계산되며 상황 매크로가 확인되기 전에 주입됩니다. 호출자가 이미 해당 값을 제공한 경우 파생 변수는 관여하지 않으므로 호출자 제공 항목이 여전히 우선합니다.
1. **프로젝트 변수 (Project variables)** — 프로젝트의 `variables:` 블록에 있는 값입니다([프로젝트 변수](variables.md) 참조). 문자열 및 정수 값만 참여합니다.
1. **프로젝트 환경 변수 (Project environment variables)** — 프로젝트의 `environment:` 블록에 있는 값입니다. 재귀적으로 확인되므로 프로젝트 환경 값은 내장 변수, 디렉터리 이름, 기타 프로젝트 환경 변수 또는 셸 환경 변수를 참조할 수 있습니다.
1. **셸 환경 변수 (Shell environment variables)** — 최종 대체 수단입니다. Griptape Nodes를 시작한 셸에 설정된 모든 변수(`HOME`, `USER` 또는 사용자가 내보낸 모든 항목 포함)는 매크로에서 `{NAME}`으로 참조할 수 있습니다. 동일한 이름의 프로젝트 환경 변수가 항상 우선합니다. 셸에는 프로젝트 상태를 가려서는 안 되는 많은 부수적인 변수가 있으므로 예약된 이름(내장 변수, 디렉터리)이 셸보다 자동으로 우선합니다.

호출자가 시스템 값과 다른 내장 변수 또는 디렉터리 이름에 대한 값을 제공하려고 하면 확인이 `RESERVED_NAME_COLLISION` 오류로 실패합니다.

### 파생 변수

| 변수 | 파생 출처 | 진실의 원천(Source of truth) |
| --- | --- | --- |
| `file_extension_directory` | `file_extension` 및 프로젝트의 `file_extension_directories` 매핑 | [파일 확장자 디렉터리](file_extension_directories.md) 참조 |\n