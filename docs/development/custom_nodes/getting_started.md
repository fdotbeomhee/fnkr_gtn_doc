# 노드 개발 시작하기 (Getting Started)

Griptape Nodes 노드 개발을 시작하는 단계별 안내서입니다.

## 기본 개발 환경

커스텀 노드는 Python 패키지 또는 샌드박스 라이브러리 형태로 작성됩니다.

1. **샌드박스 라이브러리 위치**: 워크스페이스 내의 `sandbox_library/` 폴더에 `.py` 파일을 배치하면 엔진이 시작할 때 자동으로 노드를 로드합니다.
2. **패키지 형태**: 재사용 및 배포를 위해 `pyproject.toml`과 `griptape_nodes_library.json` 매니페스트를 갖춘 독립 라이브러리로 패키징합니다.

## 첫 번째 DataNode 작성

입력 텍스트를 대문자로 변환하는 간단한 텍스트 변환 노드를 만들어 보겠습니다.

```python
from griptape_nodes.exe_types.core_types import Parameter, ParameterMode
from griptape_nodes.exe_types.node_types import DataNode


class UpperCaseNode(DataNode):
    def __init__(self, **kwargs) -> None:
        super().__init__(**kwargs)
        self.category = "Text"
        self.description = "입력 텍스트를 대문자로 변환합니다."

        # 입력 파라미터 추가
        self.add_parameter(
            Parameter(
                name="text_in",
                type="str",
                input_types=["str"],
                mode=ParameterMode.INPUT,
                tooltip="변환할 텍스트",
            )
        )

        # 출력 파라미터 추가
        self.add_parameter(
            Parameter(
                name="text_out",
                type="str",
                output_type="str",
                mode=ParameterMode.OUTPUT,
                tooltip="대문자로 변환된 결과 텍스트",
            )
        )

    def process(self) -> None:
        input_text = self.get_parameter_value("text_in") or ""
        self.parameter_output_values["text_out"] = input_text.upper()
```

## 첫 번째 ControlNode 작성

외부 API를 호출하거나 실행 흐름을 제어해야 하는 경우 `ControlNode`를 상속합니다.

```python
import asyncio
from griptape_nodes.exe_types.core_types import Parameter, ParameterMode
from griptape_nodes.exe_types.node_types import ControlNode


class DelayedMessageNode(ControlNode):
    def __init__(self, **kwargs) -> None:
        super().__init__(**kwargs)
        self.category = "Utility"
        self.description = "지정된 시간(초) 동안 대기한 후 메시지를 출력합니다."

        self.add_parameter(
            Parameter(
                name="delay_seconds",
                type="float",
                default_value=2.0,
                tooltip="대기 시간(초)",
            )
        )
        self.add_parameter(
            Parameter(
                name="message",
                type="str",
                default_value="Hello, Griptape!",
                tooltip="출력할 메시지",
            )
        )

    async def aprocess(self) -> None:
        delay = self.get_parameter_value("delay_seconds")
        message = self.get_parameter_value("message")

        # 비동기 대기
        await asyncio.sleep(delay)
        self.parameter_output_values["message"] = message
```

## 노드 테스트 및 검증

1. 작성한 `.py` 파일을 `<workspace_directory>/sandbox_library/` 폴더에 저장합니다.
2. Griptape Nodes 에디터를 새로고침하거나 엔진을 재시작합니다.
3. 에디터 좌측 노드 라이브러리 패널에서 작성한 카테고리와 노드를 검색하여 캔버스에 추가합니다.
4. 노드를 연결하고 **Run Node**를 클릭하여 출력을 확인합니다.
