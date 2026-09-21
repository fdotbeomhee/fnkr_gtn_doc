# Agent

## 어떤 노드인가요?

Agent 노드를 사용하면 도구(Tools) 및 규칙 세트(Rulesets)와 같은 맞춤형 기능을 갖춘 AI 에이전트를 구성할 수 있습니다. 이 노드는 자체 프롬프트를 통해 즉시 응답을 생성하는 에이전트를 만들 수도 있고, 다른 노드의 "agent" 입력으로 전달할 수도 있습니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- 구성 가능한 AI 에이전트를 처음부터 새로 생성할 때
- 특정 도구 및 규칙 세트를 갖춘 에이전트를 설정할 때
- 워크플로 전반에서 재사용할 수 있는 에이전트를 준비할 때
- 커스텀 프롬프트를 사용하여 에이전트로부터 즉각적인 응답을 얻고자 할 때

## 사용 방법

### 기본 설정

1. 워크플로에 Agent 노드를 추가합니다.
1. 에이전트의 기능(도구 및 규칙 세트)을 구성합니다.

### 파라미터 (Parameters)

- **agent**: 기존 Agent 구성(선택 사항). 지정된 경우 프롬프팅 시 기존 Agent를 사용합니다.
- **provider**: 사용할 AI 제공업체(예: Griptape Cloud, Ollama, LM Studio 또는 커스텀 엔드포인트). 설정 방법은 [AI Providers](../../guides/agent/providers/index.md)를 참조하세요.
- **prompt model**: 선택한 제공업체의 특정 모델.
- **prompt**: 에이전트에게 전달할 지시사항 또는 질문.
- **additional_context**: 에이전트에게 추가 컨텍스트를 제공하는 문자열 또는 키-값 쌍.
- **tools**: 에이전트에게 부여할 기능(도구).
- **rulesets**: 에이전트가 수행할 수 있는 작업과 수행할 수 없는 작업을 지정하는 규칙.
- **output_schema**: 에이전트의 응답이 따라야 할 정확한 형식을 정의하는 JSON Schema 템플릿(선택 사항).

### 출력 (Outputs)

- **output**: 에이전트의 텍스트 응답 (프롬프트가 제공된 경우)
- **agent**: 다른 노드에 연결할 수 있는 구성된 에이전트 객체

## 예시

prompt_context를 기반으로 하이쿠를 작성할 수 있는 에이전트를 만드는 예시입니다:

1. KeyValuePair 노드를 추가합니다.
1. "key"를 "topic"으로, "value"를 "swimming"으로 설정합니다.
1. Agent 노드를 추가합니다.
1. Agent의 "prompt"를 "Write me a haiku about {{topic}}"으로 설정합니다.
1. KeyValuePair의 dictionary 출력을 Agent의 "prompt_context" 입력에 연결합니다.
1. 워크플로를 실행합니다.
1. Agent의 "output"에 수영에 관한 하이쿠가 생성됩니다!

## 출력 스키마(Output Schema) 사용하기

### 출력 스키마란 무엇인가요?

출력 스키마는 AI에게 작성을 요청하는 일종의 '양식(Form)'이라고 생각하면 됩니다. 자유 형식의 텍스트 응답을 받는 대신, 원하는 정보 항목과 구성 방식을 정확히 지정할 수 있습니다.

예를 들어, "이 제품에 대해 알려주세요"라고 질문하여 줄글 형태의 긴 텍스트를 받는 대신 다음과 같이 요청할 수 있습니다:

- 제품명 (텍스트)
- 가격 (숫자)
- 재고 유무 (예/아니오)
- 주요 기능 목록 (여러 개의 텍스트 항목)

그러면 AI는 요청한 내용과 정확히 일치하는 구조화된 데이터로 응답합니다.

### 출력 스키마를 사용하는 이유

다음과 같은 상황에서 출력 스키마를 사용합니다:

- **일관된 형식**: 모든 응답이 동일한 구조를 따르므로 후속 처리가 훨씬 수월해집니다.
- **특정 데이터 타입 보장**: 숫자가 필요한 곳에는 숫자, 목록이 필요한 곳에는 목록이 정확히 반환되도록 보장합니다.
- **손쉬운 자동화**: 구조화된 데이터는 워크플로 내의 다른 노드와 연결하기가 훨씬 쉽습니다.
- **유효성 검증 (Validation)**: AI는 필수 필드를 모두 올바른 형식으로 제공해야 합니다.

### 출력 스키마 생성 방법

1. 워크플로에 **JSON Input** 노드를 추가합니다.
1. 원하는 출력을 정의하는 JSON Schema를 추가합니다(온라인 도구를 활용하여 쉽게 생성할 수 있습니다).

### 출력 스키마 예시

리뷰에서 레스토랑 정보를 추출하려는 경우:

1. 스키마 필드 생성:

    - 필드 "restaurant_name" (type: string)
    - 필드 "rating" (type: integer)
    - 필드 "price_range" (type: string)
    - 필드 "cuisine_type" (type: string)
    - 필드 "recommended_dishes" (type: list, list_type: string)

1. 모든 필드를 Create Schema 노드에 연결합니다.

1. 해당 스키마를 Agent의 output_schema 입력에 연결합니다.

1. Agent의 프롬프트를 설정합니다: "Extract restaurant information from this review: [리뷰 텍스트]"

1. 이제 Agent는 일반 텍스트 대신 정확히 해당 필드들을 포함하는 구조화된 데이터로 응답합니다.

1. 생성된 스키마는 다음과 같은 형태가 됩니다:

```json
{
  "type": "object",
  "properties": {
    "restaurant_name": { "type": "string" },
    "rating": { "type": "integer" },
    "price_range": { "type": "string" },
    "cuisine_type": { "type": "string" },
    "recommended_dishes": {
      "type": "array",
      "items": { "type": "string" }
    }
  },
  "required": ["restaurant_name", "rating", "price_range", "cuisine_type", "recommended_dishes"]
}
```

### 스키마 사용 시 변경되는 점

- **출력 타입**: Agent의 출력이 일반 텍스트에서 구조화된 데이터(JSON 형식)로 변경됩니다.
- **유효성 검증**: AI가 요청된 형식으로 데이터를 제공하지 못하면 재시도하거나 오류를 반환합니다.

## 중요 참고 사항

- 프롬프트를 제공하지 않으면 노드는 에이전트를 실행하지 않고 생성만 수행하며, 출력에는 정확히 "Agent Created"가 포함됩니다.
- 이 노드는 스트리밍 및 비스트리밍 프롬프트 드라이버를 모두 지원합니다.
- 도구(Tools)와 규칙 세트(Rulesets)는 개별 항목 또는 목록 형태로 제공할 수 있습니다.
- additional_context 파라미터를 통해 문자열 또는 키/값 쌍의 딕셔너리로 에이전트에게 추가 컨텍스트를 제공할 수 있습니다.
- 기본적으로 이 노드는 Griptape Cloud를 사용하며 유효한 `GT_CLOUD_API_KEY`가 필요합니다. **provider** 파라미터를 사용하여 로컬 또는 커스텀 제공업체로 전환할 수 있습니다([AI Providers](../../guides/agent/providers/index.md) 참조).
- agent 입력/출력 핀을 사용하여 한 노드에서 다른 노드로 Agent를 전달하면 대화 메모리가 유지됩니다. 즉:
    - Agent는 동일한 흐름 내의 이전 상호작용을 "기억"합니다.
    - 이전 프롬프트의 컨텍스트가 Agent가 새 프롬프트를 해석하는 방식에 영향을 줍니다.
    - 여러 노드에 걸쳐 멀티턴(Multi-turn) 대화를 구축할 수 있습니다.
    - Agent는 워크플로의 이전 단계에서 제공된 정보를 참조할 수 있습니다.
- output_schema 파라미터를 위한 JSON Schema 작성법을 잘 모르시나요? [JSON Schema Builder](https://transform.tools/json-to-json-schema)와 같은 온라인 도구를 사용하여 원하는 출력 구조를 기반으로 스키마를 생성해 보세요. 또는 Agent에게 원하는 출력 예시를 제공하고 스키마를 직접 생성하도록 요청할 수도 있습니다!

## 워크플로에서 에이전트 동작 제어하기

워크플로에서 Agent의 동작 방식을 형성하려면 **Rulesets**(또는 `Behaviors (Rulesets)` 입력에 연결된 일반 **TextInput**)를 사용하세요. 이를 통해 에이전트가 수행할 수 있는 작업과 수행할 수 없는 작업을 정의하고, 페르소나를 설정하거나 출력 스타일을 제한할 수 있습니다.

!!! note "Agent 노드에서는 Skill이 지원되지 않습니다"

    [채팅 사이드바](../../guides/agent/skills.md)에서 사용되는 `.agents/skills` 폴더는 워크플로의 Agent 노드에는 적용되지 않습니다. 대신 에이전트 동작을 제어하려면 **Ruleset 노드** 또는 Agent의 `Behaviors (Rulesets)` 입력에 연결된 **TextInput**을 사용하세요.

## 자주 묻는 질문 및 문제 해결

- **제공업체가 구성되지 않음**: 제공업체가 설정되지 않은 경우 노드는 기본적으로 Griptape Cloud를 사용하며 유효한 `GT_CLOUD_API_KEY`가 필요합니다. Ollama, LM Studio 또는 커스텀 엔드포인트를 추가하려면 [AI Providers](../../guides/agent/providers/index.md)를 참조하세요.
- **스트리밍 문제**: 스트리밍 프롬프트 드라이버를 사용하는 경우 워크플로가 스트리밍 출력을 처리할 수 있도록 구성되어 있는지 확인하세요.
