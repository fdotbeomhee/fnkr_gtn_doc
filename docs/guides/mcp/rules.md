# MCP 서버 규칙 (Rules)

MCP 서버 규칙을 사용하면 AI 에이전트가 특정 MCP 서버의 도구를 사용할 때 적용할 커스텀 지침을 제공할 수 있습니다. 이 규칙들은 에이전트가 해당 서버의 도구를 사용할 때마다 규칙 세트(Ruleset)로 자동 적용되어, 일관되고 적절한 동작을 보장하도록 도와줍니다.

## 규칙이란 무엇인가요?

규칙은 AI 에이전트가 특정 MCP 서버와 상호작용하는 방식을 안내하는 텍스트 지침입니다. MCP 서버 구성에 추가되며, 다음의 경우에 자동으로 적용됩니다:

- 해당 서버와 함께 **MCPTask** 노드를 사용할 때
- 해당 서버가 구성된 **Agent** 노드를 사용할 때

## 왜 규칙을 사용해야 하나요?

규칙을 활용하면 다음과 같은 이점이 있습니다:

- **에이전트 동작 가이드**: 서버의 도구를 사용하는 구체적인 지침을 제공합니다
- **일관성 확보**: 에이전트가 선호하는 패턴을 따르도록 합니다
- **예외 상황 처리**: 에러나 특수한 상황을 처리하는 방법을 에이전트에 안내합니다
- **사용 최적화**: 에이전트가 서버의 기능을 가장 효과적으로 사용하도록 유도합니다

## MCP 서버에 규칙 추가하기

### 서버를 생성할 때

새 MCP 서버를 생성할 때 **Rules** 텍스트 영역에 규칙을 추가할 수 있습니다:

1. **Settings** → **MCP Servers**로 이동합니다.
1. **+ New MCP Server**를 클릭합니다.
1. 서버 구성(이름, 연결 유형 등)을 입력합니다.
1. **Rules** 텍스트 영역에 커스텀 규칙을 입력합니다.
1. **Create Server**를 클릭합니다.

### 서버를 편집할 때

1. **Settings** → **MCP Servers**로 이동합니다.
1. 수정하려는 서버의 **Edit** 버튼을 클릭합니다.
1. **Rules** 텍스트 영역의 내용을 수정합니다.
1. 변경 사항을 저장합니다.

## 예제 규칙

### 웹 가져오기(Web Fetching) 서버용

```
Always validate URLs before fetching. Check that URLs use HTTPS when possible. If a fetch fails, return a clear error message explaining what went wrong.
```

### 파일 시스템(File System) 서버용

```
Always check if a file exists before attempting to read it. Use absolute paths when possible. Never delete files without explicit user confirmation.
```

### 검색(Search) 서버용

```
Always verify search results are relevant before returning them. If no relevant results are found, suggest alternative search terms. Format results in a clear, readable structure.
```

### 데이터베이스(Database) 서버용

```
Always validate SQL queries before executing them. Never execute DROP or DELETE operations without explicit confirmation. Return query results in a structured format.
```

## 규칙 작동 원리

1. **저장**: 규칙은 MCP 서버 구성의 일부로 저장됩니다.
1. **적용**: 에이전트가 해당 서버의 도구를 사용할 때 규칙이 규칙 세트(Ruleset)로 자동 추가됩니다.
1. **범위**: 규칙은 해당 특정 MCP 서버를 사용할 때만 적용됩니다.
1. **형식**: 규칙은 단일 문자열이며, 마침표나 줄바꿈으로 구분된 여러 지침을 포함할 수 있습니다.

## 모범 사례

### 명확하고 구체적으로 작성하세요

✅ **좋은 예**: "Always validate URLs before fetching. Check for HTTPS and return clear error messages."

❌ **모호한 예**: "Be careful with URLs."

### 동작에 초점을 맞추세요

✅ **좋은 예**: "Return errors in JSON format with 'error' and 'message' fields."

❌ **너무 일반적인 예**: "Handle errors well."

### 간결하게 유지하세요

✅ **좋은 예**: "Validate inputs before processing. Return structured JSON responses."

❌ **너무 긴 예**: 가능한 모든 시나리오를 구구절절 설명하는 긴 문단.

### 규칙을 테스트하세요

규칙을 추가한 후 MCP 서버로 테스트하여 예상대로 작동하는지 확인하세요:

1. 해당 서버를 사용하는 MCPTask 노드를 생성합니다.
1. 테스트 프롬프트를 실행합니다.
1. 에이전트가 지정한 규칙을 따르는지 확인합니다.

## 다양한 컨텍스트에서의 규칙

### MCPTask 노드

MCPTask 노드를 사용할 때, 선택한 MCP 서버의 규칙이 작업을 처리하는 에이전트에 자동으로 적용됩니다.

### Agent 노드

MCP 서버가 구성된 Agent 노드를 사용할 때, 활성화된 모든 MCP 서버의 규칙이 수집되어 에이전트에 적용됩니다.

## 문제 해결

### 규칙이 적용되지 않는 경우

- Rules 텍스트 영역에 규칙이 입력되어 있는지 확인합니다(비어 있지 않은지 확인).
- 서버가 활성화되어 있는지 확인합니다.
- 워크플로우에서 올바른 서버 이름을 사용하고 있는지 확인합니다.

### 에이전트가 규칙을 따르지 않는 경우

- 규칙을 더 구체적이고 실행 가능하게 만듭니다.
- 먼저 더 단순한 규칙으로 테스트해 봅니다.
- 규칙이 해당 서버의 기능에 적합한지 확인합니다.

## 다음 단계

- **[시작하기 튜토리얼](./getting_started.md)** - 첫 번째 MCP 서버 설정 방법 알아보기
- **[연결 유형](./index.md#연결-유형)** - 다양한 연결 방식 알아보기
- **[예제 서버](./servers/index.md)** - 구성된 MCP 서버 예제 확인하기
