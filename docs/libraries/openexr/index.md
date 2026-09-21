# OpenEXR 라이브러리 (OpenEXR Library)

VFX 및 HDR 파이프라인을 위한 전문가급 [OpenEXR](https://openexr.com/) 지원 라이브러리입니다.
EXR 파일을 로드하여 구조를 검사하고, 개별 파트 또는 수동으로 조합한 채널을 톤 매핑된 이미지로 표시하며, 이미지 또는 채널 데이터를 다시 EXR로 저장할 수 있습니다.

- **리포지토리**: [griptape-ai/griptape-nodes-library-openexr](https://github.com/griptape-ai/griptape-nodes-library-openexr)
- **요구 사항**: 없음 (색상 관리 표시를 위해 [OpenColorIO 라이브러리](../opencolorio/index.md)와 선택적으로 연동)
- **노드 카테고리**: 노드 선택기 내 `EXR`

## 설치 방법

에디터에서 **Manage → Library Management**를 열고 **Add Library**를 클릭한 후 다음 URL을 붙여넣습니다:

```text
https://github.com/griptape-ai/griptape-nodes-library-openexr
```

또는 CLI를 통해 설치할 수 있습니다:

```bash
gtn libraries download https://github.com/griptape-ai/griptape-nodes-library-openexr
```

일반적인 설치, 업데이트 및 문제 해결 도움말은 [라이브러리 가이드](../../guides/libraries.md)를 참조하세요.

## 제공 노드

| 노드 | 설명 |
| --- | --- |
| [Load EXR](load_exr.md) | EXR 파일을 로드하고 전체 구조(파트, 채널, 헤더 속성)를 타입화된 출력으로 표시 — 기본적으로 헤더만 읽으므로 대용량 멀티파트 렌더에서도 빠름 |
| [Display EXR Part](display_exr_part.md) | EXR 파트를 노출 및 톤 매핑(또는 OCIO 색상 관리)을 적용하여 8비트 sRGB/RGBA PNG로 렌더링 |
| [Display EXR Channel](display_exr_channel.md) | 1~4개의 개별 EXR 채널을 8비트 표시 이미지로 결합하며, 배경 위에 선택적으로 알파 합성 수행 |
| [Save EXR](save_exr.md) | 8비트 이미지 또는 EXR 채널 아티팩트로부터 압축, 픽셀 타입, 헤더 메타데이터 제어 기능을 갖춘 단일 파트 EXR을 저장 |

## 빠른 시작 (Quick start)

```text
Load EXR → Display EXR Part → (이미지 출력)
         ↘ Display EXR Channel (어떤 파트나 파일에서든 R/G/B/A 조합)
```

1. **Load EXR** 노드를 추가하고 `file_path`에 `.exr` 파일을 지정합니다. 노드가 헤더를 스캔하여 동적 **Parts** 및 **Channels** 그룹을 채웁니다.
1. 파트 출력을 **Display EXR Part**에 연결하거나 (또는 개별 채널을 **Display EXR Channel**에 연결하여) 캔버스에서 미리 보거나 다른 이미지 노드로 전달할 수 있는 톤 매핑된 PNG를 생성합니다.
1. **Save EXR**을 사용하여 이미지 또는 채널 데이터를 다시 EXR로 출력합니다.

## OpenColorIO 연동

디스플레이 노드는 두 가지 색상 모드를 지원합니다:

- **`basic`** — 로컬 노출 + 톤 매핑 (`filmic` 또는 `linear`).
- **`ocio`** — 연결된 `OCIOColorParamsArtifact` ([OpenColorIO 라이브러리](../opencolorio/index.md)에서 제공)가 OCIO 디스플레이-뷰 변환을 구동합니다.

OpenColorIO 라이브러리가 설치되어 있으면 표시 노드가 자동으로 `ocio` 모드를 기본값으로 사용하며, 그렇지 않으면 `basic`으로 기본 설정됩니다. OCIO 종속성은 선택 사항이며, OCIO 없이도 모든 기능이 정상 작동합니다.

## 라이브러리 설정 (Library settings)

설정은 엔진 구성의 `openexr` 카테고리 아래에 있습니다.

| 설정 항목 | 기본값 | 설명 |
| --- | --- | --- |
| `openexr.header_only` | `true` | `true`인 경우 **Load EXR**은 파일 헤더만 읽습니다 (빠름). 픽셀 데이터를 메모리에 로드하는 대신 채널별 정확한 픽셀 타입을 읽으려면 `false`로 설정하세요. |
| `openexr.viewer_executable` | `""` | 외부 HDR 뷰어 실행 파일의 전체 경로 (예: `/usr/bin/djv` 또는 Nuke 바이너리). 비어 있으면 OS 기본 파일 연결 프로그램이 사용됩니다. |
| `openexr.viewer_args` | `""` | 파일 경로 앞에 뷰어로 전달되는 추가 명령줄 인수 (예: `--hdr --linear`). 쉘 스타일 따옴표 규칙으로 파싱됩니다. |

모든 노드에는 구성된 뷰어(또는 OS 기본값)에서 소스 EXR을 여는 **Open in external viewer** 버튼이 있습니다. 뷰어는 비동기(fire-and-forget) 방식으로 실행되므로 버튼을 클릭해도 캔버스가 멈추지 않습니다.

## 지원 및 문의

버그를 발견했거나 기능 요청이 있으신가요?
[이슈를 등록해 주세요](https://github.com/griptape-ai/griptape-nodes-library-openexr/issues).
