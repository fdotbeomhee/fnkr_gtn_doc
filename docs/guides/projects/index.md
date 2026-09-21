# 프로젝트 (Project)

Griptape Nodes에서 작업할 때 프로젝트 시스템은 파일이 어떻게 구성되고, 이름이 지정되며, 저장되는지를 제어합니다. 노드가 이미지를 저장하거나, 업로드된 파일을 복사하거나, URL에서 다운로드할 때마다 프로젝트 시스템이 해당 파일이 저장될 위치와 파일 이름을 결정합니다.

## 존재하는 이유

프로젝트 시스템이 없다면 모든 파일 저장 작업에 하드코딩된 경로가 필요합니다. 프로젝트 시스템은 하드코딩된 경로를 **매크로(macro)**라는 명명된 템플릿과 **상황(situation)**이라는 명명된 파일 저장 시나리오로 대체합니다. 이를 통해 전체 프로젝트의 파일 레이아웃을 한 곳에서 커스터마이즈할 수 있으며, 파일을 저장하는 모든 노드가 해당 레이아웃을 자동으로 따르게 됩니다.

## 구성 요소의 결합 방식

```
workspace/                         ← 작업 공간(workspace) 디렉터리 (루트)
  griptape-nodes-project.yml       ← 선택 사항: 사용자 정의 설정
  my_workflow/                     ← 워크플로 디렉터리
    inputs/                        ← 기본 inputs 디렉터리
    outputs/                       ← 기본 outputs 디렉터리
    temp/                          ← 기본 temp 디렉터리
    .griptape-nodes-previews/      ← 기본 previews 디렉터리
```

개념적 계층 구조는 다음과 같습니다:

```
workspace
  └── project template
        ├── situations    (각 시나리오에서 파일을 저장하는 위치와 방법)
        ├── directories   (논리적 이름 → 상대 경로 매핑)
        ├── variables     (프로젝트 소유 변수, 노드 및 매크로에서 사용 가능)
        └── environment   (매크로 구성을 위한 사용자 정의 키-값 쌍)
```

**작업 공간(Workspace)**은 이 프로젝트의 모든 작업에 대한 루트 디렉터리입니다. 설정에서 구성됩니다.

**프로젝트 템플릿(Project template)**은 이 프로젝트를 관리하는 구성 파일입니다. 시작 시 로드되며 — 먼저 시스템 기본값, 그 다음 `parent_project_path`를 통해 선언된 [상위 프로젝트](projects.md#상위-프로젝트-parent-projects), 마지막으로 이 프로젝트의 `griptape-nodes-project.yml`에 있는 오버라이드가 로드됩니다. 프로젝트 템플릿을 통해 파일이 저장되는 위치와 이름 지정 방식을 제어할 수 있습니다.

**상황(Situations)**은 파일을 읽거나 쓸 때의 명명된 시나리오입니다. 각 상황에는 파일 경로를 결정하는 매크로 템플릿과 파일이 이미 존재할 때 수행할 동작을 결정하는 정책(policy)이 있습니다.

**디렉터리(Directories)**는 논리적 이름과 경로 간의 매핑입니다. 각 디렉터리에는 매크로에서 변수로 사용할 수 있는 짧은 이름(예: `outputs`)이 있습니다. 이 이름은 디스크의 실제 경로에 매핑되며 — 기본적으로 `outputs`는 `outputs` 하위 폴더에 매핑됩니다. 디렉터리 경로는 상대 경로 또는 절대 경로일 수 있으며, 그 자체로 매크로 변수 또는 환경 변수 참조를 포함할 수 있습니다.

**변수(Variables)**는 노드 파라미터 및 매크로에서 `{VAR}` 치환에 참여하는 프로젝트 소유 명명된 값(문자열 또는 정수)입니다. 아티스트가 런타임에 변경할 수 있도록 쓰기 가능으로 표시할 수 있으며, 변경 사항은 프로젝트 파일에 다시 저장됩니다.

**환경(Environment)**은 매크로에서 참조할 수 있는 사용자 정의 키-값 쌍의 모음입니다.

**매크로(Macros)**는 구체적인 파일 경로를 생성하기 위해 상황 및 디렉터리에서 사용되는 템플릿 문자열입니다.

## 이 섹션의 문서

- [프로젝트 관리 (GUI)](gui_guide.md) — GUI에서 프로젝트 생성, 조회, 편집, 전환 및 제거
- [작업 공간 (Workspace)](workspace.md) — 루트 작업 컨텍스트 및 상대 경로 해석 방법
- [프로젝트 (Projects)](projects.md) — 프로젝트 파일 형식 및 병합 모델
- [버전 고정 (Version Pinning)](version_pinning.md) — 프로젝트를 엔진 버전 및 특정 라이브러리 버전에 고정
- [매크로 (Macros)](macros.md) — 파일 경로 생성을 위한 템플릿 구문 참조
- [시퀀스 (Sequences)](sequences.md) — 이미지 시퀀스 패턴 및 누락된 프레임 처리 방법
- [디렉터리 (Directories)](directories.md) — 논리적 이름-경로 매핑
- [상황 (Situations)](situations.md) — 정책이 포함된 명명된 파일 저장 시나리오
- [환경 및 내장 변수 (Environment & Builtin Variables)](environment.md) — 사용자 정의 변수 및 시스템 제공 값
- [프로젝트 변수 (Project Variables)](variables.md) — 타입, 권한 및 런타임 쓰기가 포함된 프로젝트 소유 변수
- [파일 확장자 디렉터리 (File Extension Directories)](file_extension_directories.md) — 확장자별 폴더로 파일 라우팅
- [사용자 정의 가이드 (Customization Guide)](customization.md) — 일반적인 사용자 정의를 위한 실용적인 예제\n