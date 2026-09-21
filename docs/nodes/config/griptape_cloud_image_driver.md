# GriptapeCloudImage

## 어떤 노드인가요?

GriptapeCloudImage 노드는 Griptape Cloud의 이미지 생성 서비스에 대한 연결을 설정합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- Griptape Cloud 서비스를 통해 이미지를 생성하고자 할 때 (OpenAI에 별도로 가입하여 API 키를 발급받을 필요가 없습니다)
- Griptape 플랫폼을 통해 DALL-E 3로 이미지를 생성할 때

## 사용 방법

### 기본 설정

1. 워크플로에 GriptapeCloudImage 노드를 추가합니다.
1. driver 출력을 이미지를 생성해야 하는 노드(예: GenerateImage)에 연결합니다.

### 파라미터 (Parameters)

- **model**: 사용할 모델 (기본값: "gpt-image-1-mini")
- **image_size**: 생성할 이미지 크기 (기본값: "1024x1024")
- **style**: natural 또는 vivid. Natural은 자연스러운 조명과 텍스처를 가진 실사 같은 이미지를 생성하며, vivid는 강화된 색상, 대비 및 더욱 극적인 구도의 이미지를 생성합니다.
- **quality**: 이미지 생성 품질을 선택합니다 (Standard 또는 HD).

### 출력 (Outputs)

- **image_model_config**: 다른 노드에서 사용할 수 있도록 구성된 Griptape Cloud 이미지 모델 설정

## 예시

Griptape Cloud를 사용하여 이미지를 생성하려는 경우:

1. 워크플로에 GriptapeCloudImage 노드를 추가합니다.
1. 세로형 이미지를 위해 "size"를 "1024x1792"로 설정합니다.
1. "image_model_config" 출력을 GenerateImage의 "image_model_config" 입력에 연결합니다.
1. 이제 해당 노드는 사용자의 설정을 적용하여 Griptape Cloud로 이미지를 생성합니다.

## 중요 참고 사항

- 환경 변수에 `GT_CLOUD_API_KEY`로 유효한 Griptape API 키가 설정되어 있어야 합니다(Griptape Nodes를 사용하는 환경이라면 기본적으로 자동 설정됩니다).
- 노드는 선택한 모델에 따라 이미지 크기를 자동으로 조정합니다.
- 참고: 이 설정은 GenerateImage 노드의 기본 image_model_config이므로 해당 노드에 직접 연결하더라도 기본 동작과 동일할 수 있습니다.
