# Streamable HTTP 연결

**streamable_http** 연결 유형을 사용하면 Griptape Nodes가 **양방향 스트리밍** (클라이언트 ↔ 서버) 및 실시간 통신을 지원하는 HTTP를 통해 MCP 서버와 통신할 수 있습니다.

## Streamable HTTP를 사용하는 경우

- **양방향 통신**: 클라이언트와 서버가 모두 데이터를 전송해야 할 때
- **인터랙티브 애플리케이션**: 실시간 채팅, 동시 편집, 실시간 협업
- **HTTP 인프라**: 기존 HTTP 기반 시스템 활용
- **커스텀 스트리밍**: SSE가 제공하는 것 이상의 제어가 필요할 때
- **세션 관리**: 영구적인 세션 상태 유지가 필요한 애플리케이션

## 사용 가능한 Streamable HTTP MCP 서버

- **[Exa](../servers/exa.md)** - 고급 웹 검색 및 리서치 기능

## Streamable HTTP MCP 서버 예제 구성

### 채팅 애플리케이션 서버

```json
{
  "name": "chat_app",
  "transport": "streamable_http",
  "url": "https://api.chat-service.com/mcp/stream",
  "headers": {
    "Authorization": "Bearer chat-token"
  },
  "timeout": 60,
  "sse_read_timeout": 120,
  "terminate_on_close": false,
  "description": "실시간 메시징 및 통신"
}
```

## 주요 Streamable HTTP 사용 사례

- **채팅 애플리케이션** - 실시간 메시징 및 대화
- **공동 편집** - 문서 공유 편집 (Google Docs 등)
- **실시간 협업** - 팀 워크스페이스 및 공유 화이트보드
- **인터랙티브 대시보드** - 실시간 데이터 시각화 및 상호작용
- **고객 지원** - 라이브 채팅 및 상담 시스템
- **온라인 게임** - 턴제 및 실시간 멀티플레이어 게임

## 구성

### 필수 필드

| 필드 | 타입 | 설명 | 예시 |
| --- | --- | --- | --- |
| `url` | string | MCP 서버의 HTTP 엔드포인트 | `"https://api.example.com/mcp"` |

### 선택적 필드

| 필드 | 타입 | 설명 | 기본값 |
| --- | --- | --- | --- |
| `headers` | object | 인증용 HTTP 헤더 | `{}` |
| `timeout` | number | 요청 제한 시간(초) | `30` |
| `sse_read_timeout` | number | SSE 읽기 제한 시간(초) | `60` |
| `terminate_on_close` | boolean | 연결 종료 시 세션 종료 여부 | `true` |

## 예제 구성

### 기본 Streamable HTTP

```json
{
  "name": "streamable_api",
  "transport": "streamable_http",
  "url": "https://api.example.com/mcp/stream",
  "description": "양방향 스트리밍(클라이언트 ↔ 서버)을 지원하는 HTTP API"
}
```

### 인증이 필요한 Streamable HTTP

```json
{
  "name": "auth_streamable",
  "transport": "streamable_http",
  "url": "https://api.example.com/mcp/stream",
  "headers": {
    "Authorization": "Bearer your-token-here",
    "Content-Type": "application/json",
    "Accept": "application/json"
  },
  "timeout": 60,
  "sse_read_timeout": 120,
  "terminate_on_close": true
}
```

### 커스텀 구성

```json
{
  "name": "custom_streamable",
  "transport": "streamable_http",
  "url": "https://mcp.example.com/stream",
  "headers": {
    "X-API-Key": "your-api-key",
    "X-Client-Version": "1.0.0",
    "User-Agent": "GriptapeNodes/1.0"
  },
  "timeout": 90,
  "sse_read_timeout": 300,
  "terminate_on_close": false
}
```

## 설정 단계

### 1. MCP 서버 배포

MCP 서버가 streamable HTTP를 지원하는지 확인하세요:

```python
# Streamable HTTP 엔드포인트 예시
@app.post("/mcp/stream")
async def mcp_stream(request: Request):
    return StreamingResponse(
        process_mcp_stream(request),
        media_type="application/json",
        headers={"Cache-Control": "no-cache", "Connection": "keep-alive"},
    )
```

### 2. Griptape Nodes에서 구성

1. Griptape Nodes 설정을 엽니다.
1. MCP Server 구성 메뉴로 이동합니다.
1. streamable_http 전송 방식을 사용하는 새 서버를 추가합니다.
1. 서버 URL을 입력합니다.
1. 인증 및 타임아웃을 설정합니다.
1. 연결을 테스트합니다.

### 3. 워크플로우에서 사용

1. 플로우에 MCPTask 노드를 추가합니다.
1. 구성된 streamable HTTP 서버를 선택합니다.
1. 프롬프트를 입력합니다.
1. 워크플로우를 실행합니다.

## 장점

- **양방향 스트리밍**: 전이중(Full duplex) 통신 (클라이언트 ↔ 서버)
- **HTTP 호환성**: 표준 웹 인프라와 호환
- **실시간 업데이트**: 양방향 실시간 데이터 스트리밍
- **세션 관리**: 기본 제공되는 세션 처리
- **커스텀 구현 가능**: 스트리밍 동작에 대한 세부 제어 가능
- **인터랙티브 애플리케이션**: 실시간 협업에 이상적

## 제한 사항

- **HTTP 오버헤드**: 직접 연결 방식보다 오버헤드가 큼
- **네트워크 종속성**: 안정적인 네트워크 연결 필요
- **복잡성**: 단순한 HTTP 요청보다 구현이 복잡함
- **리소스 사용량**: 더 많은 리소스 소비
- **커스텀 구현 부담**: SSE에 비해 더 많은 개발 작업 필요

## Streamable HTTP vs SSE 비교

| 기능 | Streamable HTTP | SSE |
| --- | --- | --- |
| **방향성** | 양방향 (클라이언트 ↔ 서버) | 단방향 (서버 → 클라이언트) |
| **프로토콜** | 커스텀 HTTP 스트리밍 | 표준화됨 (`text/event-stream`) |
| **사용 사례** | 인터랙티브 앱, 실시간 채팅 | 알림, 라이브 피드, 모니터링 |
| **구현 방식** | 커스텀 클라이언트/서버 로직 | 브라우저 기본 지원 |
| **재연결** | 수동 구현 필요 | 자동 재연결 지원 |
| **예시** | 채팅 애플리케이션, 협업 편집 | 주가 시세 표시기, 뉴스 피드 |

## 인증

### Bearer 토큰

```json
{
  "headers": {
    "Authorization": "Bearer your-jwt-token"
  }
}
```

### API 키

```json
{
  "headers": {
    "X-API-Key": "your-api-key",
    "X-Client-ID": "griptape-nodes"
  }
}
```

### 커스텀 인증

```json
{
  "headers": {
    "X-Custom-Auth": "your-custom-token",
    "X-User-ID": "user123",
    "X-Session-ID": "session456"
  }
}
```

## 세션 관리

### 닫을 때 종료 (Terminate on Close)

```json
{
  "terminate_on_close": true
}
```

- 연결이 닫힐 때 세션을 자동으로 종료합니다
- 상태를 유지하지 않는(stateless) 작업에 유용합니다
- 기본 동작 방식입니다

### 영구 세션 (Persistent Sessions)

```json
{
  "terminate_on_close": false
}
```

- 연결 간에 세션 상태를 유지합니다
- 상태를 유지해야 하는(stateful) 작업에 유용합니다
- 서버 측 세션 관리가 필요합니다

## 문제 해결

### 연결 문제

- 서버 URL에 접근 가능한지 확인합니다
- 네트워크 연결 상태를 확인합니다
- curl이나 Postman으로 테스트합니다
- 서버 로그를 모니터링합니다

### 시간 초과 문제

- 타임아웃 값을 늘립니다
- 서버 응답 시간을 점검합니다
- 네트워크 지연 시간을 모니터링합니다
- 서버 성능을 최적화합니다

### 인증 실패

- 인증 정보가 올바른지 확인합니다
- 토큰 만료 여부를 확인합니다
- 헤더 형식이 올바른지 확인합니다
- 인증 과정을 별도로 테스트합니다

### 스트리밍 문제

- 서버가 스트리밍을 지원하는지 확인합니다
- 올바른 Content-Type인지 확인합니다
- 연결 안정성을 모니터링합니다
- 더 작은 페이로드로 테스트합니다

## 모범 사례

1. **HTTPS 사용**: 항상 안전한 보안 연결을 사용하세요.
1. **재연결 처리**: 자동 재연결 로직을 구현하세요.
1. **세션 모니터링**: 세션 상태를 추적하고 적절히 정리(cleanup)하세요.
1. **타임아웃 최적화**: 작업 특성에 맞는 적절한 타임아웃 값을 설정하세요.
1. **보안 인증 정보 보호**: 민감한 인증 정보를 안전하게 보관하세요.

## 사용 사례 예시

### 인터랙티브 채팅

```json
{
  "name": "chat_interactive",
  "transport": "streamable_http",
  "url": "https://chat.example.com/stream",
  "headers": {
    "Authorization": "Bearer chat-token"
  },
  "terminate_on_close": false
}
```

### 실시간 협업

```json
{
  "name": "collaboration",
  "transport": "streamable_http",
  "url": "https://collab.example.com/stream",
  "headers": {
    "X-User-ID": "user123",
    "X-Workspace-ID": "workspace456"
  }
}
```

### 실시간 데이터 처리

```json
{
  "name": "data_processor",
  "transport": "streamable_http",
  "url": "https://processor.example.com/stream",
  "timeout": 120,
  "sse_read_timeout": 600
}
```

## 성능 고려 사항

### 시간 초과(Timeout) 구성

- **짧은 타임아웃**: 빠른 작업용 (30~60초)
- **중간 타임아웃**: 표준 작업용 (60~120초)
- **긴 타임아웃**: 복잡한 대용량 작업용 (120초 이상)

### 커넥션 풀링(Connection Pooling)

- 가능한 경우 연결을 재사용합니다
- 동시 연결 수 제한을 모니터링합니다
- 사용 완료 후 적절히 리소스를 해제합니다
- 연결 오류를 안정적으로 처리합니다

## 다음 단계

- [WebSocket 연결](./websocket.md) - 전이중 실시간 통신
- [SSE 연결](./sse.md) - 단방향 스트리밍
- [stdio 연결](./stdio.md) - 로컬 프로세스 통신
