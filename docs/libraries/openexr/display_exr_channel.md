# Display EXR Channel

**1~4개의 개별 EXR 채널을 8비트 sRGB 또는 RGBA PNG로 결합하며, 배경 이미지 위에 알파 합성을 선택적으로 수행할 수 있습니다.**

카테고리: `EXR`

## 개요 (TL;DR)

- 각 슬롯(R, G, B, A)은 `EXRChannelArtifact`를 허용하며 선택 사항입니다 — 채널은 서로 다른 EXR 파일이나 서로 다른 파트에서 가져올 수 있습니다.
- 누락된 RGB 슬롯은 0으로 채워집니다. A 슬롯을 연결하면 선택적 배경 합성이 포함된 RGBA 출력이 생성됩니다.
- 최소 하나의 RGB 슬롯이 연결되어야 하며 연결된 모든 채널은 동일한 픽셀 해상도를 공유해야 합니다.
- [Display EXR Part](display_exr_part.md)와 동일한 색상 관리: `basic` 톤 매핑 또는 연결된 `OCIOColorParamsArtifact`를 통한 `ocio`.

## 일반적인 워크플로우 위치

```text
Load EXR (채널들) → [Display EXR Channel] → (이미지 출력)
```

## 노드 미리보기

<!-- TODO: add ../assets/display-exr-channel.png screenshot -->

## 입력 (Inputs)

| 이름 | 타입 | 필수 여부 | 설명 |
| --- | --- | --- | --- |
| `channel_r` | `EXRChannelArtifact` | 아니요 | 빨간색(Red) 평면에 매핑되는 채널입니다. |
| `channel_g` | `EXRChannelArtifact` | 아니요 | 초록색(Green) 평면에 매핑되는 채널입니다. |
| `channel_b` | `EXRChannelArtifact` | 아니요 | 파란색(Blue) 평면에 매핑되는 채널입니다. |
| `channel_a` | `EXRChannelArtifact` | 아니요 | 알파(Alpha)로 사용되는 채널입니다. 연결 시 출력이 RGBA가 됩니다. |
| `exposure` | `float` | 아니요 | 톤 매핑 또는 OCIO 변환 전에 적용되는 EV 스톱 단위의 노출입니다. 범위 -10 ~ +10, 기본값 `0.0`. |
| `color_params` | `OCIOColorParamsArtifact` | 아니요 | [OCIO Color Parameters](../opencolorio/ocio_color_parameters.md)에서 전달받습니다. `color_mode`가 `ocio`일 때 필수입니다. |
| `background` | `ImageArtifact \| ImageUrlArtifact \| str` | 아니요 | A-over-B 합성을 위한 배경 이미지입니다. 알파 채널이 연결되지 않은 경우 무시됩니다. |

## 출력 (Outputs)

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| `image` | `ImageUrlArtifact` | 캔버스 내 미리보기를 위한 8비트 sRGB 또는 RGBA PNG입니다. |
| `output_file` | `str` | 저장된 PNG 파일의 경로입니다. |

## 파라미터 (Parameters)

| 이름 | 타입 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `color_mode` | `basic \| ocio` | OpenColorIO 라이브러리가 설치된 경우 `ocio`, 그렇지 않으면 `basic` | `basic`은 로컬 톤 매핑을 사용하고, `ocio`는 연결된 `OCIOColorParamsArtifact`를 사용합니다. |
| `tone_mapping` | `filmic \| linear` | `filmic` | `color_mode`가 `basic`일 때의 로컬 톤 매핑 방식입니다. `filmic`은 Narkowicz 2015 커브를 적용하고, `linear`는 [0, 1] 범위로 클램핑합니다. `ocio` 모드에서는 숨겨집니다. |

## 팁 및 주의사항

- **최소 하나의 RGB 슬롯을 연결하세요.** 알파 채널만 연결된 경우 노드가 실패합니다.
- **연결된 모든 채널 간에 해상도가 일치해야 합니다.** 해상도가 다른 채널을 결합하면 실패하므로 업스트림에서 먼저 크기를 조정하세요.
- **AOV 검사에 이상적입니다.** 단일 AOV 채널(예: `depth` 또는 `normal.x`)을 `channel_r`에 연결하여 그레이스케일-레드로 시각화하거나 `normal.x/y/z`를 RGB로 조합할 수 있습니다.
- **배경 합성에는 알파가 필요합니다.** `channel_a`가 연결되어 있지 않으면 `background` 입력은 무시됩니다.

## 관련 항목

- [Load EXR](load_exr.md) — 채널 입력을 제공합니다.
- [Display EXR Part](display_exr_part.md) — 자동 채널 선택으로 전체 파트를 렌더링합니다.
- [Save EXR](save_exr.md) — 조합된 채널을 다시 EXR로 저장합니다.
