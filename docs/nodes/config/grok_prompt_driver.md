# GrokPrompt

!!! warning "xAI API 사용 시 결제 정보 등록 필요"

    GrokPrompt 노드를 사용하려면 xAI API 키가 작동하기 전에 xAI 계정 및 결제 정보(Billing)가 등록되어 있어야 합니다. 결제 설정을 완료하지 않으면 유효한 API 키가 있더라도 xAI를 사용하는 노드가 실패합니다. 결제가 포함된 xAI 계정 설정 방법은 [이 가이드](../../guides/integrations/grok.md)를 참조하세요.

## 어떤 노드인가요?

GrokPrompt 노드는 xAI의 Grok 모델에 대한 연결을 설정합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- 워크플로에서 Grok의 대화 모델을 사용하고자 할 때
- 에이전트가 Grok 모델과 상호작용하는 방식을 맞춤 설정할 때
- Grok 모델 응답에 대한 특정 설정을 제어할 때

## 사용 방법

### 기본 설정

1. 워크플로에 GrokPrompt 노드를 추가합니다.
1. 출력을 Grok 프롬프트 모델을 사용할 수 있는 노드(예: Agent)에 연결합니다!

### 파라미터 (Parameters)

- **model**: 사용할 모델. 기본값은 "grok-3-beta"이며, 선택 옵션으로 "grok-3-beta", "grok-3-fast-beta", "grok-3-mini-beta", "grok-3-mini-fast-beta", "grok-2-vision-1212"를 지원합니다.
- **top_p**: 출력의 다양성을 제어 (기본값: 0.9)
- **stream**: 응답을 생성되는 즉시 실시간으로 수신할지(true), 한 번에 모두 수신할지(false) 여부
- **temperature**: 응답의 무작위성을 제어 (높을수록 창의적, 낮을수록 집중적)
- **max_attempts_on_fail**: 오류 발생 시 재시도 횟수
- **use_native_tools**: 모델의 내장 도구를 사용할지 여부
- **max_tokens**: 응답의 최대 토큰 길이

### 출력 (Outputs)

- **prompt_model_config**: 다른 노드에서 사용할 수 있도록 구성된 Grok 드라이버

## 예시

특정 설정을 갖춘 Grok 모델을 사용하는 에이전트를 생성하는 방법:

1. 워크플로에 GrokPrompt를 추가합니다.
1. **prompt_model_config** 출력을 Agent의 **prompt_model_config** 입력에 연결합니다.
1. 이제 해당 에이전트는 사용자의 맞춤 설정을 적용하여 Grok을 사용합니다.

시도해 볼 만한 작업:

1. "model"을 "grok-3-beta"에서 "grok-3-mini-beta"로 변경해 보세요.
1. 더 집중된 응답을 얻으려면 "top_p"를 0.7로 설정해 보세요.

## 중요 참고 사항

- 환경 변수에 `GROK_API_KEY`로 유효한 Grok API 키가 설정되어 있어야 합니다.
- 다른 일부 드라이버와 달리 Grok은 'seed' 및 'top_k' 파라미터를 지원하지 않습니다.
- Grok은 유료 서비스이며, API 키가 정상 작동하려면 Grok.ai 웹사이트에서 결제 설정이 완료되어 있어야 합니다.
