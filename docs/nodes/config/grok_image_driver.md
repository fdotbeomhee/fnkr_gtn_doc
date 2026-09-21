# GrokImage

## 어떤 노드인가요?

GrokImage 노드는 Grok의 이미지 생성 서비스(DALL-E)에 대한 연결을 설정합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- Grok의 DALL-E 모델을 사용하여 이미지를 생성하고자 할 때
- 텍스트 설명으로부터 시각적 콘텐츠를 생성할 때
- 이미지 생성 기능을 워크플로에 연결하고자 할 때

## 사용 방법

### 기본 설정

1. 워크플로에 GrokImage 노드를 추가합니다.
1. driver 출력을 이미지를 생성할 수 있는 노드(예: GenerateImage)에 연결합니다.

### 파라미터 (Parameters)

- **model**: 사용할 모델 (기본값: "grok-2-image-1212")

### 출력 (Outputs)

- **image_model_config**: 다른 노드에서 사용할 수 있도록 구성된 Grok 이미지 모델 설정

## 예시

Grok을 사용하여 이미지를 생성하려는 경우:

1. 워크플로에 GrokImage 노드를 추가합니다.
1. 사용 가능한 설정을 구성합니다.
1. "image_model_config" 출력을 GenerateImage의 "image_model_config" 입력에 연결합니다.

## 중요 참고 사항

- https://console.x.ai 에서 발급받은 유효한 Grok API key가 환경 변수 `GROK_API_KEY`에 설정되어 있어야 합니다.
- 이 노드는 Grok의 이미지 생성 기능을 간편하게 감싼 래퍼(Wrapper) 노드입니다.

## 자주 묻는 질문 및 문제 해결

- **API 키 누락 (Missing API Key)**: Grok API 키가 올바르게 설정되어 있는지 확인하세요.
- **연결 오류 (Connection Errors)**: 인터넷 연결 상태와 API 키의 유효성을 확인하세요.
- **생성 한도 초과 (Generation Limits)**: Grok의 요청 속도 제한(Rate limits) 및 사용량 쿼터에 유의하세요.
