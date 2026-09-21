# OpenAiImage

## 어떤 노드인가요?

OpenAiImage 노드는 OpenAI의 이미지 생성 서비스(DALL-E)에 대한 연결을 설정합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- OpenAI의 DALL-E 모델을 사용하여 이미지를 생성하고자 할 때
- 텍스트 설명으로부터 시각적 콘텐츠를 생성할 때
- 이미지 생성 기능을 워크플로에 연결하고자 할 때

## 사용 방법

### 기본 설정

1. 워크플로에 OpenAiImage 노드를 추가합니다.
1. driver 출력을 이미지를 생성할 수 있는 노드(예: GenerateImage)에 연결합니다.

### 파라미터 (Parameters)

- **model**: 사용할 모델 (기본값: "dall-e-3")
- **image_size**: 생성할 이미지 크기 (기본값: "1024x1024")
- **style**: natural 또는 vivid. Natural은 자연스러운 조명과 텍스처를 가진 실사 같은 이미지를 생성하며, vivid는 강화된 색상, 대비 및 더욱 극적인 구도의 이미지를 생성합니다.
- **quality**: 이미지 생성 품질을 선택합니다 (Standard 또는 HD).
- **background**: 이미지 생성을 위한 배경을 선택합니다.
- **moderation**: 유해하거나 불쾌감을 주는 콘텐츠에 대한 필터링 강도(Standard 또는 strict).
- **output_format**: png, jpeg

### 출력 (Outputs)

- **image_model_config**: 다른 노드에서 사용할 수 있도록 구성된 OpenAI 이미지 모델 설정

## 예시

OpenAI의 DALL-E를 사용하여 이미지를 생성하려는 경우:

1. 워크플로에 OpenAiImage 노드를 추가합니다.
1. 사용 가능한 설정을 구성합니다.
1. "image_model_config" 출력을 GenerateImage의 "image_model_config" 입력에 연결합니다.

## 중요 참고 사항

- 환경 변수에 `OPENAI_API_KEY`로 유효한 OpenAI API 키가 설정되어 있어야 합니다.
- 이 노드는 OpenAI의 이미지 생성 기능을 간편하게 감싼 래퍼(Wrapper) 노드입니다.
- 실제 사용되는 특정 DALL-E 모델은 기본 드라이버에 구성된 내용에 따라 달라집니다.

## 자주 묻는 질문 및 문제 해결

- **API 키 누락 (Missing API Key)**: OpenAI API 키가 올바르게 설정되어 있는지 확인하세요.
- **연결 오류 (Connection Errors)**: 인터넷 연결 상태와 API 키의 유효성을 확인하세요.
- **생성 한도 초과 (Generation Limits)**: OpenAI의 요청 속도 제한 및 사용량 쿼터에 유의하세요.
