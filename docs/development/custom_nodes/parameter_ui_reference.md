# 파라미터 UI 레퍼런스 (Parameter UI Reference)

파라미터의 타입과 `ui_options` 설정에 따라 에디터에서 렌더링되는 UI 위젯 매핑 레퍼런스입니다.

## 기본 위젯 매핑

| 파라미터 타입 | 렌더링되는 UI 위젯 | 설명 |
| --- | --- | --- |
| `str` | 단일 행 텍스트 입력창 | 일반 텍스트 입력 |
| `str` (`ui_options={"multiline": True}`) | 다중 행 텍스트 영역 (Textarea) | 긴 프롬프트나 코드 입력 |
| `int` | 정수 숫자 입력창 / 스핀박스 | 정수 값 입력 |
| `float` | 부동소수점 숫자 입력창 | 소수점 값 입력 |
| `bool` | 토글 스위치 / 체크박스 | True/False 선택 |
| `options` (trait) | 드롭다운 선택 상자 (Select) | 사전 정의된 목록 중 선택 |
| `slider` (trait) | 슬라이더 바 (Slider) | 범위 내 수치 조절 |
| `color` | 색상 선택기 (Color Picker) | RGB/HEX 색상 선택 |
| `file_path` | 파일 선택기 및 경로 입력창 | 파일 경로 지정 |

## UI Options 예시

```python
self.add_parameter(
    Parameter(
        name="prompt",
        type="str",
        ui_options={
            "multiline": True,
            "rows": 4,
            "placeholder": "프롬프트를 입력하세요...",
        },
    )
)
```
