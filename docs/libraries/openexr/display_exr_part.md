# Display EXR Part

**EXR 파트를 노출 및 톤 매핑 또는 OCIO 색상 관리를 적용하여 8비트 sRGB (또는 RGBA) PNG로 렌더링합니다.**

카테고리: `EXR`

## 개요 (TL;DR)

- [Load EXR](load_exr.md)에서 `EXRPartArtifact`를 전달받아 RGB 표시 채널을 자동 선택하고 캔버스에서 미리 볼 수 있는 `ImageUrlArtifact`를 출력합니다.
- 두 가지 색상 모드 지원: `basic` (로컬 노출 + 필름/선형 톤 매핑) 및 `ocio` (연결된 `OCIOColorParamsArtifact`를 통한 OCIO 디스플레이-뷰 변환).
- 파트에서 알파 채널(`A`)이 발견되면 알파가 자동으로 포함됩니다.
- 노드 실행 후 **Open in external viewer** 버튼을 클릭하면 설정된 HDR 뷰어에서 원본 EXR이 열립니다 ([라이브러리 설정](index.md#라이브러리-설정-library-settings) 참조).

## 일반적인 워크플로우 위치

```text
Load EXR → [Display EXR Part] → (이미지 출력)
```

## 노드 미리보기

<!-- TODO: add ../assets/display-exr-part.png screenshot -->

## 입력 (Inputs)

| 이름 | 타입 | 필수 여부 | 설명 |
| --- | --- | --- | --- |
| `part` | `EXRPartArtifact` | 예 | 렌더링할 EXR 파트입니다. 씬 리니어(scene-linear) HDR 데이터로 간주하며 색역 변환은 적용되지 않습니다. |
| `exposure` | `float` | 아니요 | 톤 매핑 또는 OCIO 변환 전에 적용되는 EV 스톱 단위의 노출입니다. 범위 -10 ~ +10, 기본값 `0.0`. |
| `color_params` | `OCIOColorParamsArtifact` | 아니요 | [OCIO Color Parameters](../opencolorio/ocio_color_parameters.md)에서 전달받습니다. `color_mode`가 `ocio`일 때 필수입니다. |

## 출력 (Outputs)

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| `image` | `ImageUrlArtifact` | 캔버스 내 표시를 위한 8비트 sRGB 또는 RGBA PNG입니다. |
| `output_file` | `str` | 저장된 PNG 파일의 경로입니다. |

## 파라미터 (Parameters)

| 이름 | 타입 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `color_mode` | `basic \| ocio` | OpenColorIO 라이브러리가 설치된 경우 `ocio`, 그렇지 않으면 `basic` | `basic`은 로컬 톤 매핑을 사용하고, `ocio`는 연결된 `OCIOColorParamsArtifact`를 사용합니다. |
| `tone_mapping` | `filmic \| linear` | `filmic` | `color_mode`가 `basic`일 때의 로컬 톤 매핑 방식입니다. `filmic`은 Narkowicz 2015 커브를 적용하고, `linear`는 [0, 1] 범위로 클램핑합니다. `ocio` 모드에서는 숨겨집니다. |

## 팁 및 주의사항

- **OCIO 모드에는 연결된 `color_params`가 필요합니다.** 이것이 없으면 노드가 명확한 오류와 함께 실패합니다 — OCIO 설정이 없다면 `color_mode`를 `basic`으로 전환하세요.
- **데이터는 씬 리니어(scene-linear)로 간주됩니다.** 입력 색역 변환이 적용되지 않으므로 EXR에 디스플레이 참조(display-referred) 또는 로그(log) 데이터가 저장되어 있는 경우 올바른 소스 색공간과 함께 OCIO 모드를 사용하세요.
- **OCIO 실패는 명확하게 보고됩니다.** 잘못된 설정이나 유효하지 않은 디스플레이/뷰가 지정되면 조용히 기본 톤 매핑으로 대체되는 대신 노드가 실패합니다.

## 관련 항목

- [Load EXR](load_exr.md) — `part` 입력을 제공합니다.
- [Display EXR Channel](display_exr_channel.md) — 개별 채널로부터 표시 이미지를 직접 조합합니다.
- [OCIO Color Parameters](../opencolorio/ocio_color_parameters.md) — `color_params` 입력을 제공합니다.
