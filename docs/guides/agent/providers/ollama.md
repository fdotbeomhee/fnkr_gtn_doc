# Ollama (로컬)

[Ollama](https://ollama.com)를 사용하면 로컬 컴퓨터에서 직접 AI 모델을 실행할 수 있습니다. API 키가 필요하지 않으며 어떤 데이터도 컴퓨터 외부로 전송되지 않습니다. Griptape Nodes는 설치된 모델을 동적으로 자동 감지합니다.

## 사전 요구 사항

1. [ollama.com](https://ollama.com)에서 Ollama를 다운로드하고 설치합니다.

1. 하나 이상의 모델을 가져옵니다(pull). 예:

    ```bash
    ollama pull llama3.2
    ```

1. Ollama가 실행 중이고 모델을 사용할 수 있는지 확인합니다:

    ```bash
    ollama list
    ```

    Griptape Nodes에서 Ollama를 사용하려면 항상 Ollama가 실행 중이어야 합니다.

## 제공자 추가하기

1. **Settings → Agent Settings**를 엽니다.
1. **+ Add Provider**를 클릭합니다.
1. **Ollama (local)**을 선택합니다.

<!-- TODO(#5095): screenshot of the "Add Provider — Configure Ollama (local)" step -->

구성 항목을 입력합니다:

- **Name** — `Ollama` 그대로 두거나 사용자 지정 레이블을 입력합니다.
- **Base URL** — 기본값은 `http://localhost:11434/v1`입니다. 새로고침 버튼을 클릭하여 연결을 테스트합니다.
- Ollama가 실행 중이면 녹색으로 **Connected — N models available** 메시지가 표시됩니다.
- **Model (선택 사항)** — 기본 모델을 선택하거나 공란으로 두어 대화 시점에 선택하도록 합니다.

**Create Provider**를 클릭합니다.

## 테스트

생성 후 마법사에 확인 화면이 표시됩니다. 모델을 선택하고 테스트 메시지를 입력한 후, 응답이 정상적으로 오는지 확인하고 **Done**을 클릭하세요.

<!-- TODO(#5095): screenshot of the "Provider Added" step with a successful test response -->

## 모델 선택하기

`ollama pull`로 가져온 모델은 다음 위치에 자동으로 표시됩니다:

- [에이전트](../index.md)의 **Model** 드롭다운 (Ollama 제공자 이름 아래에 그룹화됨)
- `provider`가 Ollama로 설정된 경우 **Agent 노드**의 `prompt model` 파라미터

모델을 더 추가하려면 터미널에서 `ollama pull <model-name>`을 실행하면 즉시 반영됩니다.

## 관련 문서

- [AI 제공자 개요](./index.md)
- [LM Studio](./lm_studio.md) — 또 다른 로컬 제공자 옵션
- [에이전트를 활용한 로컬 모델 (MCP 방식)](../../mcp/advanced_local_models.md) — 워크플로우 내에서 MCP 노드를 통해 Ollama를 사용하는 대체 패턴
