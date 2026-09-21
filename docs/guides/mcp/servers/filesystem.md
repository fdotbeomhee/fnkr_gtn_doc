# Filesystem MCP 서버

**[Filesystem MCP Server](https://github.com/modelcontextprotocol/servers/blob/main/src/filesystem/README.md)**는 AI 에이전트가 로컬 머신에서 파일 및 디렉터리 작업을 수행할 수 있도록 지원합니다. 파일 관리, 콘텐츠 정리, 데이터 처리 작업을 위해 특정 디렉터리에 대한 안전하고 통제된 접근을 제공합니다.

## 설치

1. **Griptape Nodes를 열고** **Settings** → **MCP Servers**로 이동합니다.

1. **+ New MCP Server를 클릭합니다.**

1. **서버를 구성합니다**:

    - **Server Name/ID**: `filesystem`
    - **Connection Type**: `Local Process (stdio)`
    - **Configuration JSON**:

    ```json
    {
    "transport": "stdio",
    "command": "npx",
    "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/path/to/allowed/directory"],
    "env": {},
    "encoding": "utf-8",
    "encoding_error_handler": "strict"
    }
    ```

1. **Create Server를 클릭합니다.**

## 사용 가능한 도구

- **`read_file`** - 파일 내용 읽기
- **`write_file`** - 파일에 내용 쓰기
- **`list_directory`** - 디렉터리 내용 목록 조회
- **`create_directory`** - 새 디렉터리 생성
- **`search_files`** - 이름 또는 패턴으로 파일 검색
- **`move_file`** - 파일 이동 또는 이름 변경
- **`delete_file`** - 파일 삭제

## 구성 옵션

여러 디렉터리에 대한 접근을 허용하려면 별도의 인수로 추가하세요:

```json
{
  "transport": "stdio",
  "command": "npx",
  "args": [
    "-y",
    "@modelcontextprotocol/server-filesystem",
    "/Users/username/Desktop",
    "/Users/username/Downloads",
    "/Users/username/Documents"
  ],
  "env": {},
  "encoding": "utf-8",
  "encoding_error_handler": "strict"
}
```

## 참고 자료

- [Filesystem MCP Server](https://github.com/modelcontextprotocol/servers/blob/main/src/filesystem/README.md) - 공식 저장소 및 설명서
- [Node.js File System API](https://nodejs.org/api/fs.html) - 파일 작업 레퍼런스
