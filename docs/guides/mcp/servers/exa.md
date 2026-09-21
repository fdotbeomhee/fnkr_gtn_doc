# Exa MCP 서버

**[Exa MCP Server](https://github.com/exa-labs/exa-mcp-server)**는 Exa AI의 고급 검색 엔진을 통해 강력한 웹 검색 및 리서치 기능을 제공합니다. 로컬 및 원격 배포 옵션을 모두 제공하며 코드 검색, 웹 리서치 및 콘텐츠 추출을 위한 특화된 도구를 지원합니다.

## 설치

Exa를 사용하는 가장 쉬운 방법은 호스팅되는 MCP 서버를 이용하는 것입니다:

1. **Griptape Nodes를 열고** **Settings** → **MCP Servers**로 이동합니다.

1. **+ New MCP Server를 클릭합니다.**

1. **서버를 구성합니다**:

    - **Server Name/ID**: `exa`
    - **Connection Type**: `Streamable HTTP`
    - **Configuration JSON**:

    ```json
    {
        "transport": "streamable_http",
        "url": "https://mcp.exa.ai/mcp",
        "headers": {},
        "timeout": 30,
        "sse_read_timeout": 300,
        "terminate_on_close": true
    }
    ```

1. **Create Server를 클릭합니다.**

## 사용 가능한 도구

- **`get_code_context_exa`** - 수십억 개의 GitHub 저장소, 문서, Stack Overflow에서 관련 코드 예제 검색
- **`web_search_exa`** - 최적화된 결과로 실시간 웹 검색 수행
- **`crawling`** - 특정 URL에서 콘텐츠 추출
- **`company_research`** - 종합적인 기업 정보 수집
- **`linkedin_search`** - LinkedIn에서 기업 및 인물 검색
- **`deep_researcher_start`** - 복잡한 주제에 대한 AI 기반 리서치 시작
- **`deep_researcher_check`** - 종합 리서치 보고서 확인

## 구성 옵션

구성(configuration)에 도구를 추가하여 특정 도구만 활성화할 수 있습니다:

```json
{
  "transport": "streamable_http",
  "url": "https://mcp.exa.ai/mcp",
  "enabled_tools": ["get_code_context_exa", "web_search_exa"]
}
```

사용 가능한 도구 조합:

- **개발자용**: `["get_code_context_exa", "web_search_exa"]`
- **연구원용**: `["web_search_exa", "deep_researcher_start", "deep_researcher_check"]`
- **비즈니스 인텔리전스(BI)용**: `["company_research", "linkedin_search", "web_search_exa"]`
- **모든 도구**: `["get_code_context_exa", "web_search_exa", "company_research", "crawling", "linkedin_search", "deep_researcher_start", "deep_researcher_check"]`

## 문제 해결

### 일반적인 문제

- **연결 문제**: 원격 서버 URL `https://mcp.exa.ai/mcp`를 테스트하고, 인터넷 연결을 확인하며, 방화벽 설정에서 HTTPS 연결을 허용하는지 확인하세요.
- **도구를 사용할 수 없음**: 구성에서 해당 도구가 활성화되어 있는지 확인하고, 도구 이름의 철자를 검토하며, 현재 Exa 서비스에서 도구를 사용할 수 있는지 확인하세요.

### 디버깅 팁

1. 먼저 간단한 쿼리로 테스트합니다.
1. Exa 대시보드에서 사용량 및 오류를 확인합니다.
1. 문제 해결을 더 쉽게 하려면 원격 서버를 활용하세요.
1. 고급 기능을 사용하기 전에 기본 도구부터 시작하세요.

## 참고 자료

- [Exa MCP Server](https://mcp.exa.ai/mcp) - 공식 서버 엔드포인트
- [Exa AI](https://exa.ai) - Exa AI 플랫폼 및 설명서
