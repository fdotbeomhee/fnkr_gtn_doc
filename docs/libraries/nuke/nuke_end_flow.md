# Nuke End Flow

**Nuke 대상 워크플로우의 종료 노드로, 흐름의 성공 여부를 보고합니다.**

카테고리: `Foundry Nuke`

## 개요 (TL;DR)

- `.gizmo`로 게시하려는 모든 워크플로우의 최상위 흐름 끝에 이 노드를 배치합니다.
- 게시된 기즈모가 Nuke 내부에서 실행 상태를 다시 보고할 수 있도록 `was_successful` 및 `result_details`를 제공합니다.
- 흐름의 반대쪽 끝에 [Nuke Start Flow](nuke_start_flow.md)와 쌍으로 구성합니다.

## 일반적인 워크플로우 위치

```text
Nuke Start Flow → (워크플로우 노드들) → [Nuke End Flow]
```

## 입력 (Inputs)

| 이름 | 타입 | 필수 여부 | 설명 |
| --- | --- | --- | --- |
| `exec_in` | control | 예 | 흐름의 마지막 노드로부터 전달되는 제어 연결입니다. |

## 출력 (Outputs)

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| `was_successful` | `bool` | 흐름이 성공적으로 완료되었는지 여부입니다. |
| `result_details` | `str` | 흐름 실행 결과에 대한 상세 정보입니다. |

## 팁 및 주의사항

- **기즈모 게시에 필수입니다.** 게시자(publisher)는 최상위 흐름이 [Nuke Start Flow](nuke_start_flow.md)로 시작하고 Nuke End Flow로 끝나는지 유효성을 검사합니다. 일반 End Flow 노드는 검증을 통과하지 못합니다.

## 관련 항목

- [Nuke Start Flow](nuke_start_flow.md) — 짝을 이루는 진입 노드.
- [Nuke Script](nuke_script.md) — 흐름 내부에서 `.nk` 스크립트를 실행합니다.
