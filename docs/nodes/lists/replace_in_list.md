# Replace In List

Replace In List 노드를 사용하면 항목 값 일치 또는 인덱스 위치를 기준으로 리스트 내의 항목을 대체할 수 있습니다.

## 입력 (Inputs)

- **Items** (list): 수정할 대상 리스트
- **Item To Replace** (any): 리스트에서 대체할 항목 (Replace By가 "item"일 때 표시됨)
- **Index To Replace** (int): 대체할 항목의 인덱스 (Replace By가 "index"일 때 표시됨)
- **New Item** (any): 새로 교체할 새 항목

## 속성 (Properties)

- **Replace By** (str): 대체할 항목을 식별하는 방식
    - 옵션:
        - "item": 일치하는 항목 값을 기준으로 대체
        - "index": 인덱스 위치를 기준으로 대체

## 출력 (Outputs)

- **Output** (list): 항목이 교체되어 수정된 리스트

## 예시

```python
# 입력 리스트: [1, 2, 3, 4]

# 항목 기준으로 대체 (Replace by item):
# Item To Replace: 3
# New Item: "three"
# Output: [1, 2, "three", 4]

# 인덱스 기준으로 대체 (Replace by index):
# Index To Replace: 1
# New Item: "two"
# Output: [1, "two", 3, 4]
```

## 참고 사항 (Notes)

- 항목 기준으로 대체할 때, 해당 항목이 처음 나타난 위치가 교체됩니다.
- 인덱스 기준으로 대체할 때, 인덱스는 리스트의 유효 범위 내에 있어야 합니다.
- 항목 기준 대체 시 항목을 찾지 못하면 변경 사항이 적용되지 않습니다.
- 원본 리스트는 수정되지 않으며, 새로운 리스트가 반환됩니다.
