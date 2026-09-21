# AI 제공자 (AI Providers)

Griptape Nodes는 [에이전트](../index.md) 및 워크플로우의 **Agent 노드**를 위해 여러 AI 제공자를 지원합니다. 기본적으로 Griptape Cloud가 제공되며, 로컬 제공자나 OpenAI 호환 엔드포인트를 추가 옵션으로 구성할 수 있습니다.

<!-- TODO(#5095): screenshot of Agent Settings → AI Providers section showing the provider list and "+ Add Provider" button -->

## 사용 가능한 제공자

| 제공자 | 유형 | API 키 필요 여부 |
| ----------------------------------------- | ---------------------- | ---------------- |
| [Griptape Cloud](./griptape_cloud.md) | 호스팅 프록시 (기본값) | Griptape API 키 |
| [Ollama](./ollama.md) | 로컬 | 불필요 |
| [LM Studio](./lm_studio.md) | 로컬 | 불필요 |
| [Custom (OpenAI 호환)](./custom.md) | 호스팅 또는 로컬 | 필요 |

## 제공자 추가하기

**Settings → Agent Settings**를 엽니다. **AI Providers** 섹션에서 **+ Add Provider**를 클릭합니다.

<!-- TODO(#5095): screenshot of the "Add Provider — Choose type" modal -->

추가하려는 제공자 유형을 선택하고 해당 페이지(위 표에 링크됨)의 구성 단계를 따릅니다. 마법사는 3단계(유형 선택 → 구성 → 테스트)로 진행됩니다.

## 제공자 관리하기

Griptape Cloud 이외의 각 제공자에는 세 가지 제어 기능이 있습니다:

| 제어 항목 | 동작 |
| -------------- | ------------------------------------------------------------------------- |
| 토글 (Toggle) | 활성화 또는 비활성화. 비활성화된 제공자는 모델 드롭다운에 표시되지 않습니다. |
| 편집 (연필 아이콘) | 이름, 기본 URL, API 키 또는 기본 모델 업데이트 |
| 삭제 (휴지통 아이콘) | 제공자 제거 |

<!-- TODO(#5095): screenshot showing the toggle, edit, and delete controls on a provider row -->

## 채팅 사이드바에서 제공자 사용하기

제공자가 활성화되면 해당 모델이 에이전트의 **Model** 드롭다운에 제공자 이름별로 그룹화되어 표시됩니다. 모델을 클릭하면 즉시 해당 모델로 전환됩니다.

채팅 인터페이스에 대한 자세한 내용은 [에이전트 사이드바](../index.md)를 참조하세요.

## 워크플로우에서 제공자 사용하기

**Agent 노드**에는 `provider` 파라미터가 있습니다. 활성화된 제공자로 설정하면 해당 노드의 AI 호출에 사용됩니다. `prompt model` 파라미터를 통해 특정 모델을 선택할 수 있습니다.

<!-- TODO(#5095): screenshot of the Agent node showing the provider and prompt model parameters -->

이를 통해 동일한 워크플로우 내에서 서로 다른 노드가 서로 다른 제공자를 사용하도록 설정할 수 있습니다.
