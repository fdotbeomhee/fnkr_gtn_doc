# WebSocket 연결

**websocket** 연결 유형은 Griptape Nodes가 WebSocket 프로토콜을 사용하여 MCP 서버와 전이중(full-duplex) 실시간 통신을 수행할 수 있도록 지원합니다.

> **참고**: WebSocket은 공식 MCP 전송(transport) 유형은 아니지만, [MCP 사양](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports)에 따라 커스텀 전송으로 구현할 수 있습니다.

## WebSocket이란?

WebSocket은 단일 TCP 연결을 통해 전이중 통신 채널을 제공하는 통신 프로토콜입니다. HTTP와 달리 WebSocket은 클라이언트와 서버 양측 모두가 언제든지 데이터를 전송할 수 있도록 지원합니다.

## 출시 예정

MCP 서버에 대한 WebSocket 지원은 아직 초기 단계입니다. 이 연결 유형은 커스텀 전송으로 구현할 수 있지만, 현재 시연에 활용할 수 있는 널리 사용되는 WebSocket 기반 MCP 서버는 없습니다.

**현재 권장하는 대안:**

- **[Streamable HTTP](./streamable_http.md)** - 실제 예제가 포함된 HTTP 기반 통신
- **[Local Process (stdio)](./stdio.md)** - 다양한 예제가 포함된 로컬 서버 연결

## 구성

WebSocket MCP 서버가 출시되면 다음과 유사한 구성을 사용하게 됩니다:

```json
{
  "transport": "websocket",
  "url": "ws://your-websocket-server.com/mcp",
  "headers": {
    "Authorization": "Bearer your-api-key"
  }
}
```

## 다음 단계

- **[Streamable HTTP](./streamable_http.md)** - 실제 예제가 포함된 HTTP 기반 통신
- **[Local Process (stdio)](./stdio.md)** - 다양한 예제가 포함된 로컬 서버 연결
- **[예제 MCP 서버](../servers/index.md)** - 사용 가능한 서버 예제
