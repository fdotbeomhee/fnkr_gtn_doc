# Nuke Start Flow

**Nuke 대상 워크플로우의 진입점으로, 워크플로우를 Nuke 기즈모(gizmo)로 게시할 때 필수적입니다.**

카테고리: `Foundry Nuke`

## 개요 (TL;DR)

- `.gizmo`로 게시하려는 모든 워크플로우의 최상위 흐름 시작 부분에 이 노드를 배치합니다.
- 표준 [Start Flow](../../nodes/execution/start_flow.md) 노드와 유사하게 작동하며, Nuke 변형 노드는 기즈모 게시자(publisher)에게 해당 워크플로우가 Nuke 대상임을 표시합니다.
- 흐름의 반대쪽 끝에 [Nuke End Flow](nuke_end_flow.md)와 쌍으로 구성합니다.

## 일반적인 워크플로우 위치

```text
[Nuke Start Flow] → (워크플로우 노드들) → Nuke End Flow
```

## 입력 (Inputs)

없음.

## 출력 (Outputs)

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| `exec_out` | control | 흐름의 첫 번째 노드로 연결되는 제어 연결입니다. |

## 팁 및 주의사항

- **기즈모 게시에 필수입니다.** 게시자(publisher)는 최상위 흐름이 Nuke Start Flow로 시작하고 [Nuke End Flow](nuke_end_flow.md)로 끝나는지 유효성을 검사합니다. 일반 Start Flow 노드는 검증을 통과하지 못합니다.

## 관련 항목

- [Nuke End Flow](nuke_end_flow.md) — 짝을 이루는 종료 노드.
- [Nuke Script](nuke_script.md) — 흐름 내부에서 `.nk` 스크립트를 실행합니다.
