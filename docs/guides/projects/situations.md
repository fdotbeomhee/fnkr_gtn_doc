# 시츄에이션 (Situations)

시츄에이션(Situation)은 명명된 파일 저장 시나리오입니다. 다음을 정의합니다:

- 파일이 저장될 **위치** (매크로 템플릿 사용)
- 해당 위치에 파일이 이미 존재할 때 처리하는 **방식** (충돌 정책 사용)
- 저장에 실패할 경우 **수행할 작업** (선택적 폴백 시츄에이션 사용)

노드가 파일을 저장해야 할 때 현재 속한 시츄에이션(예: `save_node_output`)을 지정하면, 프로젝트 시스템이 해당 시츄에이션의 매크로를 사용하여 경로를 해석하고 정책을 적용합니다.

## 충돌 정책 (Collision policies)

| 정책          | 동작                                                                                                                                                                                                                                                                                           |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `create_new`  | 충돌하지 않는 파일 이름을 찾을 때까지 파일 이름의 카운터를 증가시킵니다. 매크로에는 `{_index?:NN}`(선택 사항 — 첫 저장 시 생략, 충돌 시 인덱스 부여) 또는 `{_index:NN}`(필수 — 첫 저장부터 인덱스 부여)을 포함할 수 있습니다. 둘 다 없으면 시스템은 충돌 시 해석된 파일 이름 뒤에 `_1`, `_2`, …를 추가합니다. |
| `overwrite`   | 확인을 묻지 않고 기존 파일을 덮어씁니다.                                                                                                                                                                                                                                                       |
| `fail`        | 파일이 이미 존재하는 경우 작업을 중단하고 오류를 보고합니다.                                                                                                                                                                                                                                   |

`create_dirs` 필드는 중간 상위 디렉터리를 자동으로 생성할지(`true`, `mkdir -p`와 유사) 아니면 상위 디렉터리가 없을 때 오류를 발생시킬지(`false`)를 제어합니다.

## 폴백 (Fallbacks)

시츄에이션은 폴백(대체) 시츄에이션을 지정할 수 있습니다. 기본 시츄에이션이 매크로를 해석할 수 없는 경우(예: 필수 변수 누락 등) 시스템은 폴백을 시도합니다. 기본 `save_file` 시츄에이션은 대부분의 다른 시츄에이션에서 사용하는 최소한의 폴백입니다.

## 기본 시츄에이션

### `save_file`

```
macro:  {file_name_base}{_index?:03}.{file_extension}
policy: create_new, create_dirs: true
```

프로젝트 루트(또는 호출자의 경로 컨텍스트가 지정한 위치)에서의 일반적인 파일 저장입니다. 대부분의 다른 시츄에이션에 대한 폴백 역할을 합니다. `{_index?:03}` 변수는 0으로 패딩된 선택적 변수로, 첫 저장 시에는 생략되고 충돌 시 `001`, `002`, …로 진행됩니다(시퀀스 전체에 걸쳐 패딩 너비 유지).

### `copy_external_file`

```
macro:    {inputs}/{node_name?:_}{parameter_name?:_}{file_name_base}{_index?:03}.{file_extension}
policy:   create_new, create_dirs: true
fallback: save_file
```

사용자가 외부 파일을 프로젝트로 복사하거나 드래그하여 가져올 때 사용됩니다. 파일은 `inputs` 디렉터리에 배치됩니다. 파일의 출처를 식별하기 위해 노드 이름과 파라미터 이름이 선택적 접두사로 앞에 추가됩니다.

**예시:**

```
node_name="LoadImage", parameter_name="source", file_name_base="photo", file_extension="jpg"
→ inputs/LoadImage_source_photo.jpg

node_name 제공 안됨, file_name_base="photo", file_extension="jpg"
→ inputs/photo.jpg
```

### `download_url`

```
macro:    {inputs}/{sanitized_url}
policy:   overwrite, create_dirs: true
fallback: save_file
```

노드가 URL에서 파일을 다운로드할 때 사용됩니다. URL은 안전한 파일 이름으로 정리(sanitize)됩니다. 동일한 URL에서 다운로드된 파일은 복제되지 않고 덮어쓰기됩니다.

### `save_node_output`

```
macro:    {outputs}/{sub_dirs?:/}{node_name?:_}{file_name_base}{_index?:03}.{file_extension}
policy:   create_new, create_dirs: true
fallback: save_file
```

노드가 출력을 생성하고 저장할 때 사용됩니다. 파일은 `outputs` 디렉터리에 저장됩니다. 선택적 하위 디렉터리(`{sub_dirs?:/}`)를 사용하여 outputs 내부를 중첩 구성할 수 있습니다. 노드 이름은 선택적 접두사입니다.

**예시:**

```
outputs="outputs", node_name="ImageGen", file_name_base="render", _index=1, file_extension="png"
→ outputs/ImageGen_render001.png

sub_dirs="lighting/pass_a", node_name="ImageGen", file_name_base="render", file_extension="exr"
→ outputs/lighting/pass_a/ImageGen_render.exr
```

### `save_preview`

```
macro:    {previews}/{drive_volume_mount?:/}{source_relative_path?:/}{source_file_name}.{preview_format}
policy:   overwrite, create_dirs: true
fallback: save_file
```

미리보기 썸네일을 생성할 때 사용됩니다. 미리보기는 각 소스 파일이 정확히 하나의 미리보기를 갖도록 소스 파일의 디렉터리 계층 구조를 미러링합니다. 미리보기는 버전 관리되지 않고 덮어씁니다. `previews` 디렉터리의 기본값은 `.griptape-nodes-previews`(숨김 폴더)입니다.

### `save_static_file`

```
macro:    {workflow_dir?:/}{static_files_dir}/{file_name_base}.{file_extension}
policy:   overwrite, create_dirs: true
fallback: save_file
```

정적 파일 관리자가 정적 에셋을 저장할 때 사용됩니다. 파일은 현재 워크플로 디렉터리의 `static_files_dir` 하위 디렉터리에 저장됩니다. 재생성 시 기존 파일을 덮어씁니다.

### `save_temp_file`

```
macro:    {temp}/{node_name?:_}{file_name_base}{_index?:03}.{file_extension}
policy:   overwrite, create_dirs: true
fallback: save_file
```

노드가 처리 중에 중간 파일이나 임시 스크래치 파일을 작성해야 할 때 사용됩니다(예: 색 공간 변환 단계 사이에 기록되는 임시 EXR). 파일은 `temp` 디렉터리에 저장되며 사용 후 노드에서 삭제해야 합니다.

### `save_workflow`

```
macro:    {workspace_dir}/{sub_dirs?:/}{file_name_base}.{file_extension}
policy:   overwrite, create_dirs: true
fallback: save_file
```

워크플로가 저장될 때 사용됩니다. 워크플로 파일은 선택적 `{sub_dirs?:/}` 접두사를 통해 하위 디렉터리 계층 구조를 유지하면서 워크스페이스 루트에 저장됩니다. 워크플로를 저장하면 버전을 새로 생성하지 않고 기존 파일을 덮어씁니다. 번호가 매겨진 시퀀스로 저장하려면 아래의 [`create_versioned_workflow`](#createversionedworkflow)를 참조하세요.

**예시:**

```
workspace_dir="/projects/demo", file_name_base="my_workflow", file_extension="py"
→ /projects/demo/my_workflow.py

sub_dirs="archived", file_name_base="my_workflow", file_extension="py"
→ /projects/demo/archived/my_workflow.py
```

### `create_versioned_workflow`

```
macro:    {workspace_dir}/{sub_dirs?:/}{file_name_base}_v{_index:03}.{file_extension}
policy:   create_new, create_dirs: true
fallback: save_file
```

버전 관리 저장 의도로 워크플로가 저장될 때 사용됩니다. 저장할 때마다 시퀀스에서 다음 패딩 인덱스를 가진 새 파일(`my_workflow_v001.py`, `my_workflow_v002.py`, …)이 생성되므로 사용자는 이전 작업을 덮어쓰지 않고 스냅샷을 유지할 수 있습니다.

버전 증가는 **매크로 기반**으로 실행됩니다. 버전 지정 저장이 실행되면 엔진은 이전 저장 경로를 이 시츄에이션의 매크로와 역방향 매칭하여 바인딩된 패딩 슬롯을 포함하여 매크로가 정의한 모든 변수를 추출합니다. 다음 저장은 해당 변수를 그대로 재사용하며, 충돌 탐색을 통해 기존 파일을 지나 패딩 인덱스를 증가시킵니다. 버전 접미사에 대해 하드코딩된 것이 없으므로 매크로 커스터마이징(예: `_v{_index:03}`을 `.{_index:04}`로 변경)도 정상 작동합니다. 새로운 패턴이 순방향 저장과 역방향 매칭 모두에 대한 규약이 됩니다.

> **팁:** 커스텀 프로젝트에서는 자동 인덱스 슬롯을 더 명시적인 `_v{###}` 구문으로 전환할 수 있습니다([시퀀스 슬롯 (`{###}`)](macros.md#시퀀스-슬롯) 참조). 기본 3자리 숫자의 경우 `{_index:03}`과 동일하게 동작하며, `999`를 넘어갈 때 0 패딩을 유지하는 대신 자연스럽게 4자리 이상으로 확장됩니다.

이 시츄에이션은 `SaveWorkflowRequest`에 `create_versioned=True`를 전달하여 API 레이어에서 선택되며, UI에서는 별도의 메뉴 항목(예: "Save New Version")으로 제공됩니다. 자동 인덱스 규약에 대해서는 [매크로 — 숫자 패딩](macros.md#숫자-패딩)을 참조하세요.

> **참고**: `save_workflow`를 수정하여 `create_versioned_workflow` + 플래그 대신 `create_new`를 직접 사용하면 저장 시 경고가 발생합니다. 해당 구성도 작동하여 첫 번째 저장은 `_v001`에 저장되지만, 이후의 모든 저장은 제자리 덮어쓰기 분기를 타서 `_v002`로 증가하는 대신 `_v001`에 다시 기록됩니다. 진정한 버전 관리를 위해서는 `create_versioned_workflow`를 사용하세요.

**예시:**

```
workspace_dir="/projects/demo", file_name_base="my_workflow", file_extension="py"
  첫 번째 저장 → /projects/demo/my_workflow_v001.py
  두 번째 저장 → /projects/demo/my_workflow_v002.py
  세 번째 저장 → /projects/demo/my_workflow_v003.py
```

## 노드가 시츄에이션을 사용하는 방식

노드의 시츄에이션은 사용자가 아닌 노드 작성자가 선택합니다. 파일을 저장하는 노드는 `ProjectFileParameter`를 사용하며, 해당 파라미터가 빌드될 때 시츄에이션 이름이 내장됩니다. 노드 전면에는 **시츄에이션 필드가 표시되지 않습니다**. 노드에는 파일 이름 파라미터(주로 **Output File**이라고 함)가 표시되고, 시츄에이션은 그 뒤에서 작동합니다.

파일 이름 파라미터가 바로 `ProjectFileParameter`입니다. 여기에 입력하는 모든 내용은 `file_name_base` 및 `file_extension` 변수가 되며, 시츄에이션의 매크로가 파일이 실제로 저장될 위치를 결정합니다. 따라서 `save_node_output`을 사용하는 노드에 `render.png`를 입력하면 프로젝트 루트의 `render.png`가 아니라 `outputs/MyNode_render.png`가 생성됩니다. 노드는 파일 이름 요소를 제공하고, 프로젝트 시스템은 나머지 모든 것(디렉터리 경로, 내장 변수)을 제공합니다.

거의 모든 생성 및 저장 노드는 `save_node_output`을 사용합니다. 다른 시츄에이션들은 해당 이름이 가리키는 시스템 부분에서 사용됩니다. 파일을 드래그하여 가져오면 `copy_external_file`, URL 다운로드는 `download_url`, 썸네일은 `save_preview`, 워크플로 저장은 `save_workflow`를 사용합니다.

### 노드가 사용하는 시츄에이션 확인하기

노드의 파일 이름 파라미터 위에 마우스를 올립니다. 툴팁에 시츄에이션 이름이 표시됩니다. 예:

```
Output filename (uses 'save_node_output' situation template)
```

모든 노드와 해당 시츄에이션을 나열하는 별도의 패널이 없으므로, 툴팁이 특정 노드를 확인하는 신뢰할 수 있는 방법입니다. 커스텀 노드의 소스 코드에서는 `ProjectFileParameter`에 전달되는 `situation=` 인수입니다.

### 단일 노드에서 시츄에이션 오버라이드하기

프로젝트 파일을 편집하지 않고 단일 노드가 쓰는 위치를 변경하려면 노드의 파일 이름 파라미터에 있는 **톱니바퀴(cog)** 버튼을 클릭하세요. 그러면 노드의 시츄에이션과 현재 파일 이름이 미리 채워진 **File Output Settings** 노드가 생성되어 해당 파라미터에 연결됩니다.

File Output Settings 노드는 시츄에이션이 숨겨두었던 항목을 노출하며 각 필드를 변경할 수 있습니다:

- **Situation**: 프로젝트 파일의 커스텀 시츄에이션을 포함한 모든 시츄에이션을 선택할 수 있습니다. 변경하면 아래의 매크로와 충돌 정책이 다시 로드됩니다.
- **Macro**: 이 연결에 대해 편집 가능한 경로 템플릿입니다.
- **If File Exists**: 충돌 정책(Increment Version, Overwrite Existing 또는 Abort / Error)을 지정합니다.
- **Auto Create Path**: 누락된 상위 디렉터리를 자동 생성할지 여부를 설정합니다.

여기서 설정한 모든 내용은 연결된 해당 노드에만 적용됩니다. *모든* 노드가 쓰는 위치를 변경하려면 프로젝트 파일에서 시츄에이션을 직접 편집하세요.

## 커스텀 시츄에이션 추가하기

예시는 [커스터마이징 가이드](customization.md)를 참조하세요.

프로젝트 파일에서 `save_node_output`과 같은 기본 시츄에이션을 재정의하면 노드를 편집할 필요 없이 해당 시츄에이션을 사용하는 모든 노드의 대상 위치가 변경됩니다. *새로운* 시츄에이션을 추가하는 것은 이를 선택하는 대상이 있을 때만 적용됩니다. 즉, `situation=`을 전달하는 커스텀 노드이거나 이를 가리키는 **File Output Settings** 노드가 있어야 합니다.
