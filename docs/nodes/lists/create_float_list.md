# Create Float List

Create Float List 노드를 사용하면 부동 소수점(float) 값들의 리스트를 생성할 수 있습니다.

## 입력 (Inputs)

- **Items** (float[]): 출력 리스트에 포함할 부동 소수점 값들의 리스트

## 출력 (Outputs)

- **Output** (list): 생성된 부동 소수점 값 리스트

## 예시

```python
# Items: [1.5, 2.7, 3.14]
# Output: [1.5, 2.7, 3.14]

# Items: [-0.5, 0.0, 42.0]
# Output: [-0.5, 0.0, 42.0]
```

## 참고 사항 (Notes)

- 입력 리스트의 모든 항목은 부동 소수점 값이어야 합니다.
- 출력 리스트에서 항목의 순서가 유지됩니다.
- 이 노드는 items 파라미터에 대해 입력 모드와 속성(Property) 모드를 모두 지원합니다.
