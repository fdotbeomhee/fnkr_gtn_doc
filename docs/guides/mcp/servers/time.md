# Time MCP 서버

[**Time MCP Server**](https://github.com/modelcontextprotocol/servers/tree/main/src/time)는 AI 에이전트에게 날짜 및 시간 작업을 제공하여 시간 관련 데이터, 일정 관리 및 시간 기반 계산을 처리할 수 있도록 지원합니다. 날짜, 시간, 시간대(타임존) 또는 일정을 처리해야 하는 모든 워크플로에 적합합니다.

## 설치

1. **Griptape Nodes를 열고** **Settings** → **MCP Servers**로 이동합니다.

1. **+ New MCP Server를 클릭합니다.**

1. **서버를 구성합니다**:

    - **Server Name/ID**: `time`
    - **Connection Type**: `Local Process (stdio)`
    - **Configuration JSON**:

    ```json
    {
        "transport": "stdio",
        "command": "uvx",
        "args": ["mcp-server-time"],
        "env": {},
        "cwd": null,
        "encoding": "utf-8",
        "encoding_error_handler": "strict"
    }
    ```

1. **Create Server를 클릭합니다.**

## 사용 가능한 도구

- **`get_current_time`** - 현재 날짜 및 시간 조회
- **`parse_date`** - 날짜 문자열을 구조화된 형식으로 파싱
- **`format_date`** - 날짜를 다른 문자열 형식으로 서식 지정
- **`add_time`** - 날짜에 기간 추가
- **`subtract_time`** - 날짜에서 기간 빼기
- **`compare_dates`** - 두 날짜 비교
- **`get_timezone_info`** - 시간대에 대한 정보 조회
- **`convert_timezone`** - 시간대 간 시간 변환

## 참고 자료

- [Time MCP Server](https://github.com/modelcontextprotocol/servers/tree/main/src/time) - 공식 저장소 및 설명서
- [JavaScript Date Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) - 날짜 작업 레퍼런스
- [시간대 데이터베이스](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) - 전체 시간대 목록
