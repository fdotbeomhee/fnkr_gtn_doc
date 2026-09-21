# AddTextToExistingImage

## 어떤 노드인가요?

AddTextToExistingImage 노드는 기존 이미지 위에 텍스트를 오버레이하는 노드입니다. 알파(투명도)를 포함한 텍스트/배경 색상, 정렬 제어, 별도의 딕셔너리 입력으로부터 확장되는 `{key}` 형태의 간단한 템플릿 플레이스홀더를 지원합니다.

## 언제 사용하나요?

다음과 같은 상황에서 이 노드를 사용합니다:

- 이미지에 제목, 캡션 또는 레이블 추가
- 이미지 위에 메타데이터(파일명, 타임스탬프, ID 등) 스탬프 표시
- 후속 단계를 위한 주석이 추가된 이미지 출력 생성
- 딕셔너리에서 가져온 `{key}` 플레이스홀더를 사용하여 동적 텍스트 렌더링

## 사용 방법

### 기본 설정

1. 워크플로우에 AddTextToExistingImage 노드를 추가합니다.
1. 이미지 소스를 **input_image**에 연결합니다.
1. **text**에 텍스트 템플릿을 입력합니다.
1. (선택 사항) `{key}` 플레이스홀더를 확장하려면 딕셔너리를 **template_values**에 연결합니다.
1. 정렬, 색상, 여백, 글꼴 크기를 조정합니다.
1. 노드를 실행하여 **output**을 생성합니다.

### 파라미터 (Parameters)

#### 입력 (Inputs)

- **input_image**: 텍스트를 렌더링할 대상 이미지 (ImageUrlArtifact / ImageArtifact / dict)

#### 텍스트 입력

- **text** (문자열): 렌더링할 텍스트 템플릿

    - `{key_name}` 형태의 플레이스홀더를 지원합니다.
    - 플레이스홀더 확장은 이미지에 렌더링되는 내용에만 적용되며, `text` 출력 파라미터는 원래의 템플릿으로 유지됩니다.

- **template_values** (딕셔너리, 선택 사항): `text` 내의 플레이스홀더를 치환하는 데 사용할 값

    - 플레이스홀더 키가 존재하지 않는 경우 렌더링된 텍스트에 그대로 유지됩니다.
    - 누락된 키는 노드 실행 시 `result_details`에도 보고됩니다.

#### 스타일링 파라미터

- **text_color** (16진수 알파 포함, 기본값: `#ffffffff`): 알파(투명도)를 포함한 텍스트 색상
- **text_background** (16진수 알파 포함, 기본값: `#000000ff`): 알파를 포함한 텍스트 배경 사각형 색상
- **text_vertical_alignment** (top | center | bottom, 기본값: top): 텍스트 블록의 세로 정렬
- **text_horizontal_alignment** (left | center | right, 기본값: left): 텍스트 블록의 가로 정렬
- **margin** (정수, 기본값: 10): 텍스트 배치를 위한 이미지 가장자리로부터의 여백(inset)
- **font_size** (정수, 기본값: 36): 렌더링에 사용할 글꼴 크기

### 출력 (Outputs)

- **output**: 업데이트된 이미지 (ImageUrlArtifact)
- **text**: 원본 텍스트 템플릿 문자열 (확장된 렌더링 문자열이 아님)
- **was_successful**: 노드 실행 성공 여부를 나타냅니다.
- **result_details**: 누락된 플레이스홀더 키 메시지를 포함한 성공/실패 세부 정보

## 예시

일반적인 "메타데이터 스탬프" 워크플로우:

1. LoadImage를 사용하여 이미지를 불러옵니다.

1. AddTextToExistingImage를 추가합니다.

1. **text**를 다음과 같이 설정합니다:

    `"Photo: {name}  |  #{index}"`

1. **template_values**를 제공합니다:

    ```json
    {"name": "Portrait", "index": 7}
    ```

1. `text_background`를 `#00000080`과 같이 반투명한 검은색으로 설정합니다.

1. `text_color`를 흰색 `#ffffffff`로 설정합니다.

1. 노드를 실행하고 **output**을 DisplayImage에 연결합니다.

## 중요 참고 사항

- **누락된 키**: `template_values`에 `{key}`가 없으면 플레이스홀더가 렌더링된 텍스트에 그대로 남으며, `key: key not found in dictionary input`과 같은 메시지가 `result_details`에 추가됩니다.
- **알파 지원**: 텍스트와 배경 색상 모두 16진수 8자리(`#RRGGBBAA`) 형식을 통해 투명도를 지원합니다.
- **실행**: 노드는 `process()` 시 최종 이미지를 업로드하고 출력합니다.
