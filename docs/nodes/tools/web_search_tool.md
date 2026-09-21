# Web Search

## 어떤 노드인가요?

Web Search 도구는 에이전트가 웹을 검색할 수 있도록 기능을 제공하는 유틸리티 도구입니다. DuckDuckGo, Google, Exa를 포함한 여러 검색 엔진을 지원합니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- 에이전트가 웹에서 정보를 검색할 수 있도록 지원할 때
- 실시간 웹 데이터에 접근할 때
- 리서치 및 조사 작업을 수행할 때
- 인터넷에서 최신 정보를 가져올 때

## 사용 방법

### 기본 설정

1. 워크플로에 Web Search 도구를 추가합니다.
1. 출력을 웹 검색 기능이 필요한 노드(예: Agent)에 연결합니다.

### 파라미터 (Parameters)

- **search_engine**: 사용할 검색 엔진 (기본값: "DuckDuckGo")

    - 선택 옵션:

        - DuckDuckGo: 무료, API 키 불필요
        - Google: Google API 키 및 Search ID 필요
        - Exa: Exa API 키 필요

### 출력 (Outputs)

- **tool**: 다른 노드에서 사용할 수 있도록 구성된 웹 검색 도구

## 예시

웹을 검색할 수 있는 에이전트를 생성하려는 경우:

1. 워크플로에 Web Search 도구를 추가합니다.
1. "tool" 출력을 Agent의 "tools" 입력에 연결합니다.
1. 이제 해당 에이전트는 대화 중 필요할 때 웹 검색을 수행할 수 있습니다.

## 구현 세부사항

Web Search 도구는 Griptape의 `WebSearchTool` 클래스를 사용하여 구현되었으며, 다양한 검색 엔진 드라이버를 지원합니다:

- `DuckDuckGoWebSearchDriver`: 무료, API 키 불필요
- `GoogleWebSearchDriver`: Google API 키 및 Search ID 필요
- `ExaWebSearchDriver`: Exa API 키 필요

Google 또는 Exa 검색 엔진을 사용할 때는 구성 설정에서 적절한 API 키를 설정해야 합니다. 도구는 선택한 엔진과의 인증 및 검색 작업을 자동으로 처리합니다.
