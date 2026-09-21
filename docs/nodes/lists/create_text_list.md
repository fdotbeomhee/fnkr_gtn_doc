# Create Text List

Create Text List 노드를 사용하면 텍스트(문자열) 값들의 리스트를 생성할 수 있습니다.

## 입력 (Inputs)

- **Items** (string[]): 출력 리스트에 포함할 텍스트 값들의 리스트

## 출력 (Outputs)

- **Output** (list): 생성된 텍스트 값 리스트

## 예시

```python
# Items: ["Hello", "World", "!"]
# Output: ["Hello", "World", "!"]

# Items: ["First", "Second", "Third"]
# Output: ["First", "Second", "Third"]
```

## 참고 사항 (Notes)

- 입력 리스트의 모든 항목은 텍스트 값이어야 합니다.
- 출력 리스트에서 항목의 순서가 유지됩니다.
- 이 노드는 items 파라미터에 대해 입력 모드와 속성(Property) 모드를 모두 지원합니다.
