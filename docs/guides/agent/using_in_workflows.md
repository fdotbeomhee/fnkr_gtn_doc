# 워크플로우에서 사용하기 (Using in Workflows)

**Agent 노드**는 워크플로우에 AI 기능을 도입하는 방법입니다. 대화형 인터랙티브 환경인 채팅 사이드바와 달리, Agent 노드는 자동화된 시퀀스의 일부로 실행되며 다른 노드로부터 입력을 받아 다운스트림으로 출력을 전달합니다.

## 주요 파라미터

- **provider** — 사용할 AI 제공자 ([제공자 (Providers)](./providers/index.md)에서 구성한 제공자와 일치)
- **prompt model** — 해당 제공자의 특정 모델
- **rulesets** — Ruleset 노드 또는 TextInput을 연결하여 에이전트의 동작을 정의 (워크플로우에서는 스킬이 지원되지 않음)
- **tools** — 에이전트에 부여할 도구 및 기능
- **output_schema** — 선택 사항으로 출력을 특정 JSON 구조로 제한

## 전체 참조 문서

전체 파라미터 목록, 출력 스키마 예제 및 일반적인 문제에 대한 내용은 [Agent 노드 참조 문서](../../nodes/agents/create_agent.md)를 확인하세요.
