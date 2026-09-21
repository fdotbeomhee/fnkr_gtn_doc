# Fetch MCP 서버

**[Fetch MCP Server](https://github.com/modelcontextprotocol/servers/tree/main/src/fetch)**는 웹 콘텐츠 가져오기 기능을 제공하여 AI 에이전트가 웹 페이지의 콘텐츠를 검색하고 처리할 수 있도록 합니다. HTML을 사용하기 쉬운 마크다운(Markdown)으로 변환해주므로 리서치, 콘텐츠 분석 및 웹 스크래핑 작업에 이상적입니다.

## 설치

1. **Griptape Nodes를 열고** **Settings** → **MCP Servers**로 이동합니다.

1. **+ New MCP Server를 클릭합니다.**

1. **서버를 구성합니다**:

    - **Server Name/ID**: `fetch`
    - **Connection Type**: `Local Process (stdio)`
    - **Configuration JSON**:

    ```json
    {
    "transport": "stdio",
    "command": "uvx",
    "args": ["mcp-server-fetch"],
    "env": {},
    "encoding": "utf-8",
    "encoding_error_handler": "strict"
    }
    ```

1. **Create Server를 클릭합니다.**

## 사용 가능한 도구

- **`fetch`** - 웹 URL에서 콘텐츠를 가져와 마크다운으로 변환

## 구성 옵션

환경 변수를 사용하여 Fetch 서버의 동작을 사용자 지정할 수 있습니다:

```json
{
  "transport": "stdio",
  "command": "uvx",
  "args": ["mcp-server-fetch"],
  "env": {
    "FETCH_TIMEOUT": "30000",
    "FETCH_USER_AGENT": "MyApp/1.0"
  },
  "encoding": "utf-8",
  "encoding_error_handler": "strict"
}
```

사용 가능한 환경 변수:

- **`FETCH_TIMEOUT`** - 밀리초 단위의 요청 제한 시간 (기본값: `30000`)
- **`FETCH_USER_AGENT`** - 요청 시 사용할 User-Agent 문자열 (기본값: `mcp-server-fetch`)
- **`FETCH_MAX_SIZE`** - 바이트 단위의 최대 응답 크기 (기본값: `10485760` (10MB))

## 문제 해결

### 일반적인 문제

- **서버가 응답하지 않음**: 인터넷 연결을 확인하고, 브라우저에서 해당 URL에 접근할 수 있는지 확인하며, 다른 URL로 서버를 테스트해 보세요.
- **콘텐츠가 로드되지 않음**: 일부 웹사이트는 자동화된 요청을 차단하므로, 커스텀 User-Agent를 추가해 보거나 사이트에 인증이 필요한지 확인하세요.
- **시간 초과 오류**: `FETCH_TIMEOUT` 값을 늘리거나, 더 작고 단순한 페이지부터 테스트해 보고, 네트워크 연결 속도를 확인하세요.
- **잘못된 URL**: URL에 프로토콜(http:// 또는 https://)이 포함되어 있는지 확인하고, 오탈자가 없는지 검토하며, 웹사이트에 정상적으로 접근 가능한지 확인하세요.

## 참고 자료

- [Fetch MCP Server](https://github.com/modelcontextprotocol/servers/tree/main/src/fetch) - 공식 저장소 및 설명서
