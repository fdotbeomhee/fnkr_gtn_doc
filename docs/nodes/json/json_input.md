# JSON Input

자동 복구 기능을 통해 입력 데이터로부터 JSON 노드를 생성합니다.

## 어떤 노드인가요?

JSON Input 노드는 다양한 입력 타입을 받아 올바른 형식의 JSON 데이터로 변환합니다. `json-repair`를 사용하여 손상되었거나 형식이 잘못된 JSON 문자열을 복구하며, 다양한 데이터 타입에 대해 견고한 변환 기능을 제공합니다.

## 파라미터 (Parameters)

### 입력 (Inputs)

| 파라미터 (Parameter) | 타입 (Type)     | 설명 (Description)                | 기본값 (Default) |
| -------------------- | --------------- | --------------------------------- | ---------------- |
| `json`               | json, str, dict | JSON으로 변환할 입력 데이터       | `{}`             |

### 출력 (Outputs)

| 파라미터 (Parameter) | 타입 (Type) | 설명 (Description)      |
| -------------------- | ----------- | ----------------------- |
| `json`               | json        | 처리된 JSON 데이터      |

## 주요 기능

- **자동 JSON 복구**: `json-repair`를 사용하여 형식이 잘못된 JSON 문자열 처리
- **다양한 입력 타입 지원**: 딕셔너리, 문자열 및 기타 데이터 타입 수용
- **오류 처리**: 파싱 실패 시 안정적인 폴백(Fallback) 동작
- **실시간 처리**: 입력이 변경되면 자동으로 업데이트

## 예시

### 기본 사용법

```python
# 입력: {"name": "John", "age": 30}
# 출력: {"name": "John", "age": 30}
```

### 잘못된 형식의 JSON 복구

```python
# 입력: '{"name": "John", age: 30}'  # age 주변의 따옴표 누락
# 출력: {"name": "John", "age": 30}  # 복구된 JSON
```

### 문자열을 JSON으로 변환

```python
# 입력: '{"user": {"name": "Alice", "active": true}}'
# 출력: {"user": {"name": "Alice", "active": true}}
```

### 딕셔너리 입력

```python
# 입력: {"status": "active", "count": 5}
# 출력: {"status": "active", "count": 5}  # 그대로 사용됨
```

## 사용 사례

- **데이터 유효성 검증**: 입력 데이터가 유효한 JSON인지 확인
- **API 연동**: 외부 API로부터 수신된 JSON 응답 처리
- **데이터 정제**: 손상되거나 형식이 맞지 않는 JSON 데이터 복구
- **워크플로 통합**: 다양한 데이터 타입을 JSON 형식으로 변환

## 관련 노드

- [JSON Extract Value](json_extract_value.md) - JSON에서 특정 값 추출
- [JSON Replace](json_replace.md) - JSON 내의 값 교체
- [Display JSON](display_json.md) - JSON 데이터 표시 및 서식 지정
- [To JSON](../convert/to_json.md) - 다른 데이터 타입을 JSON으로 변환
