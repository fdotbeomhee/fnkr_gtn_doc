# 패턴 및 예제 (Patterns and Examples)

프로덕션 환경에서 검증된 커스텀 노드 개발 패턴과 실용적인 예제 모음입니다.

## 1. 이미지 처리 파이프라인 노드 패턴

```python
from griptape_nodes.exe_types.core_types import Parameter, ParameterMode
from griptape_nodes.exe_types.node_types import ControlNode
from griptape_nodes.exe_types.param_types.parameter_image import ParameterImage


class ImageInvertNode(ControlNode):
    def __init__(self, **kwargs) -> None:
        super().__init__(**kwargs)
        self.category = "Image/Filters"
        self.description = "이미지의 색상을 반전시킵니다."

        self.add_parameter(
            ParameterImage(
                name="image_in",
                mode=ParameterMode.INPUT,
                tooltip="입력 이미지",
            )
        )
        self.add_parameter(
            ParameterImage(
                name="image_out",
                mode=ParameterMode.OUTPUT,
                tooltip="반전된 출력 이미지",
            )
        )

    def process(self) -> None:
        img_artifact = self.get_parameter_value("image_in")
        if not img_artifact:
            return

        # 이미지 처리 및 결과 저장
        processed_artifact = self.invert_colors(img_artifact)
        self.parameter_output_values["image_out"] = processed_artifact
```

## 2. LLM 스트리밍 응답 처리 패턴

스트리밍 토큰을 실시간으로 UI에 전달하면서 최종 응답을 완성하는 비동기 에이전트 노드 패턴을 구현할 수 있습니다.
