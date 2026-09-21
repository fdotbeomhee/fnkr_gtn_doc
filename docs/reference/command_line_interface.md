# Griptape Nodes 명령줄 인터페이스 (CLI)

명령줄 인터페이스(CLI)가 처음이시라면, CLI는 UI에서 버튼을 클릭하는 대신 명령어를 입력하여 소프트웨어와 상호작용하는 텍스트 기반 방식입니다. Griptape Nodes는 `griptape-nodes`(또는 `gtn`) 명령어를 통해 터미널에서 Griptape Nodes와 상호작용할 수 있는 CLI를 제공합니다.

`griptape-nodes`(또는 줄임말 `gtn`)는 컴퓨터에 설치된 Griptape Nodes Engine을 실행하고 관리하도록 특별히 설계된 명령줄 도구입니다. 이 도구는 워크스페이스 초기화, 구성 설정 관리, 웹 기반 Griptape Nodes 에디터를 구동하는 엔진 시작 등의 작업을 처리합니다. 실제 워크플로우의 생성 및 편집은 엔진을 실행할 때 열리는 웹 인터페이스에서 수행됩니다.

## 기본 사용법

```
griptape-nodes [options] [COMMAND]
```

명령어를 지정하지 않으면 기본적으로 `engine` 명령어가 실행됩니다.

## 명령어 목록

### `engine` (기본 명령어)

Griptape Nodes 엔진을 실행합니다.

```
griptape-nodes engine
```

Griptape Nodes 엔진이 시작되고 https://nodes.griptape.ai 에서 웹 인터페이스가 열립니다.

### `init`

Griptape Nodes를 위한 새 워크스페이스를 초기화합니다: API 키, 워크스페이스 디렉토리, 스토리지 백엔드 및 (선택 사항) 추가 라이브러리를 설정합니다. 다시 실행하면 동일한 프롬프트가 다시 표시되어 나중에 이러한 설정을 변경할 수 있습니다.

```
griptape-nodes init [options]
```

옵션:

- `--api-key`: 프롬프트 없이 Griptape API 키를 직접 지정
- `--workspace-directory`: 프롬프트 없이 워크스페이스 디렉토리를 직접 지정
- `--storage-backend`: 프롬프트 없이 스토리지 백엔드(`local` 또는 `gtc`) 설정
- `--bucket-name`: `--storage-backend gtc` 설정 시 사용할 버킷 이름(기존 또는 신규)
- `--register-diffusers-library` / `--no-register-diffusers-library`: Griptape Nodes Diffusers Library 설치 (또는 건너뛰기)
- `--register-griptape-cloud-library` / `--no-register-griptape-cloud-library`: Griptape Cloud Library 설치 (또는 건너뛰기)
- `--no-interactive`: 프롬프트 없이 제공된 플래그 및 기본값만 사용하여 init 실행
- `--hf-token`: 접근 제한 모델을 다운로드하는 데 사용되는 Hugging Face 토큰 설정
- `--config key=value`: 임의의 설정 값 지정 (예: `--config log_level=DEBUG --config workspace_directory=/tmp`와 같이 반복 지정 가능)
- `--secret key=value`: 임의의 비밀 값 지정 (예: `--secret MY_API_KEY=abc123`과 같이 반복 지정 가능)

### `config`

Griptape Nodes 설정을 관리합니다.

```
griptape-nodes config SUBCOMMAND
```

하위 명령어:

- `show [config_path]`: 현재 설정을 표시합니다. 인수 없이 실행하면 병합된 전체 설정을 JSON으로 출력하고, 점으로 구분된 경로(예: `workspace_directory`)를 지정하면 해당 값만 출력합니다.
- `list`: 설정에 기여하는 모든 설정 파일을 우선순위 순서대로 나열합니다.
- `reset`: 설정을 기본값으로 초기화합니다.

이 방법으로 읽거나 설정할 수 있는 전체 설정 목록은 [설정 레퍼런스(Configuration Reference)](configuration_reference.md)를 참조하세요.

### `self`

CLI 설치 자체를 관리합니다.

```
griptape-nodes self SUBCOMMAND
```

하위 명령어:

- `uninstall`: CLI를 제거하며 설정 및 데이터 디렉토리와 설치된 실행 파일을 삭제합니다.
- `version`: CLI의 현재 버전을 표시합니다.
- `info`: 디버깅을 위한 시스템 정보 보고서를 출력합니다: 엔진 버전 및 설치 소스, 플랫폼 및 Python 세부 정보, 설정 경로, 각 계층의 파일 및 구문 분석 오류가 포함된 설정 레이어 스택, 병합된 전체 설정, 등록된 모든 라이브러리와 버전. `env` 및 `runtime` 레이어는 자체 파일이 없으므로 내용을 인라인으로 출력합니다(`env`는 설정된 `GTN_CONFIG_*` 변수를 나열하고 `runtime`은 활성 프로젝트에 고정된 워크스페이스 디렉토리를 표시). 버그 리포트에 첨부할 때 유용합니다.

### `libraries`

로컬 라이브러리를 관리합니다. 라이브러리 설치 및 관리에 대한 전체 가이드는 [라이브러리](../guides/libraries.md)를 참조하세요.

```
griptape-nodes libraries SUBCOMMAND
```

하위 명령어:

- `sync`: 등록된 모든 라이브러리를 최신 버전으로 업데이트합니다.
    - `--overwrite`: 라이브러리를 업데이트하기 전에 커밋되지 않은 로컬 변경 사항을 폐기
- `download <git_url>`: Git에서 라이브러리를 복제하고 등록합니다.
    - `--branch`: 체크아웃할 브랜치, 태그 또는 커밋
    - `--target-dir`: 복제할 디렉토리 이름
    - `--download-dir`: 라이브러리가 복제되는 상위 디렉토리
    - `--overwrite`: 디렉토리가 이미 존재하는 경우 덮어쓰기

### `models`

Hugging Face Hub에서 다운로드한 AI 모델을 관리합니다. 워크플로우를 실행할 때 로컬 디퓨전이나 LLM 노드가 가져오는 모델과 동일합니다.

```
griptape-nodes models SUBCOMMAND
```

하위 명령어:

- `download <model_id>`: Hugging Face Hub에서 모델을 다운로드합니다(예: `microsoft/DialoGPT-medium`).
    - `--local-dir`: 모델을 다운로드할 로컬 디렉토리
    - `--revision`: 다운로드할 Git 리비전(기본값 `main`)
- `list`: 현재 로컬 캐시에 있는 모든 모델 파일과 디스크 용량을 나열합니다.
- `delete <model_id>`: 로컬 캐시에서 모델 파일을 삭제합니다.
- `search [query]`: Hugging Face Hub에서 모델을 검색합니다.
    - `--task`: 작업 유형별로 결과 필터링(예: `text-generation`)
    - `--limit`: 반환할 최대 결과 수(기본값 20, 최대 100)
    - `--sort`: 결과 정렬 기준 필드(기본값 `downloads`)
    - `--direction`: 정렬 방향(기본값 `desc`)
- `downloads status [model_id]`: 한 모델의 다운로드 진행률/상태를 표시하거나 인수가 없으면 추적 중인 모든 모델의 상태를 표시합니다.
- `downloads list`: 추적 중인 모든 모델 다운로드와 상태를 나열합니다.
- `downloads delete <model_id>`: 모델의 다운로드 상태 추적 기록을 삭제합니다(모델 파일 자체는 삭제되지 않으며 파일 삭제는 `models delete` 사용).

### `doctor`

Griptape Nodes 설치 상태에 대한 헬스체크를 실행하고 통과/실패 표를 출력합니다(예: 엔진의 WebSocket 연결에 도달할 수 있는지 여부). 검사가 하나라도 실패하면 0이 아닌 상태 코드로 종료되므로 스크립트에서 안전하게 사용할 수 있습니다.

```
griptape-nodes doctor
```

## 구성 경로 (Configuration)

Griptape Nodes는 설정을 다음 위치에 저장합니다:

- 설정 디렉토리: `~/.config/griptape_nodes` (macOS/Linux) 또는 `%USERPROFILE%\.config\griptape_nodes` (Windows)
- 데이터 디렉토리: `~/.local/share/griptape_nodes` (macOS/Linux) 또는 `%USERPROFILE%\.local\share\griptape_nodes` (Windows)
- 설정 파일: 설정 디렉토리 내의 `griptape_nodes_config.json`
- 환경 파일: 설정 디렉토리 내의 `.env`

Griptape Nodes Desktop은 수동 설치된 엔진과 충돌하지 않도록 이 두 디렉토리를 자체 애플리케이션 데이터 폴더 내에 보관합니다. 해당 경로는 [Griptape Nodes 제거](../uninstalling.md#griptape-nodes-desktop)를 참조하세요.

## 기본 워크플로우

일반적인 사용 절차:

1. `griptape-nodes init`을 실행하여 워크스페이스 및 API 키를 설정합니다.
1. `griptape-nodes`를 실행하여 엔진을 시작합니다.
1. 웹 인터페이스를 사용하여 워크플로우를 생성하고 관리합니다.
