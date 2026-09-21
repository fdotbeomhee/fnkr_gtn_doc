# InfoRetriever

## 어떤 노드인가요?

InfoRetriever는 워크플로에 검색 증강 생성(Retrieval Augmented Generation, RAG) 기능을 제공합니다. AI 응답을 향상시키기 위해 관련 정보를 찾아 활용할 수 있는 똑똑한 연구원과 같다고 생각할 수 있습니다.

## 언제 사용하나요?

다음과 같은 경우에 이 노드를 사용합니다:

- 사용자의 자체 데이터 소스를 기반으로 AI 응답을 생성(Grounding)할 때
- 에이전트가 특정 정보를 검색하고 참조할 수 있도록 지원할 때
- 관련 컨텍스트를 제공하여 응답의 정확성을 높일 때
- 대화형 에이전트에 지식 베이스(Knowledge Base)를 연결할 때

## 사용 방법

### 기본 설정

1. 워크플로에 InfoRetriever를 추가합니다.
1. 출력을 RAG 기능이 필요한 노드(예: Agent)에 연결합니다.

### 파라미터 (Parameters)

- **description**: 이 도구가 제공하는 정보에 대한 설명 (기본값: "Contains information")
- **off_prompt**: 메인 프롬프트 외부에서 RAG 연산을 실행할지 여부 (기본값: true)
- **rag_engine**: 정보를 검색하는 데 사용되는 엔진 (필수 항목)

### 출력 (Outputs)

- **tool**: 다른 노드에서 사용할 수 있도록 구성된 RAG 도구
- **rules**: RAG 도구에서 사용되는 규칙 세트(Ruleset)

## 예시

회사 문서를 활용하여 질문에 답변할 수 있는 에이전트를 생성하려는 경우:

1. 워크플로에 InfoRetriever를 추가합니다.
1. 문서가 포함된 벡터 저장소(Vector Store)를 "rag_engine" 입력에 연결합니다.
1. "tool" 출력을 Agent의 "tools" 입력에 연결합니다.
1. 이제 해당 에이전트는 질문에 답변할 때 회사 문서에서 특정 정보를 검색하고 참조할 수 있습니다.
