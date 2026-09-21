# OpenColorIO 라이브러리 (OpenColorIO Library)

영화, VFX 및 애니메이션 파이프라인 전반에서 사용되는 업계 표준 색상 관리 시스템인 [OpenColorIO](https://opencolorio.org/) (OCIO) 기반의 전문가급 색상 관리 노드 모음입니다.

- **리포지토리**: [griptape-ai/griptape-nodes-library-opencolorio](https://github.com/griptape-ai/griptape-nodes-library-opencolorio)
- **요구 사항**: OCIO 설정 파일 (`$OCIO` 환경 변수 또는 명시적 경로)
- **노드 카테고리**: 노드 선택기 내 `Colorspace`

## 설치 방법

에디터에서 **Manage → Library Management**를 열고 **Add Library**를 클릭한 후 다음 URL을 붙여넣습니다:

```text
https://github.com/griptape-ai/griptape-nodes-library-opencolorio
```

또는 CLI를 통해 설치할 수 있습니다:

```bash
gtn libraries download https://github.com/griptape-ai/griptape-nodes-library-opencolorio
```

일반적인 설치, 업데이트 및 문제 해결 도움말은 [라이브러리 가이드](../../guides/libraries.md)를 참조하세요.

## 사전 요구 사항: `$OCIO` 환경 변수

이 라이브러리는 색상 구성을 식별하는 기본 방법으로 `$OCIO` 환경 변수를 사용합니다. Griptape Nodes를 실행하기 전에 환경에 이 변수를 설정하세요:

```bash
export OCIO=/path/to/your/config.ocio
```

대부분의 스튜디오 워크스테이션 및 렌더 환경에는 이미 `$OCIO`가 설정되어 있습니다. Griptape [프로젝트](../../guides/projects/index.md)로 작업하는 경우 `project.yml`에서 프로젝트별로 설정할 수 있습니다:

```yaml
environment:
  OCIO: "{project_dir}/config/aces.ocio"
```

`$OCIO` 값은 노드가 실행될 때마다 실시간으로 다시 읽히므로, 워크플로우를 다시 로드하지 않고도 프로젝트를 전환할 때 새 설정이 자동으로 적용됩니다.

라이브러리는 `OCIO`를 알려진 환경 변수로 등록하므로 애플리케이션을 벗어나지 않고도 Griptape Nodes 설정 UI에서 확인하거나 설정할 수 있습니다.

## 제공 노드

| 노드 | 설명 |
| --- | --- |
| [Load OCIO Config](load_ocio_config.md) | `$OCIO` 또는 명시적 경로에서 OCIO 설정을 로드하고 다운스트림 노드를 위해 `OCIOConfigArtifact`를 출력 |
| [OCIO Color Parameters](ocio_color_parameters.md) | 소스 색공간, 디스플레이, 뷰를 재사용 가능한 단일 `OCIOColorParamsArtifact`로 묶으며 연결된 OCIO 설정에 따라 드롭다운이 채워짐 |

## 빠른 시작 (Quick start)

1. 캔버스에 **Load OCIO Config** 노드를 추가합니다.
    - `$OCIO`가 설정되어 있으면 노드가 자동으로 경로를 감지하고 표시합니다.
1. `config` 출력을 **OCIO Color Parameters** 노드에 연결하고 소스 색공간, 디스플레이, 뷰를 선택합니다.
1. `color_params` 출력을 `OCIOColorParamsArtifact`를 허용하는 모든 노드에 연결합니다 — 예를 들어 [OpenEXR 라이브러리](../openexr/index.md)의 [Display EXR Part](../openexr/display_exr_part.md) 및 [Display EXR Channel](../openexr/display_exr_channel.md) 노드가 있습니다.

```text
Load OCIO Config → OCIO Color Parameters → (다운스트림 변환/표시 노드들)
```

## 다른 라이브러리에서의 활용

선택 노드 외에도 이 라이브러리는 다른 라이브러리에서 호출할 수 있는 색공간 변환 서비스를 제공합니다. [OpenEXR 라이브러리](../openexr/index.md)는 이를 자동으로 감지합니다: OpenColorIO 라이브러리가 설치되어 있으면 EXR 표시 노드가 로컬 톤 매핑 대신 OCIO 관리 색상(`color_mode: ocio`)을 기본값으로 사용합니다.

## 지원 및 문의

버그를 발견했거나 기능 요청이 있으신가요?
[이슈를 등록해 주세요](https://github.com/griptape-ai/griptape-nodes-library-opencolorio/issues).
