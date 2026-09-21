# 외부 MCP 클라이언트를 Griptape Nodes에 연결하기

Griptape Nodes는 외부 에이전트(Claude Desktop, Claude Code, Cursor, VS Code 등)가 엔진을 직접 제어할 수 있도록 자체 MCP 서버를 실행합니다. 이 페이지는 본 섹션의 다른 내용과 반대 방향입니다. Griptape Nodes가 외부 MCP 서버를 사용하는 대신, 여기서는 Griptape Nodes 자체를 MCP 서버로 외부에 노출합니다.

## URL

기본적으로 엔진은 다음 주소에서 수신 대기합니다:

```
http://localhost:8125/mcp/
```

전송 프로토콜은 **Streamable HTTP**입니다. 끝에 슬래시(`/`)를 붙이는 것을 권장하며, 슬래시를 생략하는 클라이언트를 위해 서버가 자동으로 `/mcp`를 `/mcp/`로 리디렉션합니다.

엔진이 시작되면 실제 바인딩된 주소가 다음과 같이 로그에 기록됩니다:

```
INFO MCP server listening at http://127.0.0.1:8125/mcp/
```

## 환경 변수 재정의 (Overrides)

호스트와 포트는 다음 환경 변수를 통해 제어할 수 있습니다:

| 변수 | 기본값 | 설명 |
| --- | --- | --- |
| `GTN_MCP_SERVER_HOST` | `localhost` | 바인딩할 인터페이스. 명시적으로 지정하려면 `127.0.0.1`을, LAN 연결을 허용하려면 `0.0.0.0`을 사용하세요. |
| `GTN_MCP_SERVER_PORT` | `8125` | TCP 포트. `0`으로 설정하면 OS가 사용 가능한 포트를 임의로 할당합니다. |
| `GTN_MCP_SERVER_LOG_LEVEL` | `ERROR` | MCP 서버의 uvicorn 로그 레벨입니다. |

구성된 포트가 이미 사용 중인 경우 엔진은 OS가 할당한 포트로 대체합니다. 실제 URL은 시작 로그를 확인하세요.

!!! warning "기본적으로 로컬 전용 (Local-only by default)"

    엔진은 `localhost`에 바인딩되므로 동일한 머신에 있는 프로세스만 접근할 수 있습니다. MCP 서버에는 별도의 인증 절차가 없습니다. 접근 가능한 모든 대상을 완전히 신뢰할 수 있는 경우가 아니라면 `0.0.0.0`에 바인딩하거나 네트워크에 포트를 노출하지 마세요.

## 클라이언트 구성

### Claude Code

`~/.claude.json`에 추가하거나 `claude mcp add` 명령을 사용하세요:

```json
{
  "mcpServers": {
    "griptape-nodes": {
      "type": "streamable-http",
      "url": "http://localhost:8125/mcp/"
    }
  }
}
```

### Cursor

전역 접근을 위해 `~/.cursor/mcp.json`을 생성하거나 작업 공간에 `.cursor/mcp.json`을 생성하세요:

```json
{
  "mcpServers": {
    "griptape-nodes": {
      "url": "http://localhost:8125/mcp/"
    }
  }
}
```

### VS Code

작업 공간에 `.vscode/mcp.json`을 생성하거나, **MCP: Open User Configuration**을 통해 사용자 구성 파일을 엽니다:

```json
{
  "servers": {
    "griptape-nodes": {
      "type": "http",
      "url": "http://localhost:8125/mcp/"
    }
  }
}
```

VS Code는 (`mcpServers` 대신) `servers`를 사용하고 `"type": "http"`를 지정해야 한다는 점에 유의하세요.

### Claude Desktop

Claude Desktop의 `claude_desktop_config.json`은 `stdio` 서버만 지원합니다. 원격/HTTP MCP 서버에 연결하려면 다음 방법 중 하나를 사용하세요:

- 앱 내에서 **Settings → Connectors → Add custom connector**를 사용하고 `http://localhost:8125/mcp/`를 붙여넣거나,
- 구성 파일에서 [`mcp-remote`](https://www.npmjs.com/package/mcp-remote)로 URL을 래핑합니다:

```json
{
  "mcpServers": {
    "griptape-nodes": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "http://localhost:8125/mcp/"]
    }
  }
}
```

## 연결 확인하기

가장 간단한 비대화형 확인 방법은 [MCP Inspector](https://github.com/modelcontextprotocol/inspector) CLI를 사용하는 것입니다. 엔진이 Streamable HTTP를 사용하므로 `--transport http` 플래그를 전달하세요:

```bash
npx -y @modelcontextprotocol/inspector --cli http://localhost:8125/mcp/   --transport http --method tools/list
```

`--transport http`를 지정하지 않으면 inspector가 기본적으로 SSE를 사용하며 `SSE error: Non-200 status code (400)` 에러가 발생합니다. 이는 엔진이 유효한 MCP 세션 없이 들어오는 SSE 스타일의 GET 요청을 거부하기 때문입니다.

Inspector는 브라우저 UI도 제공합니다:

```bash
npx -y @modelcontextprotocol/inspector
```

URL 필드에 `http://localhost:8125/mcp/`를 붙여넣고 전송 방식으로 **Streamable HTTP**를 선택합니다. 연결이 `TypeError: NetworkError when attempting to fetch resource` 오류로 실패하는 경우 대부분 CORS 문제입니다. 엔진의 MCP 서버는 현재 `Access-Control-Allow-Origin` 헤더를 내보내지 않으므로 브라우저의 교차 출처(cross-origin) 요청이 차단됩니다. 브라우저 대신 위의 CLI 명령을 사용하거나 브라우저 보안 설정을 완화한 상태로 inspector를 실행하세요.

## 워크플로우 구축 스킬 (Skill) 설치하기

엔진에는 에이전트에게 위에 설명된 MCP 도구 사용법(콜드 스타트 레시피, `EventRequestBatch`, 일반적인 주의점 등)을 안내하는 [`griptape-nodes-workflows` 스킬](https://docs.griptapenodes.com/en/stable/skills/griptape-nodes-workflows/SKILL/)이 포함되어 있습니다. Claude Code, Cursor, VS Code는 [agentskills.io](https://agentskills.io)의 `name` + `description` 프론트매터 규칙을 따르는 스킬을 기본적으로 로드하므로, 디렉터리에 파일을 배치하기만 하면 설치됩니다.

게시된 마크다운 주소:

```
https://docs.griptapenodes.com/en/stable/skills/griptape-nodes-workflows/SKILL/index.md
```

어떤 범위를 선택하든 디렉터리 이름은 **반드시** `griptape-nodes-workflows`여야 하고(프론트매터의 `name` 필드와 일치해야 함), 파일 이름은 **반드시** `SKILL.md`여야 합니다.

### 클라이언트별 설치 경로

| 클라이언트 | 프로젝트 범위 | 사용자 범위 |
| --- | --- | --- |
| Claude Code | `.claude/skills/griptape-nodes-workflows/SKILL.md` | `~/.claude/skills/griptape-nodes-workflows/SKILL.md` |
| Cursor | `.cursor/skills/griptape-nodes-workflows/SKILL.md` | `~/.cursor/skills/griptape-nodes-workflows/SKILL.md` |
| VS Code (Copilot) | `.github/skills/griptape-nodes-workflows/SKILL.md` | `~/.copilot/skills/griptape-nodes-workflows/SKILL.md` |

Cursor와 VS Code는 `.agents/skills/`(프로젝트) 및 `~/.agents/skills/`(사용자) 경로도 인식하며, VS Code는 추가로 `.claude/skills/` / `~/.claude/skills/`도 인식합니다. 여러 클라이언트가 하나의 폴더를 공유하도록 하려면 `~/.agents/skills/griptape-nodes-workflows/SKILL.md` 위치에 스킬을 배치하세요.

### 단일 명령어로 설치하기

위 표에 따라 `DEST` 경로를 조정한 후 실행하세요:

```bash
DEST="$HOME/.claude/skills/griptape-nodes-workflows"
mkdir -p "$DEST"   && curl -fsSL https://docs.griptapenodes.com/en/stable/skills/griptape-nodes-workflows/SKILL/index.md        -o "$DEST/SKILL.md"
```

채팅창에서 `/skills`를 입력(Claude Code 또는 VS Code)하거나 사용자 지정 메뉴의 Skills 탭을 열어(Cursor) 정상적으로 로드되었는지 확인하세요.
