# 프로젝트 시스템 활용 (Working with the Project System)

Griptape Nodes의 프로젝트 시스템은 워크플로우 실행 중에 생성되는 파일과 에셋의 저장 위치, 경로 템플릿, 매크로 및 상황(Situations)을 체계적으로 관리합니다.

## 핵심 개념

- **상황 (Situations)**: 파일이 저장되는 목적별 디렉토리 범주입니다(예: `outputs`, `inputs`, `temp`, `cache`).
- **매크로 (Macros)**: 파일 이름이나 경로에 동적 정보를 주입하는 템플릿 변수입니다(예: `{date}`, `{time}`, `{workflow_name}`, `{node_name}`, `{count}`).
- **ProjectFileParameter**: 프로젝트 시스템과 연동되어 파일 경로를 선택하고 생성하는 특수 파라미터입니다.

## 파일 저장 시 ProjectFileParameter 사용하기

노드에서 파일을 생성하거나 저장할 때 하드코딩된 경로 대신 `ProjectFileParameter`를 사용하면 프로젝트 설정에 정의된 디렉토리 구조와 이름 지정 규칙을 자동으로 따르게 됩니다.

```python
from griptape_nodes.exe_types.core_types import Parameter, ParameterMode
from griptape_nodes.exe_types.node_types import ControlNode


class SaveReportNode(ControlNode):
    def __init__(self, **kwargs) -> None:
        super().__init__(**kwargs)
        self.category = "Reporting"
        self.description = "보고서 파일을 프로젝트 출력 디렉토리에 저장합니다."

        self.add_parameter(
            Parameter(
                name="content",
                type="str",
                input_types=["str"],
                tooltip="저장할 텍스트 내용",
            )
        )

        self.add_parameter(
            Parameter(
                name="output_file",
                type="file_path",
                default_value="{outputs}/reports/{date}_{workflow_name}_report.txt",
                tooltip="저장 대상 파일 경로 템플릿",
            )
        )

    def process(self) -> None:
        content = self.get_parameter_value("content")
        file_path = self.get_parameter_value("output_file")

        # 실제 경로 해결 및 디렉토리 생성
        resolved_path = self.resolve_project_path(file_path)
        resolved_path.parent.mkdir(parents=True, exist_ok=True)

        with open(resolved_path, "w", encoding="utf-8") as f:
            f.write(content)

        self.parameter_output_values["output_file"] = str(resolved_path)
```

## 주요 매크로 목록

| 매크로 | 설명 | 예시 |
| --- | --- | --- |
| `{outputs}` | 기본 출력 디렉토리 경로 | `/workspace/outputs` |
| `{inputs}` | 기본 입력 디렉토리 경로 | `/workspace/inputs` |
| `{date}` | 현재 날짜 (YYYY-MM-DD) | `2026-09-17` |
| `{time}` | 현재 시간 (HH-MM-SS) | `14-30-00` |
| `{workflow_name}` | 현재 워크플로우 이름 | `my_workflow` |
| `{node_name}` | 현재 노드 이름 | `SaveReport` |
| `{count}` | 순차 증가 번호 카운터 | `0001` |
