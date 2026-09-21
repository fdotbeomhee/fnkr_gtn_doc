# Nuke Script

**`nuke -t`를 통해 기존 `.nk` 스크립트를 헤드리스로 실행하고, 주석이 달린 Read/Write 노드를 Griptape 캔버스에 타입이 지정된 입력/출력 포트로 표시합니다.**

카테고리: `Foundry Nuke`

## 개요 (TL;DR)

- `script_path`에 `.nk` 파일을 지정하고 Nuke 내부의 Griptape Annotator 패널로 I/O 노드에 주석을 달면, 노드에 해당 주석과 일치하는 타입화된 포트가 생성됩니다.
- 입력으로 이미지, 비디오 또는 원시 파일 경로를 전달받을 수 있으며 출력은 이미지, 이미지 시퀀스, 비디오 또는 3D 지오메트리가 될 수 있습니다.
- **Open in Nuke**는 Annotator 패널이 로드된 상태로 Nuke GUI에서 스크립트를 실행합니다. **Refresh UI**는 주석을 다시 읽고 설치 검색을 재실행합니다.
- 주석은 `.nk` 파일 옆의 `<script>.gt.json` 사이드카 파일에 저장되며, 사이드카가 오래된 것으로 보이면 노드가 경고를 표시합니다.

## 일반적인 워크플로우 위치

```text
(이미지/비디오 소스) → [Nuke Script] → (이미지/비디오/3D 활용 노드)
```

## 노드 미리보기

<!-- TODO: add ../assets/nuke-script.png screenshot -->

## 빠른 시작 (Quick start)

1. 캔버스에 **Nuke Script** 노드를 추가합니다.
1. **Script Path**를 `.nk` 파일 경로로 설정합니다.
1. **Open in Nuke**를 클릭합니다 — Griptape Annotator 패널이 로드되고 현재 노브(knob) 재정의 값이 사전 적용된 상태로 Nuke가 시작됩니다.
1. Nuke에서 **Panels > Griptape Annotator**를 엽니다. **Annotate** 탭에서 Read 노드를 입력으로, Write 노드를 출력으로 지정하고, 필요에 따라 다른 노드의 노브를 노출한 뒤 **Save Annotations**를 클릭합니다.
1. Griptape으로 돌아와 **Refresh UI**를 클릭합니다(또는 스크립트 경로를 다시 선택). 주석과 일치하는 타입화된 입력/출력 포트가 노드에 생성됩니다.
1. 입력을 연결하고 **Frame Start** / **Frame End**를 설정한 후 노드를 실행합니다.

## 입력 (Inputs)

| 이름 | 타입 | 필수 여부 | 설명 |
| --- | --- | --- | --- |
| `script_path` | `str` | 예 | `.nk` 파일 경로입니다. |
| `<annotated Read nodes>` | `str \| ImageArtifact \| ImageUrlArtifact \| VideoUrlArtifact \| BlobArtifact` | 아니요 | 입력으로 주석이 달린 Read 노드마다 하나의 포트가 생성됩니다. URL 기반 아티팩트는 렌더링 전에 임시 파일로 다운로드됩니다. |

## 출력 (Outputs)

출력으로 주석이 지정된 Write 노드는 노드 실행 후 **Outputs** 그룹에 표시됩니다. 아티팩트 타입은 주석에 따라 결정됩니다:

| 주석 타입 | Griptape 출력 |
| --- | --- |
| `ImageArtifact` / `ImageUrlArtifact` | `ImageUrlArtifact` (단일 렌더링 프레임) |
| `VideoUrlArtifact` | `VideoUrlArtifact` (렌더링된 비디오 파일, 예: `.mp4`) |
| `ImageSequenceArtifact` | `ImageUrlArtifact`의 `ListArtifact` — 렌더링된 프레임당 하나 |
| `ThreeDUrlArtifact` / `GLTFUrlArtifact` | `ThreeDUrlArtifact` (`.obj` 또는 `.glb`) |

## 파라미터 (Parameters)

| 이름 | 타입 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `nuke_installation` | choice | 자동 감지 | 설치 검색을 통해 채워지는 **Nuke Version** 드롭다운입니다 ([라이브러리 개요](index.md#nuke-설치-환경-설정) 참조). |
| `frame_start` | `int` | 스크립트 기본값 | 렌더링할 시작 프레임입니다. |
| `frame_end` | `int` | 스크립트 기본값 | 렌더링할 마지막 프레임입니다. |
| `<exposed knobs>` | 다양함 | 스크립트 값 | Annotator 패널의 **Expose** 탭을 통해 노출된 노브들이 해당 노드 이름 아래에 그룹화되어 표시됩니다. 스크립트의 기존 값을 사용하려면 비워두고, 렌더링 전에 재정의하려면 값을 설정하세요. |

### 고급 설정 (Advanced) *(기본적으로 접혀 있음)*

| 이름 | 타입 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `nuke_executable` | `str` | `""` | Nuke 바이너리의 절대 경로입니다. 이 노드에 대해서만 Engine Settings 선택을 재정의합니다. |
| `baked_script_path` | `str` | `""` | 베이크된(baked) `.nk` 복사본의 저장 경로입니다. |
| `save_baked_copy` | button | — | 모든 현재 파라미터 값이 노브에 베이크된 스크립트 사본을 작성합니다 — 아카이빙 또는 수동 검사에 유용합니다. |

## 팁 및 주의사항

- **스크립트 편집 후 주석을 다시 저장하세요.** 주석 작성 후 `.nk`를 저장하면 `<script>.gt.json` 사이드카가 오래되었을 수 있다는 경고가 노드에 표시됩니다. Nuke에서 다시 열고 주석을 다시 저장하여 새로고침하세요.
- **포트는 주석 작성 후에만 나타납니다.** 새로 지정된 스크립트에는 I/O 포트가 없습니다. 먼저 Annotator 패널에서 주석을 설정한 후 **Refresh UI**를 클릭하세요.
- **실행 전에 라이선스를 설정하세요.** Griptape Secrets 패널에서 `foundry_LICENSE`를 구성하세요. 유효한 라이선스에 접근할 수 없으면 헤드리스 렌더링이 실패합니다.
- **`PATH`에 의존하지 말고 버전 드롭다운을 사용하세요.** 머신 간 렌더링 재현성을 보장하기 위해 `nuke_installation`(또는 노드별 `nuke_executable` 재정의)으로 정확한 Nuke 빌드를 고정하세요.

## 관련 항목

- [Nuke Start Flow](nuke_start_flow.md) · [Nuke End Flow](nuke_end_flow.md) — 기즈모 게시를 위해 흐름을 래핑합니다.
