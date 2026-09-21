# RulesetList

## 어떤 노드인가요?

RulesetList 노드는 여러 규칙 세트(Rulesets)를 하나의 목록으로 결합합니다. 여러 규칙 세트를 함께 그룹화하여 에이전트에 대해 보다 복잡한 동작을 정의할 수 있습니다.

## 언제 사용하나요?

다음과 같은 경우에 RulesetList 노드를 사용합니다:

- 보다 복잡한 에이전트 동작을 만들기 위해 여러 규칙 세트를 결합해야 할 때
- 서로 다른 규칙 세트들을 하나의 모음으로 정리하고자 할 때
- 동시에 여러 규칙 세트를 따라야 하는 에이전트를 구축할 때
- 다양한 규칙 시스템을 모듈식으로 결합하고자 할 때

## 사용 방법

### 기본 설정

1. 워크플로에 RulesetList 노드를 추가합니다.
1. 최대 4개의 개별 규칙 세트 노드를 입력 파라미터(ruleset_1, ruleset_2, ruleset_3, ruleset_4)에 연결합니다.
1. 출력(rulesets)을 규칙 세트 목록을 입력으로 받는 노드에 연결합니다.

### 파라미터 (Parameters)

- **ruleset_1**: 결합 목록에 추가할 첫 번째 규칙 세트
- **ruleset_2**: 결합 목록에 추가할 두 번째 규칙 세트
- **ruleset_3**: 결합 목록에 추가할 세 번째 규칙 세트
- **ruleset_4**: 결합 목록에 추가할 네 번째 규칙 세트

### 출력 (Outputs)

- **rulesets**: null이 아닌 모든 입력 규칙 세트를 포함하는 결합된 목록

## 예시

에이전트를 위해 여러 전문 규칙 세트를 결합하는 일반적인 사용 사례:

1. 워크플로에 Ruleset 노드를 추가하고 이름을 "Conversation Rules"로 지정합니다.
1. 워크플로에 또 다른 Ruleset 노드를 추가하고 이름을 "Task Rules"로 지정합니다.
1. 워크플로에 RulesetList 노드를 추가합니다.
1. "Conversation Rules" 규칙 세트를 RulesetList 노드의 ruleset_1에 연결합니다.
1. "Task Rules" 규칙 세트를 RulesetList 노드의 ruleset_2에 연결합니다.
1. 출력(rulesets)을 Agent 노드의 ruleset 입력에 연결합니다.
