# GriptapeCloudPrompt

## 어떤 노드인가요?

GriptapeCloudPrompt 노드는 Griptape Cloud의 AI 서비스에 대한 연결을 설정합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- Griptape Cloud 서비스를 통해 다양한 AI 모델을 사용하고자 할 때 (OpenAI에 별도로 가입하여 API 키를 발급받을 필요가 없습니다)
- 프롬프트 드라이버를 지원하는 모든 노드에 사용할 AI 모델을 맞춤 구성할 때

## 사용 방법

### 기본 설정

1. 워크플로에 GriptapeCloudPrompt 노드를 추가합니다.
1. driver 출력을 AI 모델을 사용해야 하는 노드(예: Agent)에 연결합니다.

### 파라미터 (Parameters)

- **model**: 사용할 AI 모델 (기본값: "gpt-4o")
- **stream**: 응답을 생성되는 즉시 실시간으로 수신할지(true), 한 번에 모두 수신할지(false) 여부
- **temperature**: 응답의 무작위성을 제어 (높을수록 창의적, 낮을수록 집중적)
- **max_attempts_on_fail**: 오류 발생 시 재시도 횟수
- **use_native_tools**: 모델의 내장 도구를 사용할지 여부
- **max_tokens**: 응답의 최대 토큰 길이
- **top_p**: 출력의 다양성을 제어 (내부적으로 top_p로 변환됨)

### 출력 (Outputs)

- **prompt_model_config**: 다른 노드에서 사용할 수 있도록 구성된 Griptape Cloud 드라이버

## 예시

Griptape Cloud를 통해 GPT-4o를 사용하는 에이전트를 생성하려는 경우:

1. 워크플로에 GriptapeCloudPrompt를 추가합니다.
1. "model"을 "gpt-4o"로 설정합니다.
1. "driver" 출력을 Agent의 "prompt_driver" 입력에 연결합니다.
1. 이제 해당 에이전트는 사용자의 맞춤 설정을 적용하여 Griptape Cloud를 사용합니다.

시도해 볼 만한 작업:

1. 더 창의적인 응답을 위해 "temperature"를 0.7로 설정해 보세요.
1. 응답을 실시간으로 보려면 "stream"을 true로, 완료 후 한 번에 보려면 false로 설정해 보세요.

## 중요 참고 사항

- 환경 변수에 `GT_CLOUD_API_KEY`로 유효한 Griptape API 키가 설정되어 있어야 합니다.
- 기본 모델은 "gpt-4o"입니다.
- min_p 파라미터는 내부적으로 top_p로 변환됩니다 (top_p = 1 - min_p).

## 자주 묻는 질문 및 문제 해결

- **API 키 누락 (Missing API Key)**: Griptape API 키가 올바르게 설정되어 있는지 확인하세요.
- **연결 오류 (Connection Errors)**: 인터넷 연결 상태와 API 키의 유효성을 확인하세요.
