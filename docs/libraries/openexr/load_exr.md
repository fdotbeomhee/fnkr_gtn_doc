# Load EXR

**EXR 파일을 로드하고 전체 구조(파트, 채널, 헤더 속성)를 타입화된 출력으로 표시하며, 기본적으로 픽셀 데이터를 읽지 않습니다.**

카테고리: `EXR`

## 개요 (TL;DR)

- 기본적으로 헤더만 읽으므로 (`openexr.header_only` 설정) 대용량 멀티파트 렌더에서도 스캔 속도가 빠릅니다.
- 동적 **Parts** 및 **Channels** 그룹을 채웁니다: 파트당 하나의 `EXRPartArtifact` 및 원시 채널당 하나의 `EXRChannelArtifact`.
- 파트를 [Display EXR Part](display_exr_part.md)에 연결하고 채널을 [Display EXR Channel](display_exr_channel.md) 또는 [Save EXR](save_exr.md)에 연결하세요.
- **Open in external viewer** 버튼을 클릭하면 설정된 HDR 뷰어에서 파일이 열립니다 ([라이브러리 설정](index.md#라이브러리-설정-library-settings) 참조).

## 일반적인 워크플로우 위치

```text
[Load EXR] → Display EXR Part → (이미지 출력)
           ↘ Display EXR Channel / Save EXR
```

## 노드 미리보기

<!-- TODO: add ../assets/load-exr.png screenshot -->

## 입력 (Inputs)

| 이름 | 타입 | 필수 여부 | 설명 |
| --- | --- | --- | --- |
| `file_path` | `str` | 예 | `.exr` 파일의 경로입니다. |

## 출력 (Outputs)

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| `image_width` / `image_height` | `int` | 데이터 윈도우의 해상도 치수입니다. |
| `part_count` / `channel_count` | `int` | 파트 및 채널 개수입니다. |
| `compression` | `str` | 압축 형식 (예: `ZIP_COMPRESSION`, `DWAB_COMPRESSION`). |
| `storage_type` | `str` | `scanlineimage`, `tiledimage`, `deepscanline`, `deeptiled`. |
| `pixel_aspect_ratio` | `float` | 픽셀 가로세로 비율입니다. |
| `data_window` / `display_window` | `str` | `"xmin,ymin - xmax,ymax"`. |
| `time_code` | `str` | `HH:MM:SS:FF`, 없으면 빈 값. |
| `software` | `str` | 작성 프로그램 이름, 없으면 빈 값. |
| `owner` | `str` | 에셋 소유자, 없으면 빈 값. |
| `chromaticities` | `str` | `red_x/y`, `green_x/y`, `blue_x/y`, `white_x/y`를 포함하는 JSON. |
| `custom_attributes` | `str` | 모든 비표준 헤더 속성의 JSON입니다. |
| `parts` | `list[EXRPartArtifact]` | 파일 내 모든 파트에 대한 구조화된 디스크립터입니다. |

스캔 후 동적 그룹도 채워집니다:

- **Parts** — 파트당 하나의 `EXRPartArtifact` 출력이 생성되며 각각 자체 채널 출력을 포함합니다 (단일 파트 파일의 경우 숨겨지며, 대신 채널이 Channels 그룹에 직접 나타납니다).
- **Channels** — 이름, 픽셀 타입, 샘플링 정보를 포함하는 원시 채널당 하나의 `EXRChannelArtifact`입니다 (단일 파트 파일 전용).

## 팁 및 주의사항

- **헤더 전용 모드에서는 픽셀 타입이 근사치로 읽힐 수 있습니다.** 채널별 정확한 픽셀 타입이 필요한 경우 `openexr.header_only` 설정을 `false`로 설정하세요. 단, 스캔 시 픽셀 데이터가 메모리에 로드됩니다.
- **멀티파트 파일에서는 평면적인 Channels 그룹이 숨겨집니다.** 멀티파트 렌더의 경우 채널별 출력은 각 파트 그룹 내부에 위치합니다.
- **다운스트림 노드는 픽셀을 지연 로드(lazy load)합니다.** 디스플레이 및 저장 노드는 디스크립터 아티팩트에서 직접 픽셀 데이터를 읽으므로 헤더 전용 스캔을 사용하더라도 다운스트림에서 수행할 수 있는 작업에 제한이 없습니다.

## 관련 항목

- [Display EXR Part](display_exr_part.md) · [Display EXR Channel](display_exr_channel.md) — 파트/채널을 볼 수 있는 이미지로 렌더링합니다.
- [Save EXR](save_exr.md) — 채널을 다시 저장합니다.
