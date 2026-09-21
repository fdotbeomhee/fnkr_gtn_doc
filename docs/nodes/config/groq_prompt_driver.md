# Groq Prompt Driver

Groq Prompt Driver 노드를 사용하면 Griptape Nodes 프레임워크 내에서 Groq의 언어 모델을 구성하고 활용할 수 있습니다. 이 노드는 Groq의 고성능 추론 엔진과 다양한 언어 모델에 대한 접근을 제공합니다.

## 구성

### API 키

Groq Prompt Driver를 사용하려면 Groq API 키가 필요합니다. API 키는 [Groq Console](https://console.groq.com/keys)에서 발급받을 수 있습니다.

### 지원 모델

Groq Prompt Driver는 다음 모델들을 지원합니다:

#### 프로덕션 모델 (Production Models)

- [gemma2-9b-it](https://huggingface.co/google/gemma-2-9b-it) - Google의 Gemma 2 9B 모델
- [meta-llama/llama-guard-4-12b](https://console.groq.com/docs/model/llama-guard-4-12b) - 콘텐츠 검열(Moderation)을 위한 Meta의 Llama Guard 모델
- [llama-3.3-70b-versatile](https://console.groq.com/docs/model/llama-3.3-70b-versatile) - Meta의 다목적 70B 파라미터 모델
- [llama-3.1-8b-instant](https://console.groq.com/docs/model/llama-3.1-8b-instant) - Meta의 빠른 8B 파라미터 모델
- [llama3-70b-8192](https://console.groq.com/docs/model/llama3-70b-8192) - 8K 컨텍스트를 지원하는 Meta의 70B 파라미터 모델
- [llama3-8b-8192](https://console.groq.com/docs/model/llama3-8b-8192) - 8K 컨텍스트를 지원하는 Meta의 8B 파라미터 모델
- [meta-llama/llama-4-scout-17b-16e-instruct](https://console.groq.com/docs/model/llama-4-scout-17b-16e-instruct) - 16K 컨텍스트를 지원하는 Meta의 비전 모델(Image Description 노드와 호환)
- [meta-llama/llama-4-maverick-17b-128e-instruct](https://console.groq.com/docs/model/llama-4-maverick-17b-128e-instruct) - 128K 컨텍스트를 지원하는 Meta의 비전 모델(Image Description 노드와 호환)

#### 프리뷰 모델 (Preview Models)

- [allam-2-7b](https://ai.azure.com/explore/models/ALLaM-2-7b-instruct/version/2/registry/azureml) - 사우디 데이터 및 인공지능청(SDAIA)의 7B 파라미터 모델
- [deepseek-r1-distill-llama-70b](https://console.groq.com/docs/model/deepseek-r1-distill-llama-70b) - DeepSeek의 증류(Distilled) 70B 파라미터 모델

### 파라미터 (Parameters)

Groq Prompt Driver는 다음과 같은 구성 파라미터를 지원합니다:

| 파라미터 (Parameter) | 타입 (Type) | 기본값 (Default) | 설명 (Description) |
| -------------------- | ----------- | ---------------- | ---------------------------------------------------- |
| model                | string      | gemma2-9b-it     | 텍스트 생성에 사용할 Groq 모델 |
| temperature          | float       | 0.7              | 출력의 무작위성을 제어 (0.0 ~ 1.0) |
| top_p                | float       | 0.9              | Nucleus 샘플링을 통한 다양성 제어 (0.0 ~ 1.0) |
| max_tokens           | integer     | 2048             | 생성할 최대 토큰 수 |
| stream               | boolean     | false            | 응답 스트리밍 여부 |
| max_attempts         | integer     | 3                | 실패한 요청에 대한 최대 재시도 횟수 |

## 사용 방법

1. 워크플로에 Groq Prompt Driver 노드를 추가합니다.
1. [Groq API Keys](https://console.groq.com/keys)에서 API 키를 발급받았는지 확인합니다.
1. Griptape 환경 설정에서 `GROQ_API_KEY`를 구성합니다.
1. 사용 가능한 옵션 중에서 원하는 모델을 선택합니다.
1. 필요에 따라 생성 파라미터를 조정합니다.
1. 워크플로 내의 다른 노드에 연결합니다.

## 참고 사항

- Groq Prompt Driver는 `https://api.groq.com/openai/v1`에 위치한 OpenAI 호환 API 엔드포인트를 사용합니다.
- 프리뷰 모델은 평가 목적으로 제공되며 예고 없이 지원이 중단될 수 있습니다.
- 실제 프로덕션 환경에는 프로덕션 모델 사용을 권장합니다.
- 노드는 워크플로 실행 전에 API 키 유효성 검사를 자동으로 처리합니다.
