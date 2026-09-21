# Save EXR

**8비트 이미지 또는 EXR 채널 아티팩트로부터 단일 파트 EXR 파일을 저장하며, 채널 모드에서는 부동소수점 정밀도를 보존합니다.**

카테고리: `EXR`

## 개요 (TL;DR)

- 두 가지 모드: **Mode A**는 `ImageArtifact`/`ImageUrlArtifact` (8비트)를 받아 [0, 255] 범위를 [0.0, 1.0]으로 정규화합니다. **Mode B**는 최대 4개의 `EXRChannelArtifact` 슬롯을 받아 부동소수점 정밀도를 유지합니다.
- Mode B가 우선권을 갖습니다: 채널 슬롯이 하나라도 연결되어 있으면 `image_in`은 무시됩니다.
- 출력에는 저장된 파트에 대한 `EXRPartArtifact` 디스크립터가 포함되어 있으므로 [Display EXR Part](display_exr_part.md)로 바로 연결할 수 있습니다.
- 축소된 **Metadata** 그룹을 통해 선택적 헤더 필드(소유자, 타임코드, 코멘트, 커스텀 속성 등)를 설정할 수 있습니다.

## 일반적인 워크플로우 위치

```text
(이미지 노드) → [Save EXR] → Display EXR Part / Load EXR
Load EXR (채널들) → [Save EXR]
```

## 노드 미리보기

<!-- TODO: add ../assets/save-exr.png screenshot -->

## 입력 (Inputs)

| 이름 | 타입 | 필수 여부 | 설명 |
| --- | --- | --- | --- |
| `image_in` | `ImageArtifact \| ImageUrlArtifact` | 아니요 | 8비트 이미지 소스입니다 (Mode A). 채널 슬롯이 하나라도 연결되면 무시됩니다. |
| `channel_r` | `EXRChannelArtifact` | 아니요 | R로 작성할 채널입니다 (Mode B). |
| `channel_g` | `EXRChannelArtifact` | 아니요 | G로 작성할 채널입니다 (Mode B). |
| `channel_b` | `EXRChannelArtifact` | 아니요 | B로 작성할 채널입니다 (Mode B). |
| `channel_a` | `EXRChannelArtifact` | 아니요 | A로 작성할 채널입니다 (Mode B). |

## 출력 (Outputs)

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| `output_file` | `str` | 저장된 `.exr` 파일의 경로입니다. |
| `output_part` | `EXRPartArtifact` | 저장된 파트의 디스크립터 — [Display EXR Part](display_exr_part.md) 또는 [Load EXR](load_exr.md) 사용 노드에 연결 가능합니다. |

## 파라미터 (Parameters)

| 이름 | 타입 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `compression` | `ZIP \| ZIPS \| PIZ \| DWAA \| NONE` | `ZIP` | 출력 파일에 적용되는 코덱입니다. |
| `pixel_type` | `HALF \| FLOAT` | `HALF` | `HALF`는 16비트 부동소수점(표준)이며, `FLOAT`는 32비트 전체 정밀도입니다. |

### 메타데이터 (Metadata) *(기본적으로 접혀 있음)*

| 이름 | 타입 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `part_name` | `str` | `""` | 이 EXR 파트의 이름입니다. |
| `pixel_aspect_ratio` | `float` | `1.0` | 픽셀 가로세로 비율입니다. |
| `owner` | `str` | `""` | 에셋 소유자입니다. |
| `comments` | `str` | `""` | 자유 텍스트 코멘트입니다. |
| `capture_date` | `str` | `""` | 캡처 날짜 (예: `2025-01-01T12:00:00`). |
| `software` | `str` | `""` | 작성 소프트웨어 이름입니다. |
| `time_code` | `str` | `""` | 편집용 타임코드 (`HH:MM:SS:FF`). |
| `custom_attributes` | `str` | `""` | JSON 객체 형식의 비표준 헤더 속성입니다. |

## 팁 및 주의사항

- **Mode B가 우선 적용됩니다.** `image_in`을 저장하려 했으나 채널 슬롯이 여전히 연결되어 있으면 이미지는 무시됩니다 — 채널 슬롯 연결을 해제하세요.
- **8비트 소스가 HDR로 변환되지는 않습니다.** Mode A는 디스플레이 참조 8비트 데이터를 [0.0, 1.0] 범위로 정규화할 뿐 하이라이트를 복원하지 않습니다. 실제 HDR 데이터에는 부동소수점 채널과 함께 Mode B를 사용하세요.
- **용량이 큰 뷰티 렌더에는 `DWAA`를 선택하세요.** 손실 압축이지만 파일 크기가 크게 줄어듭니다. 정확한 값이 중요한 데이터 패스(깊이, 노멀)에는 `ZIP`/`PIZ`를 유지하세요.
- **`FLOAT`는 `HALF`에 비해 파일 크기가 두 배가 됩니다.** 추가 정밀도가 실제로 필요한 경우(예: 깊이 또는 위치 패스)에만 32비트를 사용하세요.

## 관련 항목

- [Load EXR](load_exr.md) — 채널 입력을 제공하고 작성된 파일을 다시 읽습니다.
- [Display EXR Part](display_exr_part.md) — `output_part`를 통해 저장된 파트를 미리 봅니다.
