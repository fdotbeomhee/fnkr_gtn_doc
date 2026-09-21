# 디렉터리 (Directories)

디렉터리는 논리적 이름과 경로 간의 매핑입니다. 모든 위치에 `outputs/renders`와 같은 경로를 하드코딩하는 대신 이름(`outputs`)을 지정하고 매크로에서 해당 이름을 사용합니다. 나중에 출력이 저장될 위치를 변경해야 할 경우 한 곳에서 디렉터리 정의만 업데이트하면 됩니다.

## 기본 디렉터리

시스템 기본값은 6개의 디렉터리를 정의합니다:

| 이름 | 기본 경로 | 설명 |
| --- | --- | --- |
| `inputs` | `inputs` | 프로젝트로 가져온 파일 — 업로드, 복사본 및 다운로드. |
| `outputs` | `outputs` | 워크플로 실행 중에 노드에 의해 생성된 파일. |
| `temp` | `temp` | 임시 스크래치 파일. 실행 간에 안전하게 삭제할 수 있습니다. |
| `griptape-nodes-previews` | `.griptape-nodes-previews` | 생성된 미리보기/썸네일 아티팩트. 소스 파일 계층 구조를 미러링합니다. |
| `griptape-nodes-metadata` | `.griptape-nodes-metadata` | 프로젝트 파일용 사이드카 메타데이터. 소스 파일 계층 구조를 미러링합니다. |
| `griptape-nodes-thumbnails` | `.griptape-nodes-thumbnails` | UI에 표시되는 워크플로 썸네일 이미지. |

모든 기본 경로는 상대 경로이며 프로젝트 기본 디렉터리를 기준으로 확인됩니다.

## 매크로에서 디렉터리 이름 사용하기

디렉터리 이름은 모든 매크로에서 변수로 사용할 수 있습니다. 매크로가 확인될 때 프로젝트 시스템은 디렉터리 이름을 구성된 경로로 자동 대체합니다:

```
템플릿:    {outputs}/{file_name_base}.{file_extension}
           ↓
확인된 경로: outputs/my_image.png
```

디렉터리 값을 직접 제공할 필요는 없습니다. 디렉터리 정의에서 가져옵니다. 디렉터리 이름은 **예약어**입니다. 디렉터리와 동일한 이름을 가진 변수를 전달하려고 하면 시스템에서 모호성을 방지하기 위해 오류와 함께 거부합니다.

## 디렉터리 경로 커스터마이즈하기

`griptape-nodes-project.yml`에서 디렉터리 경로를 오버라이드합니다:

```yaml
project_template_schema_version: "0.1.0"
name: "My Project"

directories:
  outputs:
    path_macro: "renders/final"
```

이제 모든 매크로의 `{outputs}`는 `outputs` 대신 `renders/final`로 확인됩니다.

## 디렉터리 설명 (Directory descriptions)

각 디렉터리 정의에는 선택적으로 `description`(디렉터리의 목적과 사용 방법에 대한 사람이 읽을 수 있는 설명)이 포함될 수 있습니다. 설명은 프로젝트 디렉터리를 표시하는 도구(예: GUI의 프로젝트 관리 화면)에 표시되며 직접 편집한 프로젝트 YAML을 더 쉽게 이해할 수 있도록 도와줍니다.

```yaml
directories:
  outputs:
    path_macro: "renders/final"
    description: "클라이언트에 전달할 준비가 된 최종 렌더링 결과물입니다."
```

`description`은 선택 사항이며 기본값은 `null`입니다. 기본 또는 상위 템플릿에서 상속된 설명을 지우려면 오버레이에서 `null`로 설정하세요:

```yaml
directories:
  outputs:
    description: null
```

## 새 디렉터리 추가하기

기본값에 없는 디렉터리를 추가합니다:

```yaml
directories:
  deliverables:
    path_macro: "client_deliverables"
```

추가되면 모든 매크로에서 `{deliverables}`를 사용할 수 있습니다.

## 매크로를 사용한 디렉터리 경로

`path_macro` 필드는 물결표(`~`) 확장, 매크로 구문 및 환경 변수 참조를 지원합니다:

```yaml
directories:
  downloads:
    path_macro: "~/Downloads"
```

이렇게 하면 실행되는 머신에 관계없이 `downloads` 디렉터리가 현재 사용자의 Downloads 폴더에 매핑됩니다.

```yaml
directories:
  outputs:
    path_macro: "$OUTPUT_BASE/renders"
```

환경(또는 프로젝트의 `environment` 섹션)에 `$OUTPUT_BASE`가 설정되어 있으면 경로가 확인될 때 대체됩니다.

디렉터리 경로에서 내장 변수를 참조할 수도 있습니다:

```yaml
directories:
  outputs:
    path_macro: "{workflow_dir}/renders"
```

이렇게 하면 `outputs` 디렉터리가 프로젝트 기본 디렉터리가 아닌 현재 워크플로의 위치에 상대적이 됩니다.

## 플랫폼별 경로 (Per-platform paths)

디렉터리의 `path_macro`는 단일 문자열(모든 곳에서 사용)이거나 운영체제마다 적절한 경로가 다를 때 플랫폼별 매핑일 수 있습니다. Linux, macOS, Windows 사용자가 작업 공간을 공유하고 디렉터리에 각 OS별로 서로 다른 절대 위치가 필요할 때 사용하세요.

```yaml
directories:
  scratch:
    path_macro:
      linux: "/mnt/fast-scratch"
      darwin: "/Volumes/scratch"
      windows: "D:/scratch"
      default: "{workspace_dir}/scratch"
```

확인 시 엔진은 현재 플랫폼(`linux`, `darwin`, `windows`)과 일치하는 항목을 선택합니다. 활성 플랫폼의 키가 설정되지 않은 경우 `default`로 대체됩니다. 4개의 키 중 적어도 하나는 제공되어야 하며, 그렇지 않으면 프로젝트 유효성 검사에 실패합니다.

```yaml
directories:
  models:
    path_macro:
      darwin: "~/Library/Caches/models"
      default: "{workspace_dir}/.models"
```

플랫폼별 값은 문자열 형식과 동일한 구문을 지원합니다. 물결표 확장, 환경 변수 및 매크로가 모두 플랫폼별로 동일하게 작동합니다.

이 매핑은 오버레이 병합 중에 **원자적(atomic)**입니다. 플랫폼별 매핑을 제공하는 하위 템플릿은 키별로 병합되지 않고 상위의 `path_macro`를 완전히 대체합니다. 한 플랫폼의 값을 유지하면서 다른 플랫폼의 값을 오버라이드하려면 유지하려는 모든 키를 다시 명시하세요.

## 예약된 이름 (Reserved names)

디렉터리 이름은 전체 변수 네임스페이스에서 예약되어 있습니다. 매크로 호출에서 사용자 제공 변수로 디렉터리 이름을 사용할 수 없으며, 시도할 경우 시스템에서 오류를 반환합니다. 이는 변수 이름 충돌로 인해 디렉터리 경로가 실수로 오버라이드되는 것을 방지합니다.

내장 변수(`project_dir`, `workspace_dir`, `workflow_name`, `workflow_dir`, `static_files_dir`)도 예약되어 있으며 오버라이드할 수 없습니다. [환경 및 내장 변수](environment.md)를 참조하세요.\n