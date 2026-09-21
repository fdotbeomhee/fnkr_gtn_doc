# Display JSON

자동 복구 및 서식 지정 기능을 통해 JSON 데이터를 표시합니다.

## 어떤 노드인가요?

Display JSON 노드는 JSON 데이터를 받아 화면에 보기 좋게 서식을 지정하여 표시합니다. `json-repair`를 사용하여 형식이 잘못된 JSON 문자열을 자동으로 복구하며, 워크플로에서 JSON 데이터를 확인하고 디버깅할 수 있도록 깔끔하고 읽기 쉬운 출력을 제공합니다.

## 파라미터 (Parameters)

### 입력 (Inputs)

| 파라미터 (Parameter) | 타입 (Type)     | 설명 (Description)       | 기본값 (Default) |
| -------------------- | --------------- | ------------------------ | ---------------- |
| `json`               | json, str, dict | 표시할 JSON 데이터       | `{}`             |

### 출력 (Outputs)

| 파라미터 (Parameter) | 타입 (Type) | 설명 (Description)      |
| -------------------- | ----------- | ----------------------- |
| `json`               | json        | 서식이 지정된 JSON 데이터 |

## 주요 기능

- **자동 JSON 복구**: `json-repair`를 사용하여 손상되었거나 형식이 잘못된 JSON 문자열을 처리
- **다양한 입력 타입 지원**: 딕셔너리, 문자열 및 기타 다양한 데이터 타입 수용
- **오류 처리**: 파싱 실패 시 안정적인 폴백(Fallback) 동작
- **표시 최적화**: 손쉬운 확인 및 디버깅을 위해 JSON 서식 지정
- **실시간 처리**: 입력이 변경되면 자동으로 화면 업데이트

## 예시

### 기본 JSON 표시

```python
# 입력: {"name": "John", "age": 30, "active": true}
# 출력: {"name": "John", "age": 30, "active": true}
```

### 잘못된 형식의 JSON 복구 및 표시

```python
# 입력: '{"name": "John", age: 30, "city": "New York"}'  # age 주변의 따옴표 누락
# 출력: {"name": "John", "age": 30, "city": "New York"}  # 복구 및 서식 지정 완료
```

### 복잡한 JSON 구조

```python
# 입력: {"user": {"name": "Alice", "profile": {"email": "alice@example.com", "preferences": {"theme": "dark"}}}}
# 출력: {"user": {"name": "Alice", "profile": {"email": "alice@example.com", "preferences": {"theme": "dark"}}}}
```

### 문자열을 JSON으로 변환 및 표시

```python
# 입력: '{"items": [{"title": "Book", "price": 25}, {"title": "Magazine", "price": 10}]}'
# 출력: {"items": [{"title": "Book", "price": 25}, {"title": "Magazine", "price": 10}]}
```

## 사용 사례

- **디버깅**: 워크플로 개발 중 JSON 데이터 확인 및 검사
- **데이터 유효성 검증**: JSON 구조 및 콘텐츠 검증
- **API 응답 검사**: 외부 API로부터 수신된 응답 검사
- **구성 검토**: JSON 구성 파일 검토
- **데이터 흐름 모니터링**: 워크플로를 통과하는 JSON 데이터 모니터링

## 관련 노드

- [JSON Input](json_input.md) - 입력으로부터 JSON 데이터 생성
- [JSON Extract Value](json_extract_value.md) - JSON에서 특정 값 추출
- [JSON Replace](json_replace.md) - JSON 내의 값 교체
- [To JSON](../convert/to_json.md) - 다른 데이터 타입을 JSON으로 변환
