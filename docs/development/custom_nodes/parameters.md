# 파라미터 상세 가이드 (Parameters) {: #parameters }

파라미터(Parameters)는 노드의 입력, 출력, 속성을 정의합니다. 이 페이지에서는 모든 `Parameter` 속성, 트레이트(Traits) 시스템, 파라미터 헬퍼 클래스, 컨테이너, 동적 파라미터 패턴을 다룹니다.

파라미터 타입별 에디터 위젯 매핑, 지원되는 `ui_options` 키 및 트레이트에 대한 내용은 [파라미터 UI 레퍼런스](parameter_ui_reference.md)를 참조하세요.

## Parameter 기본 속성

- **name**: 고유 식별자 문자열 (공백 불가)
- **tooltip**: UI 도움말 텍스트
- **default_value**: 파라미터의 기본값
- **type**: 데이터 타입 문자열 (예: `"str"`, `"int"`, `"list[str]"`, `"ImageUrlArtifact"`)
- **input_types**: 수락 가능한 입력 연결 타입 목록 (`list[str]`)
- **output_type**: 나가는 연결의 출력 타입 문자열
- **allowed_modes**: 허용 모드 집합 (`set[ParameterMode]`: INPUT, OUTPUT, PROPERTY)
- **ui_options**: UI 커스터마이징을 위한 딕셔너리
- **converters**: 값 변환을 위한 콜백 함수 목록
- **validators**: 유효성 검사를 위한 콜백 함수 목록
- **settable**: 수정 가능 여부 (계산/출력 전용 파라미터는 False)
- **serializable**: 워크플로우 저장 시 직렬화 여부 (드라이버, 파일 핸들 등은 False). 출력에 지정하면 값은 생성한 프로세스에 보관되고, 워커 프로세스 경계를 넘을 때는 키가 전송됩니다. 자세한 내용은 [직렬화할 수 없는 값 전달 (Passing Values That Cannot Be Serialized)](passing_unserializable_values.md)을 참조하세요.
- **exclude_from_metadata**: 비밀번호, API 키 등 민감 정보를 메타데이터 출력에서 제외할지 여부

## 트레이트 (Traits) {: #traits }

`add_trait()` 메서드를 사용하여 파라미터에 특화된 동작과 UI 위젯을 부여합니다:

- **Options**: 드롭다운 선택 UI (`Options(choices=["옵션1", "옵션2"])`)
- **Slider**: 수치 슬라이더 UI (`Slider(min_val=0.0, max_val=1.0)`). `soft_limits=True`를 지정하면 범위는 슬라이더 트랙 크기만 결정하며, 범위를 벗어난 값도 직접 입력할 수 있습니다.
- **Button**: 버튼 UI (`Button(label="클릭", on_click=callback)`)
- **ColorPicker**: 색상 선택기 (`ColorPicker(format="hex")`)
- **FileSystemPicker**: 파일 및 폴더 탐색기

**트레이트 상태 저장 (Saving trait state)**: 트레이트는 `to_state()`와 `apply_state()`를 구현하여 상태 저장에 참여합니다. 생성자 형태의 상태(텍스트, 숫자, 불리언, 또는 이들로 구성된 리스트나 딕셔너리)를 반환하십시오. 지원되지 않는 값은 경고와 함께 제외됩니다. 기본 메서드를 사용하는 트레이트에는 저장되는 상태가 없습니다.

**UI 옵션 쓰기 수락 (Accepting UI option writes)**: `state_from_ui_options()`를 구현하면, 노드 코드, 에디터 또는 저장된 파일에서 발생한 `ui_options` 쓰기를 `apply_state()`가 받는 상태로 변환할 수 있습니다. 기본 구현은 쓰기를 무시하며, 상태가 없는 렌더링 키에 적합합니다. 트레이트가 수락하지 않았지만 렌더링 결과를 바꿀 수 있었던 쓰기는 로그에 기록됩니다.

## 파라미터 값 (Parameter Values) {: #parameter-values }

다음 경우에 파라미터 값이 직렬화됩니다.

- 워크플로우를 저장할 때
- 노드를 복사할 때
- 워크플로우를 내보낸 이미지에 포함할 때
- 노드가 자체 라이브러리의 프로세스에서 실행될 때 ([워커를 통한 노드 격리 (Node Isolation with Workers)](node_isolation_with_workers.md) 참조)
- 루프 또는 서브플로 그룹이 **로컬 실행(Local Execution)**이 아닌 **실행 환경(Execution Environment)**에서 실행될 때

다음 타입은 그대로 직렬화됩니다.

- `None`, `bool`, `int`, `float`, `str`, 그리고 이들로 구성된 리스트와 딕셔너리
- 튜플, 명명된 튜플, 집합, `bytes`, 문자열이 아닌 키를 가진 딕셔너리
- 열거형(enum), `pathlib` 경로, 날짜와 시간, `timedelta`, `UUID`, `Decimal`
- pydantic 모델, 데이터클래스, attrs 클래스
- 아티팩트, 룰셋 같은 Griptape 객체

그 밖의 클래스를 직렬화하려면 `register_value_codec`으로 데코레이트하고, 위에 나열된 타입을 반환하는 `to_state()` 메서드와 이 타입들로부터 객체를 다시 만드는 `from_state()` 클래스 메서드를 구현합니다. 하위 클래스도 적용됩니다.

```python
from griptape_nodes.exe_types.core_types import register_value_codec


@register_value_codec
class Palette:
    def __init__(self, colors: list[str]) -> None:
        self.colors = colors

    def to_state(self) -> dict:
        return {"colors": self.colors}

    @classmethod
    def from_state(cls, state: dict) -> "Palette":
        return cls(state["colors"])
```

다른 패키지의 클래스처럼 직접 수정할 수 없는 클래스의 경우, 변환 함수를 대신 전달합니다. 이 방식은 해당 클래스 자체에만 적용되며 하위 클래스에는 적용되지 않습니다. 또한 다른 저장 방법이 없는 클래스에만 사용하십시오. 라이브러리의 `before_library_nodes_loaded`에서 등록하면, 라이브러리를 로드하는 모든 프로세스에 변환 함수가 적용됩니다.

```python
import numpy as np

from griptape_nodes.exe_types.core_types import register_value_codec
from griptape_nodes.node_library.advanced_node_library import AdvancedNodeLibrary


class MyLibrary(AdvancedNodeLibrary):
    def before_library_nodes_loaded(self, library_data, library) -> None:
        register_value_codec(
            np.ndarray,
            to_state=lambda array: {"dtype": str(array.dtype), "shape": list(array.shape), "data": array.tobytes()},
            from_state=lambda state: np.frombuffer(state["data"], state["dtype"]).reshape(state["shape"]),
        )
```

위 목록에 없는 타입의 값은 워크플로우를 저장하거나 노드를 복사하거나 붙여넣을 때 제외되며, 로그에 노드와 파라미터 이름이 포함된 경고가 기록됩니다. 저장할 의도가 없는 값을 가진 파라미터에 `serializable=False`를 지정하면, 경고 없이 제외됩니다.

## 파라미터 헬퍼 클래스 (`ParameterString`, `ParameterInt`, ...)

`griptape_nodes.exe_types.param_types.*` 아래의 편리한 서브클래스들을 사용할 수 있습니다:

| 헬퍼 클래스 | 타입 (`type` / `output_type`) | 주요 편의 인자 | 설명 |
| --- | --- | --- | --- |
| `ParameterString` | `"str"` / `"str"` | `markdown`, `multiline`, `placeholder_text` | 문자열 입력 및 텍스트 영역 |
| `ParameterBool` | `"bool"` / `"bool"` | `on_label`, `off_label` | 불리언 토글 스위치 |
| `ParameterInt` | `"int"` / `"int"` | `step`, `slider`, `min_val`, `max_val` | 정수 입력 및 슬라이더 |
| `ParameterFloat` | `"float"` / `"float"` | `step`, `slider`, `min_val`, `max_val` | 부동소수점수 입력 및 슬라이더 |
| `ParameterDict` | `"dict"` / `"dict"` | — | 딕셔너리 데이터 처리 |
| `ParameterJson` | `"json"` / `"json"` | `button`, `button_label` | JSON 파싱 및 검증 |
| `ParameterImage` | `"ImageUrlArtifact"` | `clickable_file_browser`, `webcam_capture_image` | 이미지 에셋 처리 |
| `ParameterAudio` | `"AudioUrlArtifact"` | `clickable_file_browser`, `microphone_capture_audio` | 오디오 에셋 처리 |

## 컨테이너: ParameterList 및 ParameterGroup

- **ParameterList**: 사용자가 런타임에 항목을 동적으로 추가/삭제할 수 있는 가변 리스트 파라미터입니다.
- **ParameterGroup**: 노드 UI에서 관련된 파라미터들을 접고 펼칠 수 있는 그룹으로 시각적으로 묶어줍니다.

**항목별 변환 (Converting each item)**: `item_converters`를 전달하면 항목을 하나씩 변환할 수 있습니다(예: 파일 경로를 아티팩트로 변환). 항목이 자신의 행에 직접 설정되었거나 연결을 통해 리스트 전체로 전달되었거나 상관없이, 각 항목에 대해 실행됩니다. `converters`는 각 행에 대해 실행되며, 연결이 전달한 리스트 전체에 대해서도 실행됩니다. 단, 행으로부터 구성된 값에는 실행되지 않습니다.

**리스트 전체 설정 (Setting a whole list)**: 아무것도 연결되지 않은 리스트는 항목을 행 단위로 보관하며, 자체 값은 가지지 않습니다. 리스트를 읽으면 행으로부터 값이 구성됩니다. `SetParameterValueRequest` 또는 `self.set_parameter_value("images", [...])`로 리스트 전체를 설정하면, 기존 행을 항목 수만큼의 행으로 교체합니다. 노드 실행 중에 수행한 쓰기(노드의 결과로 기록됨), 워크플로우 로드 중의 쓰기, 그리고 리스트 또는 그 행의 `before_value_set` 또는 `after_value_set`에서 수행한 쓰기는 행을 변경하지 않습니다. 마지막 경우에는 경고가 기록됩니다. 항목을 변환하려고 이 훅에서 리스트를 다시 쓰지 말고, `item_converters`를 사용하십시오.

