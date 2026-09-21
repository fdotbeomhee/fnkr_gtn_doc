# OpenAiPrompt

## 어떤 노드인가요?

OpenAiPrompt 노드는 GPT-4o와 같은 OpenAI의 대화 모델에 대한 직접 연결을 설정합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- 워크플로에서 OpenAI의 모델을 사용하고자 할 때
- 에이전트가 GPT-4o와 같은 모델과 상호작용하는 방식을 맞춤 설정할 때
- OpenAI 모델 응답에 대한 특정 설정을 제어할 때

## 사용 방법

### 기본 설정

1. 워크플로에 OpenAiPrompt를 추가합니다.
1. driver 출력을 OpenAI를 사용해야 하는 노드(예: Agent)에 연결합니다.

### 파라미터 (Parameters)

- **model**: 사용할 OpenAI 모델 (기본값: "gpt-4o")
- **stream**: 응답을 생성되는 즉시 실시간으로 수신할지(true), 한 번에 모두 수신할지(false) 여부
- **temperature**: 응답의 무작위성을 제어 (높을수록 창의적, 낮을수록 집중적/결정론적)
- **use_native_tools**: OpenAI의 내장 도구를 사용할지 여부
- **max_tokens**: 응답의 최대 토큰 길이
- **max_attempts_on_fail**: 오류 발생 시 재시도 횟수
- **top_p**: 출력의 다양성을 제어 (내부적으로 top_p로 변환됨)

### 출력 (Outputs)

- **prompt_model_config**: 다른 노드에서 사용할 수 있도록 구성된 OpenAI 드라이버

## 예시

특정 설정을 갖춘 GPT-4o를 사용하는 에이전트를 생성하려는 경우:

1. 워크플로에 OpenAiPrompt 노드를 추가합니다.
1. "driver" 출력을 Agent의 "prompt_driver" 입력에 연결합니다.

시도해 볼 만한 작업:

1. "model"을 "gpt-4o" 외의 다른 모델로 설정해 보세요.
1. 더욱 집중되고 결정론적인 응답을 위해 "temperature"를 0.2로 설정해 보세요.
1. 더 긴 응답을 위해 "max_tokens"를 2000으로 설정해 보세요.

## 중요 참고 사항

- 환경 변수에 `OPENAI_API_KEY`로 유효한 OpenAI API 키가 설정되어 있어야 합니다.
- 기본 모델은 "gpt-4o"입니다.
- min_p 파라미터는 내부적으로 top_p로 변환됩니다 (top_p = 1 - min_p).
- 다른 일부 드라이버와 달리 OpenAI는 top_k 파라미터를 지원하지 않습니다.
