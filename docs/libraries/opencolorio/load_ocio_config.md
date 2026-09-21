# Load OCIO Config

**`$OCIO` 환경 변수 또는 명시적 재정의 경로에서 OpenColorIO 설정을 로드하고 다운스트림 노드를 위해 `OCIOConfigArtifact`로 출력합니다.**

카테고리: `Colorspace`

## 개요 (TL;DR)

- 환경에 `$OCIO`가 설정되어 있으면 노드가 이를 자동으로 감지합니다 — 노드를 추가하고 실행하기만 하면 됩니다.
- `$OCIO`는 매 실행마다 실시간으로 다시 읽히므로 Griptape 프로젝트 수준의 환경 재정의(`project.yml`의 `environment:`)가 항상 적용됩니다.
- 출력은 `OCIOConfigArtifact`입니다 — [OCIO Color Parameters](ocio_color_parameters.md) 또는 `OCIO Config` 입력을 가진 모든 노드에 연결하세요.
- 축소된 **Advanced** 그룹을 사용하면 테스트 목적으로 `$OCIO`를 명시적인 `.ocio` 파일 경로로 재정의할 수 있습니다.

## 일반적인 워크플로우 위치

```text
[Load OCIO Config] → OCIO Color Parameters → (다운스트림 변환/표시 노드들)
```

## 노드 미리보기

<!-- TODO: add ../assets/load-ocio-config.png screenshot -->

## 입력 (Inputs)

| 이름 | 타입 | 필수 여부 | 설명 |
| --- | --- | --- | --- |
| `context_vars` | `dict` | 아니요 | OCIO 컨텍스트 변수 (예: `{"SHOT": "sh010", "SEQ": "sq020"}`). 출력 아티팩트에 전달됩니다. |
| `file_path` | `str` | 아니요 | `.ocio` 설정 파일의 경로입니다. **Override OCIO Config**가 활성화된 경우에만 사용됩니다. 파일 선택기를 제공합니다. |

## 출력 (Outputs)

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| `config` | `OCIOConfigArtifact` | 확인된 설정 경로와 컨텍스트 변수를 전달합니다. |
| `was_successful` | `bool` | 설정을 성공적으로 로드했는지 여부입니다. |
| `result_details` | `str` | 로드 결과 세부 정보입니다 (접힌 상태 그룹). |

## 파라미터 (Parameters)

### 고급 설정 (Advanced) *(기본적으로 접혀 있음)*

| 이름 | 타입 | 기본값 | 설명 |
| --- | --- | --- | --- |
| `override_ocio_config` | bool | `False` | 활성화하면 `$OCIO` 대신 아래의 명시적 `file_path`를 사용합니다. 환경을 변경하지 않고 특정 설정을 테스트할 때 유용합니다. |
| `file_path` | file path | `""` | 재정의 토글이 켜져 있을 때 표시됩니다. |

## 팁 및 주의사항

- **인라인 메시지를 확인하세요.** 노드는 감지된 `$OCIO` 경로 정보 메시지, `$OCIO`가 누락되었을 때의 경고, 지난 실행 이후 `$OCIO`가 변경되었을 때의 경고를 표시합니다 (다운스트림 출력을 새로고침하려면 노드를 다시 실행하세요).
- **재정의 모드는 `$OCIO`를 완전히 대체합니다.** **Override OCIO Config**가 활성화된 동안에는 환경 변수가 무시됩니다. 환경 변수 모드로 돌아가려면 토글을 끄세요.
- **`$OCIO`와 재정의가 모두 없으면 노드가 실패합니다.** 둘 중 하나를 설정하세요. 프로젝트별 설정은 `project.yml`의 `environment:`를 사용하는 것이 가장 깔끔합니다.

## 관련 항목

- [OCIO Color Parameters](ocio_color_parameters.md) — `config` 출력을 사용하는 가장 일반적인 노드입니다.
