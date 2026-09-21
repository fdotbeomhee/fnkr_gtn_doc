# 커스텀 위젯 (Custom Widgets)

Griptape Nodes는 Vue/JavaScript 기반의 커스텀 프론트엔드 위젯을 지원하여 기본 제공 위젯 외에 독창적인 UI 컴포넌트를 노드에 통합할 수 있습니다.

## 커스텀 위젯 구성 요소

1. **프론트엔드 컴포넌트**: 에디터에서 렌더링될 JavaScript / Vue 3 컴포넌트.
2. **백엔드 파라미터 선언**: Python 노드에서 `ui_options={"widget": "custom_widget_name"}`으로 지정.
3. **위젯 매니페스트**: 라이브러리의 `custom_widgets/` 디렉토리에 위젯 등록.

## 위젯 통신

커스텀 위젯은 `props`를 통해 노드 파라미터 값을 전달받고, `emit("update:modelValue", newValue)` 이벤트를 통해 사용자 입력을 Python 백엔드로 전달합니다.
