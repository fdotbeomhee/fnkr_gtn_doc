# CoherePrompt

## 어떤 노드인가요?

CoherePrompt 노드는 Cohere의 AI 모델에 대한 연결을 설정합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- 워크플로에서 Cohere의 AI 모델을 사용하고자 할 때
- Cohere 모델 고유의 특화된 기능을 활용하고자 할 때

## 사용 방법

### 기본 설정

1. 워크플로에 CoherePrompt 노드를 추가합니다.
1. 이 노드의 출력을 프롬프트 드라이버를 사용할 수 있는 노드(예: Agent)에 연결합니다.

### 파라미터 (Parameters)

- **model**: 사용할 Cohere 모델 (기본값: "command-r-plus")
- **max_attempts_on_fail**: 오류 발생 시 재시도 횟수
- **use_native_tools**: Cohere의 내장 도구를 사용할지 여부
- **max_tokens**: 응답의 최대 토큰 길이
- **p**: 출력의 다양성을 제어 (temperature와 유사)
- **k**: 가능성이 가장 높은 토큰에 대한 집중도를 제어
- **temperature**: 응답의 무작위성을 제어 (높을수록 창의적, 낮을수록 집중적)
- **stream**: 응답을 생성되는 즉시 실시간으로 수신할지(true), 한 번에 모두 수신할지(false) 여부

### 출력 (Outputs)

- **prompt_model_config**: 다른 노드에서 사용할 수 있도록 구성된 Cohere 드라이버

## 예시

Cohere를 사용하는 에이전트를 생성하려는 경우:

1. 워크플로에 CoherePrompt 노드를 추가합니다.
1. "driver" 출력을 Agent의 "prompt_driver" 입력에 연결합니다.

시도해 볼 만한 작업:

1. "model"을 기본값 외의 다른 모델로 설정해 보세요.
1. 중간 길이의 응답을 얻으려면 "max_tokens"를 1000으로 설정해 보세요.

## 중요 참고 사항

- 환경 변수에 `COHERE_API_KEY`로 유효한 Cohere API 키가 설정되어 있어야 합니다.
- 기본 모델은 "command-r-plus"입니다.
- 노드 설정 과정에서 API 키의 유효성을 검사합니다.

## 자주 묻는 질문 및 문제 해결

- **API 키 누락 (Missing API Key)**: Cohere API 키가 올바르게 설정되어 있는지 확인하세요.
- **연결 오류 (Connection Errors)**: 인터넷 연결 상태와 API 키의 유효성을 확인하세요.
