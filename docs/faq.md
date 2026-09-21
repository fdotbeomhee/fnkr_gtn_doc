# 자주 묻는 질문 (FAQ)

!!! tip

    특정 문제나 오류를 해결하고 계신가요? 자주 발생하는 문제와 복구 방법은 [문제 해결](troubleshooting.md) 문서를 참조하세요.

## 내 워크스페이스는 어디에 있나요 (내 파일은 어디에 저장되나요)?

저장된 워크플로우 등의 파일은 워크스페이스 디렉토리(Workspace Directory)에 저장됩니다.

워크스페이스 디렉토리 경로는 Griptape Nodes Editor에서 확인할 수 있습니다:

1. Griptape Nodes Editor를 엽니다.
1. 기존 워크플로우를 열거나 빈 워크플로우를 새로 만듭니다.
1. "Settings"를 클릭합니다.
1. "Configuration Editor"를 선택합니다.
1. 맨 왼쪽 열에서 "Griptape Nodes Settings"를 클릭합니다 (아직 선택되지 않은 경우).
1. 해당 경로는 "Workspace Directory" 아래에 표시되어 있습니다.

Editor를 실행하고 있지 않은 경우, 다음 명령어를 실행하면 워크스페이스 디렉토리가 출력됩니다:

```bash
gtn config show | grep workspace
```

## Editor와 다른 머신에서 Engine을 실행할 수 있나요?

Engine과 Editor는 완전히 분리된 머신에서 실행할 수 있습니다. 사용자가 저장하는 모든 파일이나 등록하는 라이브러리는 Engine이 실행 중인 머신에 저장된다는 점에 유의하세요. 따라서 파일을 바로 찾을 수 없다면 Engine이 어느 머신에서 실행 중인지 다시 한 번 확인해 보세요.

## Griptape Nodes는 어디에 설치되어 있나요?

Griptape Nodes의 정확한 설치 위치를 확인하고 싶으신가요? 다음 명령어를 실행하면 정확한 설치 경로를 확인할 수 있습니다:

=== "macOS / Linux"

    ```bash
    dirname $(dirname $(readlink -f $(which griptape-nodes)))
    ```

=== "Windows (PowerShell)"

    ```powershell
    Split-Path (Split-Path (Get-Command griptape-nodes).Source)
    ```

## 설정(config) 파일을 보거나 편집할 수 있나요?

설정 파일의 경로를 확인하려면 Editor 상단 Settings 메뉴에서 **Copy Path to Settings**를 선택하세요. 그러면 설정 파일 경로가 클립보드에 복사됩니다.

명령줄에서 작업하는 것을 선호하신다면 다음 명령어를 사용할 수도 있습니다:

```
gtn config show
```

## 초기 설정 후 Advanced Media Library를 설치하려면 어떻게 해야 하나요?

라이브러리(Advanced-Media 외 라이브러리 포함)의 설치 및 관리에 대한 전반적인 내용은 [라이브러리](guides/libraries.md) 문서를 참조하세요.

초기 설정 과정에서 Advanced Media Library 설치를 건너뛰었으나 나중에 추가하고 싶은 경우, 다음 명령어를 실행하면 됩니다:

```bash
gtn init
```

이렇게 하면 설정 프로세스가 다시 시작됩니다. 기존 워크스페이스 및 Griptape Cloud API Key 설정을 유지하려면 Enter 키를 누르세요. 다음 안내가 표시되면:

```
Register Advanced Media Library? [y/n] (n):
```

Advanced Media Library를 설치하려면 **y**를, 설치를 건너뛰려면 **n**을 누르세요.

!!! note

    Advanced Media Library의 일부 노드는 정상적으로 작동하기 위해 특정 모델이 필요합니다. 이러한 모델은 별도로 설치해야 합니다.

    어떤 노드에 어떤 모델이 필요한지 확인하려면 각 노드의 문서를 참조하세요. 각 문서에 특정 요구사항에 대한 링크가 제공되어 있습니다.

## Advanced Media Library에서 지원 중단(deprecated)된 노드는 어떻게 되었나요?

버전 0.64.0에서는 Advanced Media Library에서 지원 중단되었던 노드들이 제거되었습니다. 이 노드들은 이전에 지원 중단 예정으로 표시되었으며, 더 유연한 대안 노드로 대체되었습니다.

지원 중단된 노드를 사용하는 워크플로우가 있는 경우 [MIGRATION.md](https://github.com/griptape-ai/griptape-nodes/blob/main/MIGRATION.md) 가이드를 참조하세요. 이 종합 가이드에는 다음 내용이 포함되어 있습니다:

- 제거된 노드 및 대체 노드의 전체 목록
- 단계별 마이그레이션 지침
- 대체 노드의 시각적 예시
- 새로운 Diffusion Pipeline Builder 시스템에 대한 세부 정보

마이그레이션 가이드에는 지원 중단된 모든 이미지 처리, diffusion 파이프라인, 업스케일링 및 LoRA 노드에 대한 대체 방안이 포함되어 있습니다.

## Griptape Nodes를 제거하려면 어떻게 해야 하나요?

[Griptape Nodes 제거](uninstalling.md) 문서를 참조하세요.

## Griptape Nodes를 업데이트하려면 어떻게 해야 하나요?

Griptape Nodes는 실행될 때마다 업데이트가 필요한지 자동으로 확인합니다. 업데이트가 필요한 경우 (y/n) 응답을 요청하는 메시지가 표시됩니다. y로 응답하면 자동으로 최신 버전의 Engine으로 업데이트됩니다.

*수동*으로 업데이트하려면 언제든지 다음 명령어 중 하나를 사용할 수 있습니다:

```bash
griptape-nodes self update
griptape-nodes libraries sync
```

또는

```bash
gtn self update
gtn libraries sync
```

## 피드백을 제공하거나 질문을 하려면 어디로 가야 하나요?

여러 채널을 통해 소통할 수 있습니다:

- [웹사이트](https://www.griptape.ai) - 일반적인 정보는 홈페이지를 방문하세요.
- [Discord](https://discord.com/invite/rpWWNmgGv9) - 질문과 토론을 위해 커뮤니티에 참여하세요.
- [GitHub](https://github.com/griptape-ai/griptape-nodes) - 이슈를 등록하거나 코드베이스에 기여해 보세요.

동일한 링크들이 모든 문서 페이지 하단 푸터(오른쪽 아래)의 세 가지 아이콘으로도 제공됩니다.

## 출시 전 기능(unreleased features)을 미리 테스트해보려면 어떻게 해야 하나요?

출시 전 기능을 테스트하는 데 관심이 있으시다면, Griptape Nodes의 프리릴리즈(pre-release) 빌드를 설치할 수 있습니다.

!!! warning

    프리릴리즈 빌드는 안정성이 보장되지 않으며 버그나 미완성 기능이 포함될 수 있습니다. 사용 시 주의하시기 바랍니다.

Griptape Nodes Desktop을 사용하는 경우, [앱 설정](guides/desktop/app_settings.md#release-channels)에서 **Release Channel**을 **Nightly**로 전환하세요. Nightly 빌드는 매일 배포되며 앱과 함께 프리릴리즈 엔진 및 에디터 빌드가 번들로 제공됩니다. 재설치할 필요 없이 언제든지 **Stable**로 다시 전환할 수 있습니다.

엔진을 직접 설치한 경우, 프리릴리즈 엔진은 [latest](https://github.com/griptape-ai/griptape-nodes/releases/tag/latest) 태그로 하루에 두 번 게시됩니다.

프리릴리즈 업데이트 채널로 전환하려면 다음 명령어를 실행하세요:

```
uv tool uninstall griptape-nodes
uv tool install git+https://github.com/griptape-ai/griptape-nodes.git@latest --reinstall --force --python 3.12
```

이렇게 하면 현재 버전의 Griptape Nodes가 제거되고 GitHub 리포지토리에서 최신 프리릴리즈 빌드가 설치됩니다.

!!! info

    `uv tool uninstall griptape-nodes`를 사용하여 제거해도 기존 프로젝트나 설정은 삭제되지 않습니다. Griptape Nodes 엔진 자체만 제거됩니다.

`gtn self version`을 실행하여 정상적으로 적용되었는지 확인할 수 있습니다. 버전 번호에 git 커밋 참조가 표시되어야 합니다:

```
gtn self version
v0.31.0 (git - e172e80)
```

안정(stable) 릴리즈 채널로 다시 전환하려면 다음 명령어를 실행하세요:

```
uv tool uninstall griptape-nodes
uv tool install griptape-nodes
```
