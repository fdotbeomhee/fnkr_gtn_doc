# 에셋 및 출력 (Assets and Outputs)

워크플로우의 노드가 이미지, 비디오, 오디오 클립 또는 기타 파일을 생성할 때, 해당 파일은 디스크의 특정 위치에 저장되고 에디터에서 접근할 수 있어야 미리보기 및 다운로드가 가능합니다. 이 페이지에서는 생성된 파일이 기본적으로 어디에 저장되는지, 이를 어떻게 변경하는지, 그리고 파일이 처음에 워크플로우로 어떻게 유입되는지(업로드, 드래그 앤 드롭)에 대해 설명합니다.

## 생성된 파일이 저장되는 위치

노드는 하드코딩된 위치에 파일을 직접 쓰지 않습니다. 모든 저장은 **프로젝트(project)**를 거칩니다. 프로젝트는 이름이 지정된 디렉터리 세트(`inputs`, `outputs`, `temp` 등)와 특정 종류의 파일이 어느 디렉터리에 어떤 이름으로 저장될지를 결정하는 **시츄에이션(situations)**이라는 파일 저장 시나리오 세트를 정의합니다. 기본 프로젝트 설정에서는 다음과 같이 작동합니다:

- 노드의 렌더링된 출력(이미지, 렌더링된 비디오, 생성된 오디오)은 [`save_node_output`](projects/situations.md#savenodeoutput) 시츄에이션을 통해 프로젝트의 `outputs` 디렉터리에 저장됩니다.
- 에디터로 드래그하거나 프로젝트 외부에서 복사한 파일은 [`copy_external_file`](projects/situations.md#copyexternalfile) 시츄에이션을 통해 `inputs` 디렉터리에 저장됩니다.
- 노드가 처리 중에 작성하고 이후 정리하는 임시 스크래치 파일은 `temp`로 이동합니다.
- 에디터가 사용자를 위해 생성하는 썸네일 및 미리보기 이미지는 숨겨진 `.griptape-nodes-previews` / `.griptape-nodes-thumbnails` 폴더로 이동합니다.

이러한 경로 중 어떤 것도 엔진에 고정되어 있지 않습니다. 이 경로들은 프로젝트에서 제공되며, 프로젝트 파일은 이 중 어느 것이든 다른 위치(공유 드라이브, 다른 하위 폴더, 플랫폼별 경로)로 지정하거나 해당 이름의 파일이 이미 존재할 때 수행할 작업을 변경할 수 있습니다. 이름이 지정된 디렉터리의 전체 목록은 [디렉터리](projects/directories.md)를, 각 파일 종류가 따르는 저장 규칙은 [시츄에이션](projects/situations.md)을, 이 모든 것을 커스터마이징하는 방법은 [프로젝트](projects/index.md)를 참조하세요.

<!-- screenshot (#5166): the default project's folder layout in a file manager, with inputs and outputs visible -->

## 에디터가 파일을 미리보고 다운로드하는 방식

엔진이 파일을 표시하거나 다운로드할 수 있도록 해야 할 때, 브라우저에 원시 파일 시스템 경로를 직접 전달하지 않고 URL을 생성합니다. 작업 공간(workspace) 내부 파일의 경우, 대개 로컬 정적 파일 서버로 향하는 직접 URL(기본값: `http://localhost:8124/workspace/...`)이 됩니다. 그 외의 모든 항목(작업 공간 외부 경로, `file://` 경로, `{outputs}/render.png`와 같은 매크로 경로)의 경우, 엔진은 요청 시 수명이 짧은 사전 서명된(presigned) 다운로드 URL을 생성합니다. 어느 방식이든 메커니즘은 동일합니다. 에디터가 URL을 요청하면 엔진이 파일이 실제로 존재하는 위치에 대해 이를 확인(resolve)하고, 브라우저는 해당 URL을 사용하여 파일을 가져오거나 다운로드합니다. 이러한 URL은 사용자가 직접 관리하지 않습니다. 필요할 때 요청에 따라 생성되며 세션 외부에서 공유되거나 오래 유지되도록 설계되지 않았습니다.

작업 공간에 `staticfiles` 폴더가 보일 수도 있습니다(정확한 이름은 `static_files_directory` 설정에 따라 지정됨). 이는 대부분의 노드가 더 이상 사용하지 않는 이전 저장 경로에 속하지만(대신 위에서 설명한 프로젝트 시스템 디렉터리를 통해 저장됨), 엔진의 정적 파일 API를 통해 저장된 파일은 여전히 해당 위치에 저장됩니다.

## 워크플로우로 파일 업로드하기

외부 파일(참조 사진, 소스 비디오, 오디오 클립)을 워크플로우로 가져오는 것은 반대 방향으로 동일하게 작동합니다. 노드로 파일을 드래그하거나 노드의 파일 선택기를 사용하면 에디터가 해당 파일을 프로젝트로 업로드합니다. 파일이 저장되는 위치는 프로젝트의 [`copy_external_file`](projects/situations.md#copyexternalfile) 시츄에이션에 의해 결정됩니다. 기본 프로젝트에서는 `inputs` 디렉터리로 지정되며, 파일 확장자가 인식되면 파일 유형별 하위 폴더(`images`, `videos`, `audio`, `text`)로 그룹화됩니다. 동일한 이름의 파일이 이미 있는 경우 새 업로드 파일은 덮어쓰는 대신 번호가 붙은 접미사(`photo_001.png`)를 받습니다.

<!-- screenshot (#5166): dragging a file onto a node's file parameter, showing the drop target highlighted -->

내부적으로 이것은 브라우저가 파일 시스템과 직접 통신하는 대신 2단계 핸드오프로 이루어집니다. 에디터가 엔진에 업로드 URL을 요청한 다음, 해당 URL로 파일의 바이트를 PUT 요청으로 전송합니다.

## 관련 페이지

- [디렉터리](projects/directories.md) — 이름이 지정된 디렉터리(`inputs`, `outputs`, `temp` 등)의 전체 목록 및 경로 커스터마이징 방법.
- [시츄에이션](projects/situations.md) — 노드가 작성하는 모든 파일 종류 이면의 저장 규칙(파일이 이동하는 위치, 이름 충돌 시 동작).
- [시퀀스](projects/sequences.md) — 번호가 매겨진 출력 파일 디렉터리(`render.0001.exr`, `render.0002.exr`, …)를 단일 순서 세트로 다시 읽기.
- [매크로](projects/macros.md) — 저장 위치를 커스터마이징하려는 경우 시츄에이션이 파일 경로를 작성하는 데 사용하는 경로 템플릿 구문(`{outputs}/{file_name_base}.{file_extension}`).
- [설정 레퍼런스](../reference/configuration_reference.md) — 정확한 설정값(`static_files_directory`, `workspace_directory` 등), 기본값 및 환경 변수 오버라이드.
