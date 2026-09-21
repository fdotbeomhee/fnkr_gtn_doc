# JSON Replace

점 표기법(Dot Notation) 경로를 사용하여 JSON 내의 값을 교체합니다.

## 어떤 노드인가요?

JSON Replace 노드를 사용하면 점 표기법을 사용하여 지정된 경로의 값을 교체함으로써 JSON 데이터를 수정할 수 있습니다. 원본 데이터 수정을 방지하기 위해 원본의 깊은 복사본(Deep Copy)을 생성하며, 경로가 존재하지 않는 경우 자동으로 누락된 경로를 생성할 수 있습니다.

## 파라미터 (Parameters)

### 입력 (Inputs)

| 파라미터 (Parameter) | 타입 (Type)     | 설명 (Description)                                                       | 기본값 (Default) |
| -------------------- | --------------- | ------------------------------------------------------------------------ | ---------------- |
| `json`               | json, str, dict | 수정할 대상 JSON 데이터                                                  | `{}`             |
| `path`               | str             | 교체할 점 표기법 경로 (예: 'user.name', 'items[0].title')                | `""`             |
| `replacement_value`  | json, str, dict | 지정된 경로에 새로 입력할 값                                             | `""`             |

### 출력 (Outputs)

| 파라미터 (Parameter) | 타입 (Type) | 설명 (Description)                           |
| -------------------- | ----------- | -------------------------------------------- |
| `output`             | json        | 교체된 값이 반영되어 수정된 JSON             |

## 주요 기능

- **깊은 복사 보호**: 원본 데이터 수정을 방지하기 위해 깊은 복사본 생성
- **경로 자동 생성**: 경로가 존재하지 않는 경우 누락된 경로를 자동으로 생성
- **배열 지원**: `items[0].name`과 같은 배열 인덱싱 처리 지원
- **실시간 업데이트**: 입력 파라미터가 변경되면 자동으로 업데이트
- **안전한 작업**: 유효하지 않은 경로 또는 데이터 타입에 대해 안정적인 오류 처리

## 경로 구문

### 기본 객체 교체

```python
# 중첩 객체 속성 교체
path = "user.name"  # 사용자의 이름 교체
path = "user.profile.email"  # 중첩된 이메일 교체
```

### 배열 요소 교체

```python
# 배열 요소 교체
path = "items[0]"  # 첫 번째 항목 교체
path = "items[0].title"  # 첫 번째 항목의 제목 교체
path = "users[2].name"  # 세 번째 사용자의 이름 교체
```

### 복합 경로 교체

```python
# 중첩 배열 내부 교체
path = "orders[0].items[1].price"  # 첫 번째 주문의 두 번째 항목 가격 교체
```

## 예시

### 기본 값 교체

```python
# 원본 JSON: {"user": {"name": "John", "age": 30}}
# Path: "user.name"
# 교체 값: "Jane"
# 출력: {"user": {"name": "Jane", "age": 30}}
```

### 배열 요소 교체

```python
# 원본 JSON: {"items": [{"title": "Book", "price": 25}, {"title": "Magazine", "price": 10}]}
# Path: "items[0].title"
# 교체 값: "Novel"
# 출력: {"items": [{"title": "Novel", "price": 25}, {"title": "Magazine", "price": 10}]}
```

### 새로운 경로 생성

```python
# 원본 JSON: {"user": {"name": "John"}}
# Path: "user.email"
# 교체 값: "john@example.com"
# 출력: {"user": {"name": "John", "email": "john@example.com"}}
```

### 중첩 배열 내부 교체

```python
# 원본 JSON: {"orders": [{"items": [{"name": "Product A"}, {"name": "Product B"}]}]}
# Path: "orders[0].items[1].name"
# 교체 값: "Product C"
# 출력: {"orders": [{"items": [{"name": "Product A"}, {"name": "Product C"}]}]}
```

### 배열 확장

```python
# 원본 JSON: {"items": [{"title": "Book"}]}
# Path: "items[2].title"
# 교체 값: "Magazine"
# 출력: {"items": [{"title": "Book"}, null, {"title": "Magazine"}]}
```

## 사용 사례

- **데이터 업데이트**: 구성 또는 사용자 데이터의 특정 필드 수정
- **API 연동**: 새 값으로 요청 페이로드(Payload) 업데이트
- **데이터 변환**: 다양한 시스템에 맞게 JSON 구조 수정
- **템플릿 처리**: 템플릿 JSON을 동적 값으로 채우기
- **구성 관리**: JSON 구성 파일의 설정값 업데이트

## 관련 노드

- [JSON Input](json_input.md) - 입력으로부터 JSON 데이터 생성
- [JSON Extract Value](json_extract_value.md) - JSON에서 특정 값 추출
- [Display JSON](display_json.md) - JSON 데이터 표시 및 서식 지정
- [To JSON](../convert/to_json.md) - 다른 데이터 타입을 JSON으로 변환
