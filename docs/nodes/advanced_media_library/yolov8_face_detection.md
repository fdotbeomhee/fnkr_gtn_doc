# YOLOv8 Face Detection

!!! warning "Hugging Face 모델을 사용하려면 사전 설정 단계가 필요합니다"

    Hugging Face 계정 설정, 액세스 토큰 생성, 그리고 이 노드가 완벽하게 동작하는 데 필요한 모델 설치 방법은 [이 가이드](../../guides/integrations/hugging_face.md)를 참조하세요.

## 어떤 노드인가요?

YOLOv8 Face Detection은 🤗 Hugging Face의 YOLOv8(You Only Look Once version 8) 객체 탐지 모델을 사용하여 이미지에서 사람의 얼굴을 탐지하는 컴퓨터 비전 노드입니다. 이 노드는 이미지를 처리하여 탐지된 각 얼굴의 신뢰도 점수(Confidence Score)와 함께 바운딩 박스(Bounding Box) 좌표를 반환합니다.

이 구현은 얼굴 탐지 작업에 특별히 학습되어 실시간 애플리케이션에 적합한 빠르고 정확한 결과를 제공하는 `arnabdhar/YOLOv8-Face-Detection` 모델을 사용합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- 얼굴 인식 파이프라인을 위해 이미지에서 얼굴을 탐지할 때
- 사진에서 얼굴 영역을 잘라내거나(Crop) 추출할 때
- 이미지 내 사람의 수를 셀 때
- 얼굴의 유무에 따라 이미지를 필터링할 때
- 얼굴 중심의 구도나 시각 효과를 생성할 때
- 프라이버시 보호(얼굴 블러/마스킹)를 위해 이미지를 처리할 때
- 자동화된 사진 정리 시스템을 구축할 때
- 얼굴 기반 접근 제어 시스템을 구현할 때

## 사용 방법

### 기본 설정

1. **노드 추가**:

    - 워크플로에 "YOLOv8 Face Detection" 노드를 추가합니다.
    - 노드는 처음 사용할 때 자동으로 모델을 다운로드합니다(Hugging Face 액세스 필요).

1. **입력 연결**:

    - `ImageArtifact` 또는 `ImageUrlArtifact`를 `input_image` 파라미터에 연결합니다.
    - 파일 로더, 이미지 생성 노드 또는 기타 이미지 처리 노드에서 입력을 가져올 수 있습니다.

1. **파라미터 구성**:

    - 탐지 결과를 필터링하기 위해 `confidence_threshold` (0.0-1.0)를 설정합니다.
    - 필요에 따라 탐지된 바운딩 박스를 확장하려면 `dilation` (0-100%)을 설정합니다.

1. **탐지 실행**:

    - 노드를 실행하여 입력 이미지에서 얼굴을 탐지합니다.
    - `detected_faces` 출력에 얼굴 탐지 결과 목록이 포함됩니다.

### 파라미터 (Parameters)

#### 입력 파라미터 (Input Parameters)

- **input_image** (필수)

    - 타입: `ImageArtifact` 또는 `ImageUrlArtifact`
    - 얼굴 탐지를 수행할 분석 대상 이미지

- **confidence_threshold**

    - 타입: `float` (0.0-1.0)
    - 기본값: `0.5`
    - 탐지 결과에 포함되기 위한 최소 신뢰도 점수
    - 값이 높을수록 = 탐지 수는 적어지지만 신뢰도가 높아짐
    - 값이 낮을수록 = 탐지 수는 많아지지만 오탐(False Positive)이 포함될 수 있음

- **dilation**

    - 타입: `float` (0.0-100.0)
    - 기본값: `0.0`
    - 바운딩 박스를 중심을 유지하면서 확장할 백분율
    - 탐지된 얼굴 주변의 배경 컨텍스트를 더 많이 포함할 때 유용함
    - 예시: `10.0`은 모든 방향으로 박스를 10% 확장함

#### 출력 파라미터 (Output Parameters)

- **detected_faces**

    - 타입: `list`
    - 탐지된 얼굴 목록으로, 각 항목은 다음을 포함합니다:
        - `x`: 바운딩 박스의 좌상단 X 좌표
        - `y`: 바운딩 박스의 좌상단 Y 좌표
        - `width`: 바운딩 박스의 너비
        - `height`: 바운딩 박스의 높이
        - `confidence`: 탐지 신뢰도 점수 (0.0-1.0)

- **logs**

    - 타입: `string`
    - 다음 항목을 포함하는 탐지 프로세스의 상세 로그:
        - 모델 로딩 상태
        - 탐지 파라미터
        - 탐지된 얼굴 수

### 출력 형식 (Output Format)

탐지된 각 얼굴은 딕셔너리 형태로 표현됩니다:

```json
{
  "x": 150,
  "y": 200,
  "width": 300,
  "height": 350,
  "confidence": 0.95
}
```

바운딩 박스 좌표는 입력 이미지 크기에 상대적인 픽셀 단위이며, 원점 (0,0)은 좌상단 모서리입니다.

### 예시 워크플로 (Example Workflows)

#### 기본 얼굴 탐지

1. "File to Bytes" 또는 유사한 노드를 사용하여 이미지를 로드합니다.
1. YOLOv8 Face Detection의 `input_image`에 연결합니다.
1. 균형 잡힌 탐지를 위해 `confidence_threshold`를 `0.5`로 설정합니다.
1. `detected_faces` 출력에 모든 얼굴 위치와 신뢰도 점수가 포함됩니다.

#### 얼굴 자르기(Crop) 파이프라인

1. YOLOv8 Face Detection을 사용하여 얼굴을 탐지합니다.
1. 얼굴 주변 배경을 일부 포함하도록 `dilation`을 `10.0`으로 설정합니다.
1. `detected_faces`를 "Crop Image" 노드에 연결합니다.
1. 추가 처리를 위해 개별 얼굴 이미지를 추출합니다.

#### 고신뢰도 탐지만 수행

1. `confidence_threshold`를 `0.8` 이상으로 설정합니다.
1. 불확실한 탐지 결과를 걸러냅니다.
1. 높은 정밀도가 요구되는 애플리케이션에 적합합니다.

### 고급 기능

- **자동 모델 캐싱 (Automatic Model Caching)**: 다운로드한 모델은 이후 빠른 실행을 위해 로컬에 캐시됩니다.
- **경계선 제한 (Boundary Clamping)**: 확장된(Dilated) 바운딩 박스는 이미지 경계를 벗어나지 않도록 자동으로 제한됩니다.
- **중심 기준 확장 (Centered Dilation)**: 박스 확장 시 원래 탐지 결과의 중심점이 유지됩니다.
- **배치 호환 (Batch-Compatible)**: 루프 구조에 연결하여 여러 이미지를 일괄 처리할 수 있습니다.

## 성능 고려 사항 (Performance Considerations)

- **첫 실행**: 초기 실행 시 모델(~6MB)을 다운로드하므로 약간의 시간이 걸릴 수 있습니다.
- **이후 실행**: 캐시된 모델은 거의 즉시 로드됩니다.
- **이미지 크기**: 이미지가 클수록 처리 시간이 길어지지만 더 멀리 있는 얼굴도 탐지할 수 있습니다.
- **탐지 속도**: YOLOv8은 실시간 성능에 최적화되어 있어 일반적으로 밀리초 단위로 이미지를 처리합니다.
- **메모리 사용량**: 모델 로드 시 약 50MB의 RAM이 필요합니다.

## 자주 묻는 질문 및 문제 해결

- **API 키 누락**: Hugging Face API 토큰이 `HF_TOKEN`으로 설정되어 있는지 확인하세요. 설정 방법은 [이 가이드](../../guides/integrations/hugging_face.md)에 나와 있습니다.
- **모델을 찾을 수 없음**: "model not available" 경고가 표시되면 제공된 링크를 클릭하여 Model Manager를 열고 모델을 다운로드하세요.
- **얼굴이 탐지되지 않음**: 얼굴이 있을 것으로 예상되는데 탐지되지 않는 경우 `confidence_threshold`를 낮춰보세요.
- **오탐(False Positive)이 너무 많음**: 신뢰도가 낮은 탐지 결과를 걸러내기 위해 `confidence_threshold`를 높이세요.
- **바운딩 박스 크기 문제**: 박스가 너무 꽉 차 보이면 `dilation` 파라미터를 늘려 여백(패딩)을 추가하세요.

## 기술 세부 정보 (Technical Details)

- **모델**: Hugging Face의 YOLOv8 Face Detection (`arnabdhar/YOLOv8-Face-Detection`)
- **아키텍처**: 얼굴 탐지에 특화된 YOLOv8 객체 탐지 프레임워크
- **의존성**: `ultralytics>=8.0.0`, `supervision>=0.20.0`
- **출력 형식**: 신뢰도 점수를 포함한 표준 바운딩 박스 형식 (x, y, width, height)
- **처리 방식**: 속도와 정확도에 최적화된 단일 패스(Single-pass) 탐지
