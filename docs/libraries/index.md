# 노드 라이브러리 (Node Libraries)

이 섹션에서는 Griptape Nodes를 확장하는 설치 가능한 노드 번들인 **노드 라이브러리(node libraries)**를 다룹니다. [표준 라이브러리(Standard Library)](../nodes/overview.md)는 기본적으로 설치되어 제공되며, 다른 모든 라이브러리는 자체 Git 리포지토리에 호스팅되어 에디터의 **Libraries** 패널 또는 `gtn` CLI를 통해 설치할 수 있습니다.

설치, 업데이트, 종속성 격리 및 공유/격리 모드(Shared/Isolated modes)의 작동 방식에 대한 자세한 내용은 [라이브러리 가이드](../guides/libraries.md)를 참조하세요.

## 문서화된 라이브러리 목록

| 라이브러리 | 노드 수 | 용도 | 요구 사항 |
| --- | --- | --- | --- |
| [Standard Library](../nodes/overview.md) | 284 | 기본 노드 — 에이전트, 텍스트, 이미지, 비디오, 오디오, 리스트, 딕셔너리, JSON, 실행 흐름 등 | 없음 (기본 설치됨) |
| [Advanced Media Library](advanced_media/index.md) | 28 | 로컬 미디어 생성 및 조작 — 디퓨전 파이프라인, 이미지/비디오 보조 처리, LoRA, 얼굴 감지 | GPU 권장; 모델 다운로드를 위한 Hugging Face 계정 |
| [OpenColorIO](opencolorio/index.md) | 2 | OCIO 설정을 사용한 전문가급 색상 관리 — 영화, VFX 및 애니메이션 파이프라인의 업계 표준 시스템 | OCIO 설정 파일 (`$OCIO` 환경 변수 또는 명시적 경로) |
| [OpenEXR](openexr/index.md) | 4 | VFX 및 HDR 이미지 워크플로우를 위한 OpenEXR 파일 로드, 검사, 표시 및 저장 | 없음 (선택적으로 OpenColorIO와 연동) |
| [Nuke](nuke/index.md) | 3 | 캔버스에서 헤드리스(headless)로 `.nk` 스크립트를 실행하고 워크플로우를 버전 관리되는 Nuke 기즈모(gizmo)로 게시 | 로컬 Foundry Nuke 설치 및 라이선스 |
| [Diffusers](diffusers/index.md) | 19 | 모듈형 🧨 Diffusers 파이프라인 — 개별적으로 연결 가능한 디퓨전 단계로 미디어 생성 워크플로우 구축 | GPU (CUDA 또는 MPS) |

이 목록 외에도 [Griptape Nodes 디렉터리](https://github.com/griptape-ai/griptape-nodes-directory)에서 더 많은 공식 및 커뮤니티 라이브러리를 찾아볼 수 있습니다. 또한 에디터의 **Add Library** 모달에서 **Browse Community Libraries** 버튼을 클릭하여 탐색할 수도 있습니다.

## 라이브러리 설치 방법

표준 라이브러리는 자동으로 등록되며, Advanced Media Library는 `gtn init` 실행 중에 설치 여부를 묻습니다. 이 페이지에 나열된 다른 모든 라이브러리는 동일한 방식으로 설치됩니다:

1. 에디터에서 **Manage → Library Management**를 엽니다.
1. **Add Library**를 클릭하고 라이브러리의 Git URL(각 라이브러리 개요 페이지에 기재됨)을 붙여넣습니다.
1. **Install**을 클릭합니다. 엔진이 리포지토리를 복제하고, 라이브러리의 Python 종속성을 격리된 가상 환경에 설치한 후 노드를 등록합니다.

또는 명령줄 인터페이스(CLI)에서 설치할 수도 있습니다:

```bash
gtn libraries download <git_url>
```

각 라이브러리의 종속성은 엔진 및 다른 모든 라이브러리와 격리되므로 여러 라이브러리를 안전하게 조합하여 설치할 수 있습니다. 자세한 내용은 [공존 보장(Coexistence guarantees)](../guides/libraries.md#공존-보장-coexistence-guarantees)을 참조하세요.

## 문서 구성 방식

각 라이브러리 문서는 다음과 같이 구성되어 있습니다:

- **개요 페이지** — 라이브러리의 기능, 설치 방법, 사전 요구 사항(환경 변수, 하드웨어, 계정) 및 설정.
- **노드별 참조 페이지** — 핵심 요약(TL;DR), 워크플로우에서의 일반적인 위치, 입력/출력/파라미터 표를 포함하는 일관된 형식.
