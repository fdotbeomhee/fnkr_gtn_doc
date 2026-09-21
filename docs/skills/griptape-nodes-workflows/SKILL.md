---
name: griptape-nodes-workflows
description: 엔진의 MCP 서버를 구동하여 Griptape Nodes 워크플로우를 구축, 실행 및 검사합니다. 사용자가 노드 워크플로우 구성, 기존 워크플로우 실행, 파라미터 값 설정, 노드 간 연결 배선, 워크플로우 실행 결과 읽기를 요청할 때 사용합니다. "워크플로우 만들어줘", "플로우에 노드 추가해줘", "이 노드들 연결해줘", "플로우 실행해줘", "노드 X의 출력이 뭐야" 등의 요청에 트리거됩니다.
---

# Griptape Nodes 워크플로우 구축 가이드

이 스킬은 엔진의 MCP 서버에 대한 전체 콜드 스타트 사이클(구축 → 배선 → 실행 → 결과 읽기)과 실제 워크플로우를 실행하면서 발견된 관용구 및 주의사항을 다룹니다.

## 멘탈 모델 (Mental Model)

- **Workflow (워크플로우)**: 최상위 네임스페이스. 한 번에 **하나**만 활성화될 수 있습니다. `ClearAllObjectStateRequest`로 초기화합니다.
- **Flow (플로우)**: 워크플로우 내부의 캔버스입니다. 워크플로우에는 정확히 하나의 최상위 "캔버스" 플로우가 있습니다. 서브 플로우도 가능하지만 일반 작업에서는 거의 필요하지 않습니다.
- **Node (노드)**: 파라미터(입력, 출력, 속성)를 가진 작업 단위입니다.
- **Connection (연결)**: 두 파라미터 사이의 엣지(edge)입니다. 두 가지 종류가 있습니다:
    - **데이터 플로우 (Data flow)**: 한 노드의 타입 파라미터 → 다른 노드의 타입 파라미터. 엔진은 데이터 종속성으로부터 실행 순서를 도출하므로 일반적으로 데이터 연결만으로 충분합니다.
    - **제어 플로우 (Control flow)**: `exec_out` → `exec_in`. 두 노드가 특정 순서로 실행되어야 하지만 데이터를 공유하지 않는 경우(예: 부작용이 있는 단계, 분기)에만 필요합니다. 기본적으로는 생략합니다.
- **Current Context (현재 컨텍스트)**: 스택 구조 (workflow → flow → node). 이름을 생략하면 대부분의 요청이 "현재" 컨텍스트를 기본값으로 사용합니다.

## MCP 서버가 실제로 노출하는 인터페이스

모든 MCP 도구는 `SUPPORTED_REQUEST_EVENTS`(`src/griptape_nodes/servers/mcp.py`)에 등록된 `RequestPayload` 클래스와 1:1로 대응합니다. 각 도구에는 서버 이름 접두사가 붙으므로 `CreateNodeRequest`는 `griptape_nodes_CreateNodeRequest`로 호출할 수 있습니다. 미리 알아두어야 할 주요 특징:

- **개별 요청의 복수형 변형은 없습니다.** `CreateNodesRequest`, `CreateConnectionsRequest` 등은 존재하지 않습니다. N개의 노드를 생성하려면 N개의 `CreateNodeRequest` 호출을 보내거나 단일 `EventRequestBatch` 호출로 묶어서 전송하세요.
- **`CreateNodeRequest`는 파라미터 값을 받지 않습니다.** 파라미터 설정은 노드가 생성된 후 항상 별도의 `SetParameterValueRequest`로 수행해야 합니다. 생성 시 `parameter_values` / `inputs`와 같은 단축 속성은 제공되지 않습니다.
- **`EventRequestBatch`는 유일한 일괄 처리 도구입니다.** 이는 단일 전송 프레임에 내부 요청 목록을 순서대로 담아 보내는 도구입니다. 구축 단계의 형태를 이미 알고 있을 때 활용하세요.

## 작업 전 워크스페이스 먼저 조사하기

무언가를 구축하기 전에 현재 엔진에 실제로 무엇이 로드되어 있는지 확인하세요. 등록된 라이브러리와 노드 타입 세트는 이후 모든 단계가 참조하는 카탈로그이므로 존재하지 않는 이름을 추측하는 대신 초기에 조회를 수행하는 것이 좋습니다.

### 디스크에서 워크스페이스 디렉토리 찾기

MCP 인터페이스는 `GetConfigValueRequest`를 노출하지 않으므로 사용자 설정 파일을 직접 읽어 워크스페이스 경로를 확인합니다. macOS / Linux의 경우:

```
~/.config/griptape_nodes/griptape_nodes_config.json
```

Windows의 경우:
```
%USERPROFILE%\.config\griptape_nodes\griptape_nodes_config.json
```

확인해야 할 주요 키:
- `workspace_directory`: 절대 경로(또는 `~` 접두사) 워크스페이스 루트. 샌드박스 라이브러리가 이 디렉토리 안에 위치합니다.
- `app_events.on_app_initialization_complete.libraries_to_register`: 로컬에 등록된 `griptape_nodes_library.json` 경로 목록.

### MCP를 통해 등록된 라이브러리 조사하기

```
A. griptape_nodes_ListRegisteredLibrariesRequest()
   → 현재 로드된 라이브러리 이름 목록 (예: "Griptape Nodes Library", "Sandbox Library").

B. griptape_nodes_ListNodeTypesInLibraryRequest(library="<name>")
   → 가져올 라이브러리마다 한 번씩 호출. 반환된 노드 타입 이름은 CreateNodeRequest.node_type 및 DescribeNodeTypeRequest.node_type에 전달할 정확한 문자열입니다.

C. (선택 사항) griptape_nodes_ListCategoriesInLibraryRequest(library="<name>")
   → 대규모 라이브러리의 특정 하위 영역(예: 이미지 노드만)만 확인하려는 경우 유용합니다.
```

## EventRequestBatch: 구축 단계를 단일 왕복으로 축소

`EventRequestBatch` (MCP 도구 이름: `griptape_nodes_EventRequestBatch`)는 단일 전송 프레임에 내부 요청 목록을 순서대로 래핑합니다. 일반적인 패턴은 N개의 `CreateNodeRequest` → N개의 `SetParameterValueRequest` → N개의 `CreateConnectionRequest`를 하나의 배치로 전송하는 것입니다.

## 노드 워크플로우 구축 및 실행 절차

1. **상태 초기화**: `griptape_nodes_ClearAllObjectStateRequest()`로 기존 캔버스를 초기화합니다.
2. **노드 생성**: 사용할 노드들을 `CreateNodeRequest`로 생성합니다.
3. **파라미터 설정**: `SetParameterValueRequest`로 각 노드의 프롬프트, 모델, 설정값을 지정합니다.
4. **연결 생성**: `CreateConnectionRequest`로 노드의 출력 포트와 다음 노드의 입력 포트를 연결합니다.
5. **실행**: `griptape_nodes_RunWorkflowRequest()` 또는 `RunNodeRequest()`로 워크플로우를 실행합니다.
6. **결과 확인**: `GetNodeResultsRequest` 또는 `GetParameterValueRequest`로 최종 출력 텍스트나 에셋 경로를 확인합니다.
