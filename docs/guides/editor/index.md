# 에디터 (The Editor)

에디터는 Griptape Nodes 워크플로우를 생성, 실행 및 점검할 수 있는 시각적 작업 공간입니다. 캔버스 기반으로 작동하며, 노드를 캔버스로 드래그하고 서로 연결한 다음 결과를 실행할 수 있습니다. 헤더, 메뉴 모음, 두 개의 사이드바 등 창 내의 다른 모든 요소는 워크플로우 이름 지정 및 실행, 추가할 노드 탐색, 노드 실행 시 동작 점검 등 캔버스를 지원하기 위해 존재합니다.

이 문서는 에디터의 각 영역에 대해 안내합니다. 특정 작업에 대한 안내가 필요한 경우 [다음 단계](#다음-단계) 섹션에서 노드, 그룹, 워크플로우 실행, 미디어 에디터 및 라이브러리 관리에 대한 전용 가이드 링크를 참조하세요.

<!-- screenshot (#5166): full editor window with the canvas, header, left sidebar, and right sidebar all visible and labeled -->

## 캔버스 (The canvas)

캔버스는 워크플로우가 구성되는 공간입니다. 노드는 입력과 출력을 가진 상자 형태이며, 노드 간 연결은 한 노드의 출력에서 다른 노드의 입력으로 그리는 연결선입니다. 사용자가 구축하는 모든 것이 여기에 배치됩니다.

캔버스 이동 및 조작 방법:

- **팬 (화면 이동)**: `Space` 키를 누른 상태에서 드래그하거나(커서가 노드 위에 있어도 작동함), 마우스 가운데 버튼으로 드래그합니다.
- **줌 (확대/축소)**: 마우스 휠을 스크롤하거나 핀치 제스처를 사용하고, 왼쪽 상단 모서리의 줌 슬라이더(설정에서 활성화된 경우)를 사용하거나, `Z` 키를 누른 상태에서 좌우로 드래그하여 포토샵 스타일의 스크러비 줌(scrubby zoom)을 사용합니다.
- **선택**: 빈 캔버스를 드래그하여 여러 노드를 감싸는 선택 상자를 그리거나, `Shift` 키를 누른 채 개별 노드를 클릭하여 현재 선택 항목에 추가하거나 제거합니다.

모서리에 위치한 **미니맵(minimap)**(숨겨져 있는 경우 에디터 설정에서 전환 가능)은 전체 그래프를 한눈에 보여주며, 내부를 클릭하거나 드래그하여 원하는 위치로 빠르게 이동할 수 있습니다. 왼쪽 하단의 컨트롤 클러스터에는 확대/축소/화면 맞춤 버튼과 장식용 Griptape 로고 워터마크를 숨길 수 있는 **Clean Mode 전환(Toggle Clean Mode)** 버튼이 제공되며, 이는 워크플로우 스크린샷을 찍을 때 유용합니다.

빈 캔버스를 더블클릭하면 커서 위치에 **Add Node**(노드 추가) 메뉴가 열립니다. 노드가 배치된 후 수행할 수 있는 모든 작업은 [노드 작업 가이드](working_with_nodes.md)를 참조하고, 전체 캔버스 단축키 목록은 [키보드 단축키](keyboard_shortcuts.md)를 참조하세요.

<!-- screenshot (#5166): canvas with a few connected nodes, the minimap visible in a corner, and the bottom-left controls cluster -->

## 헤더 (The header)

헤더는 창 상단 전체에 걸쳐 있으며 세 개의 클러스터로 나뉩니다.

**왼쪽**에는 사이드바 토글 버튼이 [메뉴 모음](#메뉴-모음-the-menu-bar) 옆에 위치하며, 그 뒤에 워크플로우 이름이 표시됩니다. 저장되지 않은 변경 사항이 있을 때마다 이름 뒤에 `*`가 표시되며, 워크플로우가 한 번 이상 저장되면 파일 이름(`<workflow>.py`)이 아래에 나타납니다. 한 번도 저장되지 않은 워크플로우는 파일 이름 줄 없이 진행 중인 이름만 표시됩니다.

**중앙**에는 실행 컨트롤이 위치합니다:

| 버튼 | 기능 |
| --------------------- | -------------------------------------------------------------- |
| **Run Workflow** | 시작부터 전체 워크플로우를 실행합니다. |
| **Run To Selected** | 선택한 단일 노드까지 실행합니다. |
| **Run From Selected** | 선택한 단일 노드에서 실행을 시작하여 이후 단계로 진행합니다. |
| **Cancel Run** | 현재 실행 중인 워크플로우를 중지합니다. |

**Run To Selected** 및 **Run From Selected**는 정확히 하나의 노드만 선택되었을 때 활성화됩니다. "실행 가능"의 의미와 부분 실행 동작 방식에 대한 자세한 내용은 [워크플로우 실행](running_workflows.md)을 참조하세요.

**오른쪽**에는 엔진 선택기(에디터가 통신할 실행 중인 엔진 전환용), 오류 기록 드롭다운, 게시 대화 상자를 여는 **Publish Workflow**(워크플로우 게시) 버튼이 있습니다.

<!-- screenshot (#5166): header close-up showing the workflow name with unsaved indicator, the run button cluster, and the right-side engine picker -->

## 메뉴 모음 (The menu bar)

메뉴 모음은 사이드바 토글 옆의 헤더 왼쪽 클러스터에 위치하며 세 개의 메뉴로 구성됩니다.

### File (파일)

현재 워크플로우 파일을 생성, 열기, 저장 및 관리합니다:

- **New** / **Open...** — 새 워크플로우를 시작하거나 기존 워크플로우를 찾아 엽니다.
- **Rename...** — 워크플로우 이름을 바꿉니다(Griptape 기본 제공 템플릿의 경우 비활성화되며, 대신 Save As를 사용하여 편집 가능한 복사본을 생성하세요).
- **Save** / **Save As...** / **Save As New Version** — Save As New Version은 덮어쓰는 대신 새로운 버전 파일(`my_workflow_v002.py` 등)로 저장하며, 워크플로우가 한 번 이상 저장된 후에만 표시됩니다.
- **AutoSave** — 자동 저장을 켜거나 끄며, 설정으로 바로 이동할 수 있습니다.
- **Refresh Libraries** — 엔진을 재시작하지 않고 라이브러리 변경 사항을 다시 로드합니다.
- **Report Issue** — 버그 리포트를 제출합니다.
- **Exit** — 에디터를 종료합니다.
- **Delete `<workflow name>`** — 현재 워크플로우 파일을 영구적으로 삭제합니다.

### Manage (관리)

워크플로우가 의존하는 리소스의 관리 화면으로 이동합니다:

- **Model Management** — 필요한 노드에서 사용할 수 있는 모델을 관리합니다.
- **Library Management** — 노드 라이브러리를 설치, 업데이트 및 구성합니다. 전체 가이드는 [라이브러리](../libraries.md)를 참조하세요.
- **Engine Management** — 에디터가 연결할 수 있는 엔진을 관리합니다.
- **Project Management** — 전환 가능한 프로젝트를 관리합니다. [GUI에서 프로젝트 관리](../projects/gui_guide.md)를 참조하세요.

### Settings (설정)

구성 가능한 모든 항목이 **Settings** 하위 메뉴(All Settings, Agent Settings, Editor Settings, Theme Settings, Engine Settings, File System, Libraries, Library Settings, MCP Servers, API Keys & Secrets) 아래에 그룹화되어 있습니다. 그 아래에는 **Copy Path to Settings**(설정 파일 경로를 클립보드에 복사), **Show Settings Folder**(Finder/탐색기/파일 관리자에서 폴더 열기), **Reset Settings to Default**(설정을 기본값으로 초기화)의 세 가지 작업이 제공됩니다.

이 설정들은 엔진과 에디터를 구성합니다. Griptape Nodes Desktop을 실행 중인 경우, 테마, 라이선스, 로그 파일, 업데이트 채널과 같은 애플리케이션 자체 설정은 창 오른쪽 상단 모서리의 계정 메뉴에서 접근하는 [App Settings](../desktop/app_settings.md)에 위치합니다.

<!-- screenshot (#5166): the menu bar with the File menu open, showing its items and shortcuts -->

## 왼쪽 사이드바 (The left sidebar)

왼쪽 사이드바는 워크플로우에 추가할 노드와 라이브러리를 찾는 공간입니다. 탭 상단에는 **Favorites**(즐겨찾기) 섹션이 있어 매번 검색할 필요 없이 노드에 별표를 표시하여 고정할 수 있으며, 그 아래에는 활성 탭을 필터링하는 검색 상자가 있습니다.

검색 상자 아래에는 두 개의 탭이 있습니다:

- **Nodes** — 기본 내장 라이브러리와 설치된 라이브러리 모두에서 제공되는 사용 가능한 모든 노드를 카테고리별로 구성하여 표시합니다.
- **Libraries** — 동일한 노드들을 카테고리 대신 출처 라이브러리별로 정리하여 보여줍니다. 이는 탐색 뷰이며, 라이브러리 자체를 설치, 업데이트 또는 제거하려면 **Manage → Library Management**를 사용하세요([라이브러리](../libraries.md) 참조).

두 탭 중 어느 곳에서든 노드를 캔버스로 드래그하여 워크플로우에 추가할 수 있습니다. 헤더의 사이드바 토글 버튼(또는 단축키)을 클릭하면 사이드바를 좁은 아이콘 레일로 접거나 완전히 닫을 수 있습니다.

<!-- screenshot (#5166): left sidebar expanded, showing the Favorites section, search box, and the Nodes tab's category tree -->

## 오른쪽 사이드바 (The right sidebar)

오른쪽 사이드바에는 **Sidebar Panels**(사이드바 패널)이라는 탭 패널 세트가 포함되어 있습니다. 닫기 버튼 옆의 드롭다운(점 세 개 아이콘)을 사용하여 패널을 표시하거나 숨길 수 있습니다. 각 항목에는 눈 아이콘이 표시되어 현재 표시되는 패널을 확인할 수 있습니다. 여기서 패널을 숨겨도 내용이 삭제되지 않으며, 다음에 다시 활성화하면 탭이 다시 나타납니다.

네 가지 패널:

- **Chat** — 에디터 내에서 에이전트 스레드와 직접 대화하며, 모델/제공자 및 연결된 MCP 서버 선택기를 제공합니다.
- **Code** — 워크플로우의 생성된 Python 코드를 구문 강조 에디터로 표시하며 검색 및 실행이 가능합니다.
- **Logs** — 심각도(Debug, Info, Warning, Error, Critical)별로 필터링 및 검색이 가능한 실행 로그 스트림입니다.
- **Properties** — 캔버스에서 현재 선택한 노드(또는 여러 노드)의 파라미터를 표시합니다. 각 노드의 입력을 구성하는 곳이므로 워크플로우 구성 후 가장 많은 시간을 보내는 영역입니다.

사이드바가 닫혀 있는 경우 캔버스 오른쪽 상단 모서리 근처의 플로팅 버튼을 눌러 다시 열 수 있습니다.

<!-- screenshot (#5166): right sidebar showing the panel tabs (Chat, Code, Logs, Properties) with the panel-visibility dropdown open -->

## 다음 단계

- [키보드 단축키](keyboard_shortcuts.md) — 작업 목적별로 정리된 에디터의 모든 단축키 목록입니다.
- [노드 작업 가이드](working_with_nodes.md) — 노드 추가, 연결, 이름 변경, 잠금 및 복제에 대한 설명입니다.
- [노드 그룹](node_groups.md) — 노드 그룹화 및 그래프 정리 방법입니다.
- [워크플로우 실행](running_workflows.md) — 전체 실행, 특정 노드까지 실행, 특정 노드부터 실행의 차이점을 설명합니다.
- [미디어 뷰어 및 에디터](media_editors.md) — 이미지, 비디오 및 기타 미디어 파라미터를 위한 내장 뷰어 및 에디터 가이드입니다.
- [모델 및 라이브러리 관리](managing_models_and_libraries.md) — Model Management 및 Library Management 화면에 대한 심층 가이드입니다.
