# Griptape Nodes Retained Mode 스크립팅

"Retained Mode"는 Griptape Nodes와 상호작용하기 위한 Python 스크립팅 인터페이스를 제공합니다. 이를 통해 사용자는 단순화된 Python API를 사용하여 노드, 파라미터, 연결 및 플로우를 생성, 수정 및 관리할 수 있습니다.

> **참고:** RetainedMode의 실제 임포트 명령어는 다음과 같습니다:
>
> ```python
> from griptape_nodes.retained_mode import RetainedMode as cmd
> ```
>
> GUI의 스크립트 에디터에서는 편의를 위해 이 임포트가 이미 자동으로 처리되어 있으므로 `cmd.`를 직접 자유롭게 사용할 수 있습니다.

## 스크립트 작업 방식

스크립트를 개발하고 실행하는 두 가지 기본 방법:

1. **스크립트 에디터 사용**: Griptape Nodes 스크립트 에디터에서 직접 스크립트를 작성하고 즉시 실행하여 플로우를 수정하거나 제어합니다.
2. **외부 스크립트 임포트**: 재사용 가능한 스크립트 모듈을 외부 파일로 유지하고, 스크립트 에디터에서 Python의 import 시스템을 통해 임포트하여 실행합니다.

스크립트 에디터는 모든 스크립팅 작업의 주요 진입점입니다.

스크립트는 반복적인 작업을 자동화하는 데 매우 유용합니다: 속성 및 연결이 포함된 노드 복제, 노드 값 내보내기 및 다른 플로우로 가져오기, 사전 정의된 노드와 연결을 갖춘 전체 플로우를 프로그래밍 방식으로 생성하기, 또는 루프를 통해 많은 노드에 대한 일괄 작업 수행. 또한 Retained Mode를 다른 Python 라이브러리(pandas, numpy, requests 등)와 결합하여 스크립트의 기능을 확장할 수 있습니다.

아래 내용은 플로우 관리, 노드 작업, 파라미터 관리, 연결, 플로우 실행에 대한 API 레퍼런스입니다.

---

## 플로우 작업 (Flow Operations)

### `create_flow`

Griptape 시스템 내에 새 플로우를 생성합니다.

```python
cmd.create_flow(flow_name=None, parent_flow_name=None)
```

#### 인수 (Arguments)

| 이름 | 인수 유형 | 필수 여부 |
| --- | :---: | :---: |
| `flow_name` | string | 선택 (⚪) |
| `parent_flow_name` | string | 선택 (⚪) |

#### 반환값
플로우 생성 상태가 포함된 `ResultPayload` 객체

#### 설명
지정된 이름으로 새 플로우를 생성합니다. `parent_flow_name`이 제공되면 새 플로우가 지정된 상위 플로우의 하위 플로우(Sub-Flow)로 생성됩니다.

---

### `delete_flow`

기존 플로우를 삭제합니다.

```python
cmd.delete_flow(flow_name)
```

#### 인수

| 이름 | 인수 유형 | 필수 여부 |
| --- | :---: | :---: |
| `flow_name` | string | **필수 (⚫)** |

---

### `list_flows`

현재 워크플로우에 존재하는 모든 플로우의 목록을 반환합니다.

```python
cmd.list_flows()
```

---

## 노드 작업 (Node Operations)

### `create_node`

플로우 내에 새 노드를 생성합니다.

```python
cmd.create_node(node_type, node_name=None, flow_name=None, metadata=None)
```

#### 인수

| 이름 | 인수 유형 | 필수 여부 |
| --- | :---: | :---: |
| `node_type` | string | **필수 (⚫)** |
| `node_name` | string | 선택 (⚪) |
| `flow_name` | string | 선택 (⚪) |
| `metadata` | dict | 선택 (⚪) |

#### 예시

```python
# Agent 노드 생성
cmd.create_node(node_type="Agent", node_name="my_agent")
```

---

### `delete_node`

플로우에서 지정된 노드를 삭제합니다.

```python
cmd.delete_node(node_name, flow_name=None)
```

---

### `list_nodes`

지정된 플로우(또는 현재 플로우)의 모든 노드 목록을 반환합니다.

```python
cmd.list_nodes(flow_name=None)
```

---

## 파라미터 작업 (Parameter Operations)

### `set_parameter_value`

노드의 파라미터 값을 설정합니다.

```python
cmd.set_parameter_value(node_name, param_name, value, flow_name=None)
```

#### 예시

```python
cmd.set_parameter_value(
    node_name="my_agent",
    param_name="prompt",
    value="사자에 대한 4줄짜리 시를 써줘",
)
```

---

### `get_parameter_value`

노드의 특정 파라미터 값을 조회합니다.

```python
value = cmd.get_parameter_value(node_name, param_name, flow_name=None)
```

---

## 연결 작업 (Connection Operations)

### `create_connection`

두 노드의 파라미터 사이에 연결을 생성합니다.

```python
cmd.create_connection(
    source_node, source_param, target_node, target_param, flow_name=None
)
```

#### 예시

```python
cmd.create_connection(
    source_node="text_input_1",
    source_param="text",
    target_node="my_agent",
    target_param="prompt",
)
```

---

### `delete_connection`

두 파라미터 사이의 연결을 삭제합니다.

```python
cmd.delete_connection(
    source_node, source_param, target_node, target_param, flow_name=None
)
```

---

## 실행 작업 (Execution Operations)

### `run_workflow`

전체 워크플로우를 실행합니다.

```python
cmd.run_workflow(flow_name=None)
```

---

### `run_node`

특정 노드만 단독으로 실행합니다.

```python
cmd.run_node(node_name, flow_name=None)
```
