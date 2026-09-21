# 스레드 (Threads)

각 대화는 하나의 **스레드(Thread)**입니다. 스레드는 생성된 날짜와 시간으로 자동 명명됩니다.

- **+ New**를 클릭하여 새 대화를 시작할 수 있습니다.
- 이전 스레드는 메시지 입력창 위에 나열됩니다.

<!-- TODO(#5095): screenshot of the thread header showing the timestamp name and "+ New" button -->

## 스레드 저장 위치

스레드는 로컬 파일 시스템에 저장되며 세션 간에 유지됩니다. 각 스레드는 세 개의 파일로 저장됩니다:

| 파일 | 내용 |
| ----------------------- | -------------------------------------------- |
| `thread_{id}.json` | 전체 메시지 기록 |
| `thread_{id}.meta.json` | 제목, 타임스탬프, 메시지 수 |
| `thread_{id}.runs.json` | 실행별 제공자, 모델 및 MCP 서버 정보 |

`runs` 파일은 스레드의 각 응답에 사용된 AI 제공자, 모델 및 MCP 서버를 추적합니다.

저장 위치는 [XDG Base Directory](https://specifications.freedesktop.org/basedir-spec/latest/) 규약을 따릅니다.

| 플랫폼 | 경로 |
| ------------- | ---------------------------------------------------- |
| macOS / Linux | `~/.local/share/griptape_nodes/threads/` |
| Windows | `%USERPROFILE%\.local\share\griptape_nodes\threads\` |

Griptape Nodes Desktop의 경우 자체 애플리케이션 데이터 폴더 내의 `xdg_data_home/griptape_nodes/threads/` 폴더에 스레드를 보관합니다. 해당 폴더의 위치는 **App Settings → App Data Locations**에서 확인할 수 있습니다.

기록 파일이 손상된 경우, Griptape Nodes는 다른 스레드에 영향을 주지 않도록 해당 파일을 자동으로 격리(`.<timestamp>-corrupt` 또는 `.corrupt-<timestamp>` 접미사로 이름 변경)합니다.
