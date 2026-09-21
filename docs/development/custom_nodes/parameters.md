# 파라미터 상세 가이드 (Parameters)

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
- **serializable**: 워크플로우 저장 시 직렬화 여부 (드라이버, 파일 핸들 등은 False)
- **exclude_from_metadata**: 비밀번호, API 키 등 민감 정보를 메타데이터 출력에서 제외할지 여부

## 트레이트 (Traits)

`add_trait()` 메서드를 사용하여 파라미터에 특화된 동작과 UI 위젯을 부여합니다:

- **Options**: 드롭다운 선택 UI (`Options(choices=["옵션1", "옵션2"])`)
- **Slider**: 수치 슬라이더 UI (`Slider(min_val=0.0, max_val=1.0)`)
- **Button**: 버튼 UI (`Button(label="클릭", on_click=callback)`)
- **ColorPicker**: 색상 선택기 (`ColorPicker(format="hex")`)
- **FileSystemPicker**: 파일 및 폴더 탐색기

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
