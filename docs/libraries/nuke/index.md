# Nuke 라이브러리 (Nuke Library)

[Foundry Nuke](https://www.foundry.com/products/nuke-family) 내부에서 실행되는 워크플로우를 구축합니다.
이 라이브러리는 Nuke를 대상으로 하는 워크플로우 작성을 위한 흐름 제어 노드, 워크플로우를 Nuke 스크립트에 배치할 수 있는 버전 관리된 `.gizmo`로 패키징하는 게시자(publisher), 그리고 기존 `.nk` 스크립트를 헤드리스(headless)로 실행하고 주석이 달린 노드를 Griptape 캔버스에 타입이 지정된 포트로 표시하는 **Nuke Script** 노드를 제공합니다.

- **리포지토리**: [griptape-ai/griptape-nodes-library-nuke](https://github.com/griptape-ai/griptape-nodes-library-nuke)
- **요구 사항**: 로컬 Foundry Nuke 설치 및 유효한 라이선스
- **노드 카테고리**: 노드 선택기 내 `Foundry Nuke`

## 설치 방법

에디터에서 **Manage → Library Management**를 열고 **Add Library**를 클릭한 후 다음 URL을 붙여넣습니다:

```text
https://github.com/griptape-ai/griptape-nodes-library-nuke
```

또는 CLI를 통해 설치할 수 있습니다:

```bash
gtn libraries download https://github.com/griptape-ai/griptape-nodes-library-nuke
```

일반적인 설치, 업데이트 및 문제 해결 도움말은 [라이브러리 가이드](../../guides/libraries.md)를 참조하세요.

## 제공 노드

| 노드 | 설명 |
| --- | --- |
| [Nuke Start Flow](nuke_start_flow.md) | Nuke 대상 워크플로우의 진입점 — 워크플로우를 기즈모(gizmo)로 게시할 때 필수 |
| [Nuke End Flow](nuke_end_flow.md) | Nuke 대상 워크플로우의 종료 노드; `was_successful` 및 `result_details`를 출력 |
| [Nuke Script](nuke_script.md) | `nuke -t`를 통해 헤드리스로 `.nk` 스크립트를 실행하고, 주석이 지정된 Read/Write 노드를 타입화된 입력/출력 포트로 표시 |

## Nuke 설치 환경 설정

### 자동 검색 (Auto-discovery)

라이브러리는 시작 시 표준 설치 위치를 검색하여 **Nuke Version** 드롭다운 목록을 자동으로 채웁니다:

| OS | 검색 위치 |
| --- | --- |
| macOS | `/Applications/Nuke*` |
| Windows | `%ProgramFiles%\Nuke*`, `%ProgramFiles(x86)%\Nuke*` |
| Linux | `/usr/local/Nuke*`, `/opt/Nuke*`, `~/Nuke*` |

또한 `PATH` 환경 변수에서 `Nuke` 또는 `nuke`라는 이름의 실행 파일을 확인합니다. 새 버전을 설치한 후 검색을 다시 실행하려면 노드에서 **Refresh UI**를 클릭하세요.

### 엔진 설정을 통한 수동 구성

표준 위치가 아닌 곳에 설치된 경우 Engine Settings의 `nuke.installations` 키에 항목을 추가합니다:

```json
{
  "nuke": {
    "installations": [
      {
        "display_name": "Nuke 16.0v7",
        "executable_path": "/opt/nuke/16.0v7/Nuke16.0",
        "annotator_nuke_version": 16
      }
    ]
  }
}
```

| 필드 | 필수 여부 | 설명 |
| --- | --- | --- |
| `display_name` | 예 | **Nuke Version** 드롭다운에 표시되는 레이블 |
| `executable_path` | 예 | Nuke 바이너리의 절대 경로 |
| `annotator_nuke_version` | 아니요 | Nuke 메이저 버전; 로드할 Annotator 패널 빌드를 제어 (기본값: `16`) |
| `env_overrides` | 아니요 | Nuke 서브프로세스에 병합되는 추가 환경 변수 |
| `notes` | 아니요 | 자유 텍스트 메모; 런타임에는 사용되지 않음 |

### 추가 엔진 설정

| 키 | 설명 |
| --- | --- |
| `nuke.executable` | 설치 항목이 선택되지 않았을 때 사용되는 대체(fallback) Nuke 바이너리 경로 |
| `nuke.env` | 모든 Nuke 서브프로세스에 주입되는 전역 환경 변수 |
| `nuke.nuke_path` | 모든 실행 시 `NUKE_PATH`에 추가되는 추가 디렉터리 목록 |

### Foundry 라이선스

Griptape Secrets 패널에서 `foundry_LICENSE`를 설정하세요. 이 값은 Nuke 서브프로세스에 자동으로 주입되므로 `env_overrides`에 하드코딩하지 마세요.

## 워크플로우를 Nuke 기즈모로 게시하기

1. 최상위 흐름이 [Nuke Start Flow](nuke_start_flow.md) 노드로 시작하고 [Nuke End Flow](nuke_end_flow.md) 노드로 끝나는 워크플로우를 작성합니다.
1. **Publish Workflow**를 실행합니다 ([워크플로우 게시](../../guides/publishing.md) 참조). 대화 상자에서 다음을 선택합니다:
    - 플러그인 경로 후보를 확인하기 위한 **Nuke install** (자동 감지됨).
    - **gizmo install path** — `~/.nuke`, `NUKE_PATH` 경로 중 하나, Nuke 설치 플러그인 디렉터리 또는 사용자 정의 경로.
    - 새 버전을 생성할지 현재 버전을 덮어쓸지 결정하는 **update mode**.
1. Nuke 내부에서 Nodes 툴바의 `Griptape` 메뉴를 사용하여 기즈모를 생성하거나, 게시 후 메인 메뉴 모음에서 `Griptape > Refresh Griptape Gizmos`를 실행하여 Nuke를 다시 시작하지 않고 새 버전을 불러옵니다.

게시하면 선택한 설치 디렉터리 아래에 버전이 지정된 `.gizmo`와 실행기 스크립트, 그리고 Nuke의 Nodes 툴바에 `Griptape` 하위 메뉴를 추가하는 `menu.py`가 생성됩니다. 동일한 워크플로우의 여러 게시된 버전은 워크플로우별 하위 메뉴 아래에 그룹화됩니다. 워크플로우 출력은 `.nk` 파일 옆(`griptape_outputs/<workflow_name>/...` 아래)에 저장됩니다.

## 지원 및 문의

버그를 발견했거나 기능 요청이 있으신가요?
[이슈를 등록해 주세요](https://github.com/griptape-ai/griptape-nodes-library-nuke/issues).
