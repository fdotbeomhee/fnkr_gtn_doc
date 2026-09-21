# Combine Lists

Combine Lists 노드는 두 개의 리스트를 입력받아 하나의 평탄화된(flattened) 단일 리스트로 결합합니다.

## 입력 (Inputs)

- **List A** (list): 결합할 첫 번째 리스트
- **List B** (list): 결합할 두 번째 리스트

## 출력 (Outputs)

- **Output** (list): 두 입력 리스트의 모든 항목이 순서대로 포함된 결합된 리스트

## 예시

```python
# List A: [1, 2]
# List B: [3, 4]
# Output: [1, 2, 3, 4]

# List A: ["a", "b"]
# List B: ["c", "d"]
# Output: ["a", "b", "c", "d"]

# List A: [1, 2]
# List B: []
# Output: [1, 2]
```

## 참고 사항 (Notes)

- 출력 리스트에서 항목의 순서가 그대로 유지됩니다.
- 둘 중 하나라도 리스트가 아닌 경우 빈 리스트로 처리됩니다.
- 원본 리스트는 수정되지 않으며, 새로운 리스트가 반환됩니다.
- 이 연산은 리스트 연결(list concatenation, list_a + list_b)과 동일합니다.
