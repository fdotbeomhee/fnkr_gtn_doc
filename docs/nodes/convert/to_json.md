# To JSON

json-repair를 사용하여 입력값을 JSON 데이터로 변환합니다.

## 설명 (Description)

To JSON 노드는 잘못 형성된(Malformed) JSON 문자열을 견고하게 처리하기 위해 `json-repair`를 사용하여 다양한 데이터 타입을 JSON 형식으로 변환합니다. 서로 다른 입력 타입에 대해 지능적인 변환을 제공하며 자동 복구 기능을 포함합니다.

## 파라미터 (Parameters)

### 입력 파라미터 (Input Parameters)

| 파라미터 (Parameter) | 타입 (Type) | 설명 (Description)          | 기본값 (Default) |
| -------------------- | ----------- | --------------------------- | ---------------- |
| `from`               | any         | JSON으로 변환할 데이터      | `{}`             |

### 출력 파라미터 (Output Parameters)

| 파라미터 (Parameter) | 타입 (Type) | 설명 (Description)         |
| -------------------- | ----------- | -------------------------- |
| `output`             | json        | JSON으로 변환된 데이터     |

## 주요 기능 (Features)

- **JSON 복구 연동**: 잘못 형성된 JSON 문자열을 처리하기 위해 `json_repair.repair_json()`을 사용합니다.
- **다양한 입력 타입 지원**: 서로 다른 입력 타입을 지능적으로 처리합니다.
- **오류 처리**: 복구 또는 파싱 실패 시 안정적인 대체(Fallback) 동작을 수행합니다.
- **견고한 변환**: 다양한 입력 형식을 처리하여 올바른 JSON 데이터로 변환할 수 있습니다.

## 예시 (Examples)

### 딕셔너리를 JSON으로 변환

```python
# 입력: {"name": "John", "age": 30, "active": True}
# 출력: {"name": "John", "age": 30, "active": true}
```

### 잘못된 JSON 문자열 복구

```python
# 입력: '{"name": "John", age: 30, "city": "New York"}'  # age 주변의 따옴표 누락
# 출력: {"name": "John", "age": 30, "city": "New York"}  # 복구된 JSON
```

### 일반 JSON 문자열

```python
# 입력: '{"user": {"name": "Alice", "active": true}}'
# 출력: {"user": {"name": "Alice", "active": true}}
```

### 리스트를 JSON으로 변환

```python
# 입력: [1, 2, 3, "four", {"nested": "value"}]
# 출력: [1, 2, 3, "four", {"nested": "value"}]
```

### 기타 데이터 타입

```python
# 입력: "simple string"
# 출력: "simple string"

# 입력: 42
# 출력: 42

# 입력: True
# 출력: true
```

## 입력 타입 처리 방식 (Input Type Handling)

### 딕셔너리 입력 (Dictionary Input)

- **동작**: 이미 딕셔너리인 경우 그대로 사용합니다.
- **예시**: `{"key": "value"}` → `{"key": "value"}`

### 문자열 입력 (String Input)

- **동작**: 잘못된 JSON 복구를 시도하고, 일반 JSON 파싱으로 대체(Fallback)합니다.
- **예시**: `'{"name": "John", age: 30}'` → `{"name": "John", "age": 30}`

### 기타 타입 (Other Types)

- **동작**: 먼저 문자열로 변환한 후 복구를 시도하며, 실패 시 빈 딕셔너리로 대체합니다.
- **예시**: `[1, 2, 3]` → `[1, 2, 3]`

## 활용 사례 (Use Cases)

- **데이터 표준화**: 다양한 데이터 형식을 JSON으로 통일
- **API 연동**: JSON 기반 API 호출을 위한 데이터 준비
- **데이터 정제**: 잘못된 형식의 JSON 데이터 복구 및 표준화
- **워크플로 통합**: 후속 처리를 위해 다양한 데이터 타입을 JSON 형식으로 변환
- **설정 데이터 처리**: 다양한 형식의 구성 설정 데이터 처리

## 관련 노드 (Related Nodes)

- [JSON Input](../json/json_input.md) - 입력값으로부터 JSON 데이터 생성
- [JSON Extract Value](../json/json_extract_value.md) - JSON에서 값 추출
- [JSON Replace](../json/json_replace.md) - JSON 내 값 치환
- [Display JSON](../json/display_json.md) - JSON 데이터 표시 및 서식 지정
