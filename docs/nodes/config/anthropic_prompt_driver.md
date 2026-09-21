# AnthropicPrompt

## 어떤 노드인가요?

AnthropicPrompt 노드는 Anthropic의 AI 모델(예: Claude)에 대한 연결을 설정합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- 워크플로에서 Anthropic의 대화 모델을 사용하고자 할 때
- 에이전트가 Anthropic 모델과 상호작용하는 방식을 맞춤 설정할 때
- Anthropic 모델 응답에 대한 특정 설정을 제어할 때

## 사용 방법

### 기본 설정

1. 워크플로에 AnthropicPrompt 노드를 추가합니다.
1. 이 노드의 출력을 Claude와 같은 Anthropic 프롬프트 모델을 사용할 수 있는 노드(예: Agent)에 연결합니다!

### 파라미터 (Parameters)

- **model**: 사용할 모델 (기본값: "claude-3-7-sonnet-latest")
- **stream**: 응답을 생성되는 즉시 실시간으로 수신할지(true), 한 번에 모두 수신할지(false) 여부
- **temperature**: 응답의 무작위성(창의성)을 제어 (높을수록 창의적, 낮을수록 집중적/결정론적)
- **max_attempts_on_fail**: 오류 발생 시 재시도 횟수
- **use_native_tools**: Anthropic의 내장 도구를 사용할지 여부
- **max_tokens**: 응답의 최대 토큰 길이
- **top_p**: 출력의 다양성을 제어 (temperature와 유사)
- **top_k**: 가능성이 가장 높은 토큰에 대한 집중도를 제어

### 출력 (Outputs)

- **prompt_model_config**: 다른 노드에서 사용할 수 있도록 구성된 Anthropic 드라이버

## 예시

특정 설정을 갖춘 Anthropic 모델을 사용하는 에이전트를 생성하는 방법:

1. 워크플로에 AnthropicPrompt를 추가합니다.
1. "driver" 출력을 Agent의 "prompt_driver" 입력에 연결합니다.
1. 이제 해당 에이전트는 사용자가 설정한 맞춤 설정을 기반으로 Claude를 사용합니다.

시도해 볼 만한 작업:

1. "model"을 "claude-3-5-sonnet-latest" 등으로 변경해 보세요.
1. 더 창의적인 응답을 위해 "temperature"를 0.7로 설정해 보세요.
1. 더 긴 응답을 위해 "max_tokens"를 2000으로 설정해 보세요.

## 중요 참고 사항

- 환경 변수에 `ANTHROPIC_API_KEY`로 유효한 Anthropic API 키가 설정되어 있어야 합니다.
- 기본 모델은 "claude-3-5-sonnet-latest"입니다.
- 노드 설정 과정에서 API 키의 유효성을 검사합니다.

## 자주 묻는 질문 및 문제 해결

- **API 키 누락 (Missing API Key)**: 앱 설정에서 Anthropic API 키가 올바르게 구성되어 있는지 확인하세요.
- **연결 오류 (Connection Errors)**: 인터넷 연결 상태와 API 키의 유효성을 확인하세요.
