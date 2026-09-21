# Split List

Split List 노드를 사용하면 인덱스 또는 항목 값 일치를 기준으로 리스트를 두 부분으로 분할할 수 있습니다.

## 입력 (Inputs)

- **Items** (list): 분할할 대상 리스트
- **Split Index** (int): 리스트를 분할할 인덱스 위치 (Split By가 "index"일 때 표시됨)
- **Split Item** (any): 리스트를 분할할 기준 항목 (Split By가 "item"일 때 표시됨)

## 속성 (Properties)

- **Split By** (str): 리스트 분할 방식
    - 옵션:
        - "index": 특정 인덱스 위치에서 분할
        - "item": 특정 항목 값을 기준으로 분할
- **Keep Split Item** (bool): 두 번째 리스트에 분할 기준 항목을 유지할지 여부 (Split By가 "item"일 때 표시됨)

## 출력 (Outputs)

- **Output A** (list): 분할된 리스트의 첫 번째 부분
- **Output B** (list): 분할된 리스트의 두 번째 부분

## 예시

```python
# 입력 리스트: [1, 2, 3, 4]

# 인덱스 기준으로 분할 (Split by index):
# Split Index: 2
# Output A: [1, 2]
# Output B: [3, 4]

# 항목 기준으로 분할 (Split by item):
# Split Item: 3
# Keep Split Item: True
# Output A: [1, 2]
# Output B: [3, 4]

# 항목 기준으로 분할 (Split by item):
# Split Item: 3
# Keep Split Item: False
# Output A: [1, 2]
# Output B: [4]
```

## 참고 사항 (Notes)

- 인덱스 기준으로 분할할 때 분할 인덱스 위치의 항목은 두 번째 리스트에 포함됩니다.
- 항목 기준으로 분할할 때 두 번째 리스트에 분할 기준 항목을 유지할지 여부를 선택할 수 있습니다.
- 항목 기준 분할 시 항목을 찾지 못하면 분할이 발생하지 않습니다.
- 인덱스 기준 분할 시 인덱스는 리스트의 유효 범위 내에 있어야 합니다.
- 원본 리스트는 수정되지 않으며, 새로운 리스트들이 반환됩니다.
