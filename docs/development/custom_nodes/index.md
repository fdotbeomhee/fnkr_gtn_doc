# 노드 개발 가이드 (Developing Nodes)

이 섹션은 Griptape Nodes를 위한 커스텀 노드를 제작하는 개발자를 위한 종합 문서입니다.

!!! tip "AI 어시스턴트 및 코딩 에이전트를 위한 안내"

    이 섹션의 모든 문서는 AI 코딩 어시스턴트를 위해 후처리된 마크다운으로 제공됩니다. 전체 머신 판독 가능 인터페이스 색인은 [에이전트 안내서](../../for_agents.md)를 참조하세요.

    - **시작하기**: [Markdown](https://docs.griptapenodes.com/en/stable/development/custom_nodes/getting_started/index.md)
    - **개요** (현재 페이지): [Markdown](https://docs.griptapenodes.com/en/stable/development/custom_nodes/index.md)
    - **예제 코드**: [Python 예제 보기](https://raw.githubusercontent.com/griptape-ai/griptape-nodes/main/docs/development/custom_nodes/example_control_node.py)

    **사용법:** AI 어시스턴트에게 다음과 같이 지시하세요:
    `"이 노드 개발 가이드를 읽고: [URL] 커스텀 노드를 만드는 것을 도와줘"`

## 소개

Griptape Nodes는 시각적 프로그래밍을 통해 사용자가 복잡한 AI 워크플로우를 구축할 수 있도록 지원하는 모듈형 워크플로우 컴포넌트입니다. 이 섹션에서는 견고하고 사용자 친화적인 노드를 만들기 위한 기본 개념과 고급 패턴을 모두 다룹니다.

노드 개발이 처음이라면 [시작하기 가이드](getting_started.md)부터 살펴보세요. 노드 개발 생태계에 대한 입문용 안내와 첫 노드 구축 과정을 제공합니다.

노드는 BaseNode의 하위 클래스를 상속합니다:

- **DataNode**: 데이터 처리 작업용
- **ControlNode**: exec_in/out을 사용한 흐름 제어용
- **StartNode**: 워크플로우 초기화용
- **EndNode**: 워크플로우 종료용

## 핵심 개념

### 기본 클래스 (Base Classes)

- **DataNode**: 실행 흐름 제어 없이 데이터를 처리합니다. 데이터를 동기식으로 변환하거나 전달하는 노드에 사용하며 입력이 충족되면 즉시 처리됩니다.
- **ControlNode**: exec_in/exec_out 연결을 통해 실행 흐름을 관리합니다. 외부 API를 호출하거나 장기 실행 작업을 수행하는 노드에 사용합니다. 비동기 작업의 경우 `async def aprocess()`를 재정의하거나 `AsyncResult`를 사용하여 블로킹 작업을 백그라운드 스레드에 넘깁니다. API를 호출하고 결과를 폴링하는 노드는 반드시 ControlNode여야 합니다.
- **StartNode**: 워크플로우의 진입점
- **EndNode**: 워크플로우의 종료점

### 파라미터 (Parameters)

Parameter 클래스를 통해 입력, 출력, 속성을 정의합니다. 파라미터는 다음을 지원합니다:

- 타입 유효성 검사
- UI 맞춤 설정
- 연결 제약 조건
- 기본값
- 트레이트 (Options, Slider, Button, ColorPicker 등)

자세한 내용은 [파라미터](parameters.md) 문서를 참조하세요.

### Process 메서드

`process()` 메서드는 노드의 핵심 로직을 포함합니다. 결과값은 `self.parameter_output_values`에 설정합니다. 비동기 작업의 경우 `async def aprocess()`를 재정의하세요([실행 및 수명 주기](execution_and_lifecycle.md) 참조).

### 노드 상태 (Node States)

- **UNRESOLVED**: 초기 상태
- **RESOLVING**: 현재 처리 중
- **RESOLVED**: 처리 완료

### 연결 (Connections)

유효성 검사 및 처리를 위한 수명 주기 콜백을 통해 관리됩니다. [실행 및 수명 주기](execution_and_lifecycle.md)를 참조하세요.

### 이벤트 (Events)

워크플로우 이벤트에 반응하려면 `on_griptape_event`를 사용합니다.

## 개발 환경 설정

1. griptape-nodes 설치
2. 격리를 위한 가상 환경 사용
3. 단순한 폴더 계층 구조로 프로젝트 구성
4. `griptape_nodes.exe_types.*` 및 `griptape_nodes_library.utils.*`에서 임포트

## 노드 생성 기본 구조

```python
from typing import Any
from griptape_nodes.exe_types.core_types import Parameter, ParameterMode
from griptape_nodes.exe_types.node_types import DataNode


class MyNode(DataNode):
    def __init__(self, **kwargs) -> None:
        super().__init__(**kwargs)
        self.category = "Category"
        self.description = "Description"

        self.add_parameter(Parameter(name="input", input_types=["str"], type="str", tooltip="입력 파라미터"))
        self.add_parameter(Parameter(name="output", output_type="str", tooltip="출력 파라미터"))

    def process(self) -> None:
        val = self.get_parameter_value("input").upper()
        self.parameter_output_values["output"] = val
```

## 문서 구성

- **[시작하기 (Getting Started)](getting_started.md)** — 첫 노드 구축을 위한 초보자 가이드
- **[파라미터 (Parameters)](parameters.md)** — 파라미터 속성, 트레이트, 헬퍼 클래스, 컨테이너 및 고급 파라미터 패턴
- **[파라미터 UI 레퍼런스 (Parameter UI Reference)](parameter_ui_reference.md)** — 파라미터 타입별 위젯 매핑, `ui_options` 키 및 트레이트
- **[실행 및 수명 주기 (Execution and Lifecycle)](execution_and_lifecycle.md)** — 수명 주기 콜백 및 비동기 API 연동 패턴
- **[프로젝트 시스템 활용 (Project System)](project_system.md)** — 상황(situations), 매크로, `ProjectFileParameter`를 통한 파일 저장
- **[모범 사례 및 오류 처리 (Error Handling)](error_handling.md)** — 비밀 정보, 임포트, 페이로드 크기, 유효성 검사, 오류 처리 및 로깅
- **[라이브러리 작성 (Authoring Libraries)](authoring_libraries.md)** — 라이브러리 매니페스트, 선언, 의존성 관리 및 표준 라이브러리 기여
- **[고급 라이브러리 (Advanced Libraries)](advanced_libraries.md)** — `AdvancedNodeLibrary` 수명 주기 훅, 라이브러리 소유 요청 핸들러
- **[커스텀 위젯 (Custom Widgets)](custom_widgets.md)** — 커스텀 자바스크립트 위젯 컴포넌트 및 테스트베드
- **[패턴 및 예제 (Patterns and Examples)](examples.md)** — 프로덕션 노드의 고급 패턴 및 빠른 참조 자료
- **[워커를 통한 노드 격리 (Node Isolation with Workers)](node_isolation_with_workers.md)** — 워커 서브프로세스에서 라이브러리를 격리 실행
- **[엄격 모드 레퍼런스 (Strict Mode Reference)](strict_mode.md)** — 격리 비호환성을 식별하는 엄격 모드 규칙
- **[ControlNode 예제 (Example Control Node)](example_control_node.py)** — 컨트롤 노드 구축의 모범 사례를 보여주는 완전한 예제

## 템플릿 리포지토리에서 시작하기

프로덕션 수준의 노드 라이브러리를 만드는 가장 빠른 방법은 공식 템플릿 리포지토리를 사용하는 것입니다:

[Griptape Nodes Library Template](https://github.com/griptape-ai/griptape-nodes-library-template/) ([README](https://github.com/griptape-ai/griptape-nodes-library-template/blob/main/README.md))
