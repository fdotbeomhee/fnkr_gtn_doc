# Server-Sent Events (SSE) 연결

⚠️ **지원 중단됨(Deprecated)** - 독립 실행형 전송 방식으로서의 SSE는 **Streamable HTTP**로 대체되어 지원이 중단되었습니다.

## SSE는 어떻게 되었나요?

[공식 MCP 사양](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports)에 따르면, 독립 실행형 전송 방식으로서의 SSE는 프로토콜 버전 2024-11-05에서 지원이 중단되었으며 **Streamable HTTP**로 대체되었습니다.

## 현재 MCP 전송 옵션

현재 MCP 사양은 **두 가지 표준 전송 메커니즘**만 정의합니다:

1. **[stdio](./stdio.md)** - 표준 입력(stdin) 및 표준 출력(stdout)을 통한 통신
1. **[Streamable HTTP](./streamable_http.md)** - 선택적 SSE 지원이 포함된 HTTP 기반 통신

## 이것이 의미하는 바

- **SSE 기능은 여전히 사용 가능합니다** - 단, 독립 실행형 전송 방식이 아니라 Streamable HTTP의 일부로 제공됩니다.
- **Streamable HTTP에서 SSE를 활용할 수 있습니다** - 필요한 경우 서버에서 클라이언트로의 스트리밍에 사용됩니다.
- **독립 실행형 SSE 서버는 제공되지 않습니다** - SSE가 이제 HTTP 전송 방식에 통합되었습니다.

## 마이그레이션 경로

SSE를 사용할 계획이었다면 다음 옵션을 고려하세요:

- **[Streamable HTTP](./streamable_http.md)** - SSE 기능을 포함하는 최신 대체 방식
- **[로컬 프로세스 (stdio)](./stdio.md)** - 로컬 서버 연결용
- **[WebSocket](./websocket.md)** - 전이중 실시간 통신용 (커스텀 전송 방식)

## 다음 단계

- **[Streamable HTTP](./streamable_http.md)** - SSE 기능을 갖춘 최신 HTTP 전송 방식
- **[로컬 프로세스 (stdio)](./stdio.md)** - 풍부한 예제를 갖춘 로컬 서버 연결
- **[예제 MCP 서버](../servers/index.md)** - 사용 가능한 서버 예시 목록
