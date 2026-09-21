# Griptape Nodes 제거 (Uninstalling)

설치 방식에 해당하는 섹션을 확인하세요:

- **[Griptape Nodes Desktop](#griptape-nodes-desktop)** — [griptapenodes.com](https://griptapenodes.com)에서 다운로드한 데스크톱 애플리케이션.
- **[수동 엔진 설치](#수동-엔진-설치)** — `uv tool install griptape-nodes` 명령어로 직접 설치한 엔진.

그 후 [남아 있는 파일 정리](#남아-있는-파일-정리) 섹션으로 마무리하세요.

!!! warning "워크플로우 파일은 자동으로 삭제되지 않습니다"

    어떤 제거 프로그램도 사용자의 워크스페이스 디렉토리를 건드리지 않으므로 프로젝트, 워크플로우 및 생성된 에셋은 사용자가 직접 삭제할 때까지 디스크에 유지됩니다. 보관하려는 항목은 [워크스페이스 삭제](#워크스페이스-삭제) 단계를 진행하기 *전*에 안전한 곳으로 복사해 두세요.

## Griptape Nodes Desktop

데스크톱 애플리케이션은 엔진을 자체 번들로 포함하며 자체 데이터 폴더 내에서 엔진 디렉토리를 관리합니다.

### macOS

1. Griptape Nodes를 종료합니다.
1. Finder에서 **응용 프로그램(Applications)**을 열고 **Griptape Nodes**를 휴지통으로 드래그합니다.
1. 휴지통을 비웁니다.

이 작업 후에도 데이터 폴더는 남아 있습니다. [애플리케이션 데이터 삭제](#애플리케이션-데이터-삭제-macos-및-linux)를 참조하세요.

### Windows

1. Griptape Nodes를 종료합니다.
1. **설정 → 앱 → 설치된 앱**으로 이동합니다.
1. **Griptape Nodes**를 찾아 **...** 메뉴를 열고 **제거**를 선택합니다.

Windows 제거 프로그램은 다음 항목을 삭제합니다:

- `%LOCALAPPDATA%\ai.griptape.nodes.desktop`의 애플리케이션(함께 보관된 다운로드 업데이트 패키지 포함).
- 시작 메뉴 및 바탕화면 바로가기.
- **설치된 앱** 목록 항목.
- 애플리케이션의 두 데이터 폴더인 `%APPDATA%\Griptape Nodes` 및 `%LOCALAPPDATA%\Griptape Nodes`. 여기에는 앱 설정, 로그 및 API 키를 포함하여 엔진이 작성한 모든 내용이 포함됩니다.

이어서 [남아 있는 파일 정리](#남아-있는-파일-정리)를 진행하세요.

### Linux

Griptape Nodes Desktop은 특정 위치에 설치되지 않는 단일 파일 형태의 AppImage로 제공됩니다.

1. Griptape Nodes를 종료합니다.
1. 다운로드했던 `.AppImage` 파일을 삭제합니다.
1. AppImageLauncher와 같은 도구에 AppImage를 등록한 경우, `~/.local/share/applications` 아래의 실행 항목도 함께 제거되도록 해당 도구를 통해 제거하세요.

이어서 [애플리케이션 데이터 삭제](#애플리케이션-데이터-삭제-macos-및-linux)를 진행하세요.

### 애플리케이션 데이터 삭제 (macOS 및 Linux)

macOS 및 Linux에서는 애플리케이션을 삭제해도 데이터가 남아 있습니다. 다음 폴더도 함께 삭제하세요. Windows에서는 제거 프로그램이 이미 해당 폴더를 삭제했습니다.

| 항목 | macOS | Linux |
| --- | --- | --- |
| 애플리케이션 데이터 | `~/Library/Application Support/Griptape Nodes` | `~/.config/Griptape Nodes` |
| 로그 파일 | `~/Library/Logs/Griptape Nodes` | `~/.config/Griptape Nodes/logs` |
| 다운로드된 업데이트 패키지 | `~/Library/Caches/velopack/ai.griptape.nodes.desktop` | — |

애플리케이션 데이터 폴더에는 앱 설정, 로그인 세션, 활성화된 라이선스 키, 그리고 엔진이 작성한 파일(`griptape_nodes_config.json`, API 키 및 비밀 정보가 포함된 `.env` 파일, 에이전트 대화 스레드, 모델 다운로드 기록, 비디오 미리보기를 위해 엔진이 다운로드한 ffmpeg 바이너리)이 보관됩니다. 노드 라이브러리는 여기에 없으며 [별도로 처리](#워크스페이스-삭제)되는 워크스페이스에 위치합니다. Linux에서는 업데이트가 임시 디렉토리에 준비되므로 정리할 필요가 없습니다.

!!! tip "앱 내부에서 정확한 경로 확인하기"

    제거하기 전에 **App Settings**를 열고 [App Data Locations](guides/desktop/app_settings.md#app-data-locations)로 스크롤하세요. 현재 머신의 애플리케이션 데이터 폴더가 표시되며, Finder나 파일 관리자에서 열 수 있는 **Open** 버튼이 제공됩니다. 로그 파일 및 업데이트 패키지는 여기에 나열되지 않으므로 위의 표를 참조하세요.

이어서 [남아 있는 파일 정리](#남아-있는-파일-정리)를 진행하세요.

## 수동 엔진 설치

제거 명령어를 실행합니다:

```bash
gtn self uninstall
```

`When done, press Enter to exit.` 메시지가 출력되면 Enter 키를 눌러 실행 파일 제거를 완료합니다. 다음 항목이 삭제됩니다:

- 엔진의 **설정** 및 **데이터** 디렉토리(`~/.config/griptape_nodes` 및 `~/.local/share/griptape_nodes`), API 키 및 비밀 정보가 포함된 `.env` 파일 포함.
- `uv tool uninstall griptape-nodes` 실행을 통한 `griptape-nodes` 및 `gtn` 명령어.

**Caveats** 섹션이 출력되면 확인하세요. 워크스페이스 및 프로젝트 폴더 내부에 남아 있는 설정 파일과 함께 삭제할 수 없어 수동으로 제거해야 하는 항목이 나열됩니다.

!!! note "Windows에서의 해당 폴더 위치"

    엔진은 모든 플랫폼에서 동일한 레이아웃을 사용하므로 Windows에서 두 폴더는 `%USERPROFILE%\.config\griptape_nodes` 및 `%USERPROFILE%\.local\share\griptape_nodes`입니다. 둘 다 `AppData`가 아닌 사용자 홈 폴더 아래에 위치합니다.

이전 제거 작업으로 인해 가상 환경이 손상되어 `gtn`이 더 이상 실행되지 않는 경우 동일한 작업을 수동으로 수행할 수 있습니다:

```bash
uv tool uninstall griptape-nodes
rm -rf ~/.config/griptape_nodes ~/.local/share/griptape_nodes
```

Windows PowerShell의 경우:

```powershell
uv tool uninstall griptape-nodes
Remove-Item -Recurse -Force "$env:USERPROFILE\.config\griptape_nodes"
Remove-Item -Recurse -Force "$env:USERPROFILE\.local\share\griptape_nodes"
```

이어서 [남아 있는 파일 정리](#남아-있는-파일-정리)를 진행하세요.

## 남아 있는 파일 정리

### 워크스페이스 삭제

[워크스페이스 디렉토리](guides/projects/workspace.md)는 프로젝트, 워크플로우, 생성된 에셋 및 노드 라이브러리를 보관하며 자동으로 삭제되지 않습니다. 기본 경로는 다음과 같습니다:

- 데스크톱 애플리케이션을 사용한 경우: `Documents/GriptapeNodes`
- 엔진을 직접 설치한 경우: `gtn`을 처음 실행한 디렉토리 내부의 `GriptapeNodes` 폴더

다른 위치를 지정했을 수도 있으므로 제거 전에 경로를 확인하세요.

유지하려는 항목을 복사한 후 해당 폴더를 삭제합니다.

### 다운로드된 모델 삭제

노드가 Hugging Face Hub에서 다운로드한 모델은 Griptape Nodes 디렉토리가 아닌 공유 Hugging Face 캐시에 저장됩니다.

| 플랫폼 | 경로 |
| --- | --- |
| macOS / Linux | `~/.cache/huggingface/hub` |
| Windows | `%USERPROFILE%\.cache\huggingface\hub` |

머신의 다른 프로그램이 Hugging Face를 사용하는 경우 전체 폴더를 삭제하지 마세요. 수동 엔진 설치 환경에서는 제거 전에 Griptape Nodes 모델만 정리할 수 있습니다:

```bash
gtn models list
gtn models delete <model_id>
```

Griptape Nodes가 설치된 상태에서는 에디터의 **Model Management** 창에서도 이 작업을 수행할 수 있습니다. [모델 및 라이브러리 관리](guides/editor/managing_models_and_libraries.md)를 참조하세요.

### uv 제거

Griptape Nodes는 노드 라이브러리용 가상 환경을 구축하기 위해 [uv](https://docs.astral.sh/uv/)를 사용하며, uv는 자체 캐시와 Python 인터프리터를 유지합니다. Griptape Nodes 설치만을 위해 uv를 설치한 경우 uv의 [제거 지침](https://docs.astral.sh/uv/getting-started/installation/#uninstallation)에 따라 uv 및 관련 파일을 제거하세요.

해당 지침은 `PATH` 설정을 유지합니다. uv 설치 프로그램이 실행 파일 디렉토리(macOS/Linux: `~/.local/bin`, Windows: `%USERPROFILE%\.local\bin`)를 PATH에 추가했으므로 uv가 제거된 후 해당 항목을 정리할 수 있습니다. macOS 및 Linux에서는 셸 프로필에 추가된 라인을 삭제하고, Windows에서는 **설정 → 시스템 → 정보 → 고급 시스템 설정 → 환경 변수**에서 제거합니다.

## 재설치

다시 설치하려면 [Griptape Nodes 설치](installation.md) 과정을 따르세요. 워크스페이스를 보관해 두었다면 새 설치 환경에서 해당 경로를 지정하여 기존 프로젝트와 워크플로우를 그대로 이어서 사용할 수 있습니다.
