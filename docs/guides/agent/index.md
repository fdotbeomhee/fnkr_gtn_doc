# 에이전트 (Agent)

에이전트는 워크플로우에 직접 접근할 수 있는 내장 AI 어시스턴트입니다. 질문을 하거나, 노드를 생성하고, 워크플로우를 실행하며, 캔버스에 있는 내용을 검사하는 등 모든 작업을 대화 형태로 진행할 수 있습니다.

<!-- TODO(#5095): screenshot of the full sidebar showing the chat tab active, the model dropdown, and a sample conversation -->

## 사이드바 열기

사이드바는 Griptape Nodes 캔버스의 오른쪽에 위치합니다. 사이드바 패널(Sidebar Panels) 헤더의 첫 번째 아이콘인 **말풍선(chat bubble)** 탭을 클릭하여 엽니다.

<!-- TODO(#5095): screenshot highlighting the Sidebar Panels tabs, with the chat tab indicated -->

## 모델 선택

채팅 패널 상단에서 **Model** 드롭다운을 클릭하면 제공자(Provider)별로 그룹화된 사용 가능한 모든 모델을 확인할 수 있습니다.

<!-- TODO(#5095): screenshot of the model dropdown open, showing Griptape Cloud models at top and an Ollama section below -->

- 최상단 그룹에는 **Griptape Cloud** 모델(Claude, GPT, Gemini, DeepSeek, Llama 등)이 포함되어 있으며, 별도의 설정 없이 기본적으로 사용할 수 있습니다.
- 추가로 구성한 제공자가 있는 경우 아래에 해당 레이블의 섹션으로 표시됩니다.
- **Search models...**를 사용하여 이름으로 모델을 필터링할 수 있습니다.
- 하단의 **Manage providers...**를 클릭하여 [제공자 (Providers)](./providers/index.md) 설정을 열 수 있습니다.

## 메시지 보내기

하단의 **Write a message...** 입력창에 메시지를 입력하고 **Enter** 키를 누르거나 전송 버튼을 클릭합니다. 클립 아이콘을 사용하여 파일을 첨부할 수도 있습니다.

에이전트는 실시간으로 응답을 스트리밍합니다. 캔버스를 읽거나 워크플로우를 실행하는 등의 작업을 수행할 때는 단계별 진행 상황이 인라인으로 표시되므로 에이전트가 수행 중인 작업을 항상 확인할 수 있습니다.

<!-- TODO(#5095): screenshot of an in-progress response with a tool call visible -->

예를 들어 다음과 같이 요청할 수 있습니다:

- **"내 워크플로우에 어떤 노드가 있나요?"** — 캔버스를 읽고 어떤 노드가 있는지 알려줍니다.
- **"Text Input 노드를 추가하고 Agent에 연결해 주세요"** — 노드를 생성하고 연결해 줍니다.
- **"내 워크플로우를 실행하고 출력이 무엇인지 알려주세요"** — 워크플로우를 실행하고 결과를 보고합니다.
- **"Agent 노드의 프롬프트를 ...로 변경해 주세요"** — 노드 파라미터를 직접 업데이트합니다.
- **"이 워크플로우가 무엇을 하는지 설명해 주세요"** — 워크플로우 구조를 읽고 알기 쉬운 요약을 제공합니다.

!!! tip "에이전트는 어떻게 워크플로우를 제어하나요?"

    Griptape Nodes는 엔진과 함께 기본 내장된 **MCP 서버**를 실행합니다. MCP(Model Context Protocol)는 AI 모델이 도구를 호출할 수 있도록 지원하는 개방형 표준으로, 여기서는 캔버스 읽기, 노드 생성, 워크플로우 실행 등을 위한 도구를 제공합니다. 에이전트는 이 서버에 자동으로 연결되므로 별도의 설정이 필요하지 않습니다.

## 다음 단계

- [스레드 (Threads)](./threads.md) — 대화가 저장되는 방식 및 확인 위치
- [개인화 (Personalization)](./personalization.md) — 에이전트의 어조와 컨텍스트 맞춤 설정
- [스킬 (Skills)](./skills.md) — 에이전트에 추가적인 도메인 지식 제공
- [외부 도구 (MCP 서버)](./mcp_servers.md) — 파일 접근, 웹 검색 및 기타 도구를 에이전트에 연결
- [제공자 (Providers)](./providers/index.md) — Ollama, LM Studio 또는 사용자 지정 엔드포인트 추가
- [워크플로우에서 사용하기](./using_in_workflows.md) — 자동화된 플로우를 위한 Agent 노드
