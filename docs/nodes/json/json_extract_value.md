# JSON Extract Value

[JMESPath](https://jmespath.org) 표현식을 사용하여 JSON에서 값을 추출합니다.

## 어떤 노드인가요?

JSON Extract Value 노드를 사용하면 JMESPath 표현식을 활용하여 JSON 데이터에서 특정 값을 추출할 수 있습니다. 중첩된 객체 접근, 배열 인덱싱, 강력한 와일드카드 연산을 지원하여 복잡한 JSON 구조에서 데이터를 쉽게 탐색하고 추출할 수 있습니다.

## 파라미터 (Parameters)

### 입력 (Inputs)

| 파라미터 (Parameter) | 타입 (Type)     | 설명 (Description)                                                                        | 기본값 (Default) |
| -------------------- | --------------- | ----------------------------------------------------------------------------------------- | ---------------- |
| `json`               | json, str, dict | 추출 대상 JSON 데이터                                                                     | `{}`             |
| `path`               | str             | 데이터를 추출할 JMESPath 표현식 (예: 'user.name', 'items[0].title', '[*].assignee')      | `""`             |

### 출력 (Outputs)

| 파라미터 (Parameter) | 타입 (Type) | 설명 (Description) |
| -------------------- | ----------- | ------------------ |
| `output`             | json        | 추출된 값          |

## 주요 기능

- **JMESPath 표현식**: 유연한 데이터 추출을 위해 강력한 JMESPath 구문 사용
- **점 표기법(Dot Notation) 경로**: 간단한 점 표기법으로 JSON 구조 탐색
- **배열 인덱싱**: `[index]` 구문을 사용하여 배열 요소에 접근
- **와일드카드 연산**: `[*]` 구문을 사용하여 배열에서 모든 값 추출
- **네이티브 Python 타입**: 원시 Python 값(문자열, 딕셔너리, 리스트 등)을 반환하므로 별도의 JSON 직렬화/역직렬화가 불필요
- **실시간 업데이트**: 입력이 변경되면 자동으로 업데이트
- **안전한 추출**: 경로가 존재하지 않는 경우 빈 딕셔너리 `{}` 반환

## JMESPath 구문

> **📚 더 알아보기**: 전체 JMESPath 구문 및 고급 기능에 대해서는 [JMESPath 설명서](https://jmespath.org/tutorial.html)를 참조하세요.

### 기본 객체 접근

```python
# 중첩 객체 속성 접근
path = "user.name"  # 사용자의 이름 추출
path = "user.profile.email"  # 중첩된 이메일 추출
```

### 배열 인덱싱

```python
# 배열 요소 접근
path = "items[0]"  # 첫 번째 항목 추출
path = "items[0].title"  # 첫 번째 항목의 제목 추출
path = "users[2].name"  # 세 번째 사용자의 이름 추출
```

### 와일드카드 연산

```python
# 배열에서 모든 값 추출
path = "items[*].title"  # items 배열의 모든 제목 추출
path = "users[*].name"  # users 배열의 모든 이름 추출
path = "[*].assignee"  # 루트 배열의 모든 담당자 추출
```

### 복합 경로

```python
# 객체 및 배열 접근 결합
path = "orders[0].items[1].price"  # 첫 번째 주문의 두 번째 항목 가격 추출
path = "projects[*].tasks[*].assignee"  # 모든 프로젝트 작업의 모든 담당자 추출
```

## 예시

### 기본 객체 값 추출

```python
# 입력 JSON: {"user": {"name": "John", "age": 30}}
# Path: "user.name"
# 출력: "John"  # 원시 Python 문자열 값 반환
```

### 배열 요소 추출

```python
# 입력 JSON: {"items": [{"title": "Book", "price": 25}, {"title": "Magazine", "price": 10}]}
# Path: "items[0].title"
# 출력: "Book"  # 원시 Python 문자열 값 반환
```

### 중첩 배열 접근

```python
# 입력 JSON: {"orders": [{"items": [{"name": "Product A"}, {"name": "Product B"}]}]}
# Path: "orders[0].items[1].name"
# 출력: "Product B"  # 원시 Python 문자열 값 반환
```

### 존재하지 않는 경로

```python
# 입력 JSON: {"user": {"name": "John"}}
# Path: "user.email"
# 출력: {}  # 존재하지 않는 경로의 경우 빈 딕셔너리(Python dict 객체) 반환
```

### 다양한 반환 타입

이 노드는 JMESPath로부터 추출된 데이터 타입과 일치하는 원시 Python 값을 직접 반환합니다:

```python
# 문자열 추출
# 입력 JSON: {"product": {"name": "Widget", "category": "Electronics"}}
# Path: "product.name"
# 출력: "Widget"  # Python str

# 객체 추출
# 입력 JSON: {"user": {"name": "John", "age": 30}}
# Path: "user"
# 출력: {"name": "John", "age": 30}  # Python dict

# 배열 추출
# 입력 JSON: {"items": [{"title": "Book"}, {"title": "Magazine"}]}
# Path: "items"
# 출력: [{"title": "Book"}, {"title": "Magazine"}]  # Python list
```

**중요 참고 사항:**

- 노드는 네이티브 Python 타입(str, dict, list, int, bool 등)을 반환하므로 별도의 JSON 직렬화가 필요하지 않습니다.
- 따라서 파싱 과정 없이 다른 노드에서 출력을 직접 사용할 수 있습니다.
- 라이브러리 내의 다른 JSON 노드들(JsonInput, JsonReplace 등)과 일관되게 동작합니다.
- JMESPath가 모든 타입 변환을 자동으로 처리합니다.

## 사용 사례

- **API 응답 처리**: API 응답에서 특정 필드 추출
- **데이터 필터링**: 대규모 JSON 객체에서 필요한 데이터만 선별
- **구성 접근**: 구성 파일에서 특정 설정값 추출
- **데이터 변환**: 워크플로의 다른 노드를 위한 데이터 전처리

## 관련 노드

- [JSON Input](json_input.md) - 입력으로부터 JSON 데이터 생성
- [JSON Replace](json_replace.md) - JSON 내의 값 교체
- [Display JSON](display_json.md) - JSON 데이터 표시 및 서식 지정
- [To JSON](../convert/to_json.md) - 다른 데이터 타입을 JSON으로 변환
