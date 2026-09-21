# OCIO Color Parameters

**소스 색공간, 디스플레이, 뷰를 다운스트림의 색상 관리 노드를 위한 단일 재사용 가능 `OCIOColorParamsArtifact`로 묶습니다.**

카테고리: `Colorspace`

## 개요 (TL;DR)

- [Load OCIO Config](load_ocio_config.md)의 `config`를 연결하면 해당 설정에 따라 세 개의 드롭다운 목록이 채워집니다.
- **Source Colorspace**는 역할 별칭(예: `scene_linear`, `compositing_log`)을 먼저 나열하여 안정적이고 파이프라인에 안전한 이름을 상단에 표시합니다.
- 출력은 `OCIOColorParamsArtifact`입니다 — 하나의 노드로 여러 다운스트림 대상을 구동하여 선택 항목을 한곳에서 관리할 수 있습니다.
- 선택 항목은 실행할 때마다 실시간 설정에 대해 다시 검증됩니다. 불일치가 발생하면 인라인 경고가 표시되지만 실행이 실패하지는 않습니다.

## 일반적인 워크플로우 위치

```text
Load OCIO Config → [OCIO Color Parameters] → (다운스트림 변환/표시 노드들)
```

## 노드 미리보기

<!-- TODO: add ../assets/ocio-color-parameters.png screenshot -->

## 입력 (Inputs)

| 이름 | 타입 | 필수 여부 | 설명 |
| --- | --- | --- | --- |
| `config` | `OCIOConfigArtifact` | 아니요 | [Load OCIO Config](load_ocio_config.md)에서 전달받습니다. 드롭다운 목록 채우기 및 유효성 검사를 활성화합니다. 이 입력이 없으면 드롭다운에 플레이스홀더가 표시되고 노드는 빈 문자열을 출력합니다. |
| `source_colorspace` | `str` (선택) | 아니요 | 연결된 설정의 소스 색공간 이름입니다. 역할이 먼저 나열되고 그 다음에 모든 색공간 이름이 나열됩니다. |
| `display` | `str` (선택) | 아니요 | 연결된 설정의 디스플레이 장치 이름입니다. |
| `view` | `str` (선택) | 아니요 | 선택한 디스플레이에 대한 뷰 이름입니다. `display`가 변경되면 선택 항목이 자동으로 업데이트됩니다. |

## 출력 (Outputs)

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| `color_params` | `OCIOColorParamsArtifact` | `source_colorspace`, `display`, `view`를 전달합니다. |
| `was_successful` | `bool` | 번들이 문제없이 정상 출력되었는지 여부입니다. |
| `result_details` | `str` | 유효성 검사 및 출력 세부 정보입니다 (접힌 상태 그룹). |

## 팁 및 주의사항

- **소스 색공간에는 가급적 역할(role) 이름을 사용하세요.** `scene_linear`와 같은 역할 이름은 설정이 변경되어도 유지되지만, 원시 색공간 이름은 다음 설정 버전에서 존재하지 않을 수 있습니다.
- **경고가 발생해도 실행이 중단되지 않습니다.** 선택한 값이 실시간 설정에 더 이상 존재하지 않는 경우(설정이 변경되었거나 INPUT을 통해 값이 연결된 경우), 노드는 아티팩트를 있는 그대로 출력하고 인라인 경고로 불일치를 알립니다 — 다운스트림 렌더 결과를 신뢰하기 전에 확인하세요.
- **하나의 노드를 여러 대상에서 재사용하세요.** 노드마다 드롭다운 선택을 중복하지 말고 모든 다운스트림 변환/표시 노드에 `color_params`를 연결하세요.
- **설정이 연결되지 않아도 워크플로우 스캐폴딩이 가능합니다.** 설정을 사용할 수 있기 전에 워크플로우 구조를 먼저 배치할 수 있습니다. 설정이 연결될 때까지 노드는 빈 문자열을 출력합니다.

## 관련 항목

- [Load OCIO Config](load_ocio_config.md) — `config` 입력을 제공합니다.
