# 로컬 프로세스 (stdio) 연결

**stdio** 연결 유형을 사용하면 Griptape Nodes가 표준 입출력 스트림(stdin/stdout)을 사용하여 로컬 프로세스로 실행 중인 MCP 서버와 통신할 수 있습니다.

## stdio를 사용하는 경우

- **로컬 애플리케이션**: 동일한 머신에서 MCP 서버를 실행할 때
- **명령줄 도구**: CLI 기반 MCP 서버와 상호작용할 때
- **개발 환경**: 테스트 및 개발 시나리오
- **간단한 설정**: 최소한의 구성 오버헤드를 원할 때

## stdio MCP 서버 예시

대표적인 예시는 다음과 같습니다. 이 외에도 설정할 수 있는 다양한 MCP 서버가 있지만, 시작하는 데 도움이 되는 몇 가지를 소개합니다.

- **[Fetch](../servers/fetch.md)** - 웹 콘텐츠 가져오기 및 처리
- **[Filesystem](../servers/filesystem.md)** - 파일 및 디렉터리 작업

## 구성

### 필수 필드

| 필드 | 타입 | 설명 | 예시 |
| --- | --- | --- | --- |
| `command` | string | MCP 서버를 시작하는 명령어 | `"npx"`, `"python"`, `"uvx"` |
| `args` | array | 명령어에 전달되는 인자 목록 | `["-y", "@modelcontextprotocol/server-memory"]` |

### 선택적 필드

| 필드 | 타입 | 설명 | 기본값 |
| --- | --- | --- | --- |
| `env` | object | 환경 변수 | `{}` |
| `cwd` | string | 작업 디렉터리 | 현재 디렉터리 |
| `encoding` | string | 텍스트 인코딩 | `"utf-8"` |
| `encoding_error_handler` | string | 오류 처리 전략 | `"strict"` |

## 예제 구성

### Memory 서버 (Node.js) {: #nodejs }

```json
{
  "name": "memory",
  "transport": "stdio",
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-memory"],
  "description": "대화를 위한 영구 메모리 스토리지"
}
```

### Filesystem 서버

```json
{
  "name": "filesystem",
  "transport": "stdio",
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-filesystem", "/allowed/path"],
  "env": {
    "NODE_ENV": "production"
  },
  "cwd": "/home/user/projects"
}
```

## 설정 단계

### 1. MCP 서버 설치

```bash
# Node.js 기반 서버인 경우
npm install -g @modelcontextprotocol/server-memory

# Python 기반 서버인 경우
pip install mcp-server-git
# 또는
uvx mcp-server-git
```

### 2. Griptape Nodes에서 구성

1. Griptape Nodes 설정을 엽니다.
1. MCP Server 구성 메뉴로 이동합니다.
1. stdio 전송 방식을 사용하는 새 서버를 추가합니다.
1. command와 args를 입력합니다.
1. 연결을 저장합니다.

### 3. 워크플로우에서 사용

1. 플로우에 MCPTask 노드를 추가합니다.
1. 구성된 stdio 서버를 선택합니다.
1. 프롬프트를 입력합니다.
1. 워크플로우를 실행합니다.

## 장점

- **낮은 지연 시간(Low Latency)**: 프로세스 간 직접 통신
- **간단한 설정**: 최소한의 구성만 필요
- **로컬 제어**: 서버 프로세스에 대한 완전한 제어 권한
- **효율적인 리소스 사용**: 네트워크 오버헤드가 없음

## 제한 사항

- **로컬 전용**: 원격 서버에 연결할 수 없음
- **프로세스 관리 필요**: 서버의 라이프사이클을 직접 관리해야 함
- **플랫폼 종속적**: OS에 따라 명령어 구문이 다를 수 있음
- **단일 연결**: 서버 인스턴스당 하나의 연결만 가능

## 문제 해결

### 서버가 시작되지 않음 {: #server-wont-start }

오류가 `[Errno 2] No such file or directory: 'npx'`(또는 `'uvx'`, `'python'`)이면 엔진이 PATH에서 해당 명령어를 찾을 수 없는 것입니다:

- **데스크톱 앱**: v0.27.0 이상으로 업데이트합니다. 이전 버전은 macOS와 Linux에서 로그인 셸의 PATH를 상속하지 않으므로, Homebrew나 nvm 등으로 설치한 명령어가 엔진에 보이지 않습니다.
- **로그인 셸의 PATH가 없는 상태에서 데스크톱 앱 외부로 엔진을 실행하는 경우**(터미널, 서비스, 컨테이너): 엔진을 시작하기 전에 도구를 PATH에 추가하거나, **command**를 절대 경로로 설정하고(`which npx`로 찾을 수 있습니다) `env`에 완전한 `PATH`를 설정합니다. `env`에서 `PATH`를 설정하면 상속된 PATH를 확장하는 것이 아니라 *대체*하므로, 이 명령어뿐 아니라 다른 도구에 필요한 모든 디렉터리를 포함해야 합니다.

명령어는 시작되지만 서버가 여전히 실패하는 경우 다음도 확인하세요:

- 파일 권한
- 모든 종속성이 설치되어 있는지
- 터미널에서 명령어를 수동으로 실행했을 때 작동하는지

### 연결 시간 초과 (Connection Timeout) {: #connection-timeout }

- 서버가 stdio에 응답하고 있는지 확인합니다
- 인코딩 설정을 확인합니다
- 서버 오류 메시지가 있는지 확인합니다
- 작업 디렉터리 권한을 확인합니다

### 권한 오류

- 적절한 파일 시스템 권한이 있는지 확인합니다
- 사용자에게 필요한 디렉터리 접근 권한이 있는지 확인합니다
- 환경 변수 접근 권한을 확인합니다

## 모범 사례

1. **절대 경로 사용**: 엔진의 PATH에 명령어의 디렉터리가 포함되지 않은 경우에만 필요합니다. 위의 [서버가 시작되지 않음](#server-wont-start)을 참조하세요.
1. **환경 변수 설정**: 구성 정보 및 보안 비밀(Secret) 관리에 활용하세요.
1. **우아한 오류 처리**: 적절한 오류 처리 메커니즘을 구현하세요.
1. **리소스 모니터링**: 메모리 누수나 과도한 CPU 사용량이 없는지 점검하세요.
1. **명령어 사전 검증**: 구성을 완료하기 전에 터미널에서 서버 명령어가 정상 작동하는지 확인하세요.

## 다음 단계

- [SSE 연결](./sse.md) - HTTP 기반 스트리밍
- [Streamable HTTP](./streamable_http.md) - 스트리밍을 지원하는 HTTP
- [WebSocket 연결](./websocket.md) - 전이중(Full-duplex) 실시간 통신
