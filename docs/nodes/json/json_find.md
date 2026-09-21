# JSON Find

검색 기준에 따라 JSON 배열에서 항목을 찾습니다.

## 어떤 노드인가요?

JSON Find 노드를 사용하면 JSON 배열을 검색하여 특정 기준과 일치하는 항목을 찾을 수 있습니다. 다양한 검색 모드, 대소문자 구분 옵션을 지원하며 첫 번째 일치 항목 또는 모든 일치 항목을 반환할 수 있습니다. 복잡한 데이터 구조를 필터링하고 검색하는 데 적합합니다.

## 파라미터 (Parameters)

### 입력 (Inputs)

| 파라미터 (Parameter) | 타입 (Type)     | 설명 (Description)                                                        | 기본값 (Default) |
| -------------------- | --------------- | ------------------------------------------------------------------------- | ---------------- |
| `json`               | json, str, dict | 검색 대상 JSON 데이터 (배열이거나 배열을 포함해야 함)                     | `[]`             |
| `search_field`       | str             | 검색할 필드의 점 표기법 경로 (예: 'attributes.content')                   | `""`             |
| `search_value`       | str             | 찾을 검색값                                                               | `""`             |
| `search_mode`        | str             | 검색 모드: 'exact', 'contains' 또는 'starts_with'                         | `"exact"`        |
| `return_mode`        | str             | 반환 모드: 첫 번째 일치 항목은 'first', 모든 일치 항목은 'all'            | `"first"`        |
| `case_sensitive`     | bool            | 검색 시 대소문자 구분 여부                                                | `true`           |

### 출력 (Outputs)

| 파라미터 (Parameter) | 타입 (Type) | 설명 (Description)                                                                       |
| -------------------- | ----------- | ---------------------------------------------------------------------------------------- |
| `found_item`         | json        | 발견된 항목 (return_mode가 'first'인 경우 단일 항목, 'all'인 경우 배열)                  |
| `found_count`        | int         | 발견된 항목의 총 개수                                                                    |
| `found_index`        | int         | 첫 번째 발견된 항목의 인덱스 (발견되지 않은 경우 -1)                                     |

## 주요 기능

- **유연한 검색 모드**: 완전 일치(exact), 포함(contains), 시작 단어 일치(starts with) 지원
- **대소문자 구분 제어**: 대소문자 구분 또는 비구분 검색 선택 가능
- **다양한 반환 옵션**: 첫 번째 일치 항목 또는 전체 일치 항목 가져오기
- **중첩 필드 접근**: 점 표기법을 사용하여 JSON 구조의 깊은 곳까지 검색
- **배열 자동 감지**: 일반적인 필드 이름에서 배열을 자동으로 검색
- **실시간 업데이트**: 입력이 변경되면 자동으로 결과 업데이트

## 검색 모드

### 완전 일치 (`exact`)

필드 값이 검색값과 정확히 일치하는 항목을 찾습니다.

```python
# 검색어: "Design"
# 일치: "Design"
# 불일치: "Design Task", "design", "My Design"
```

### 포함 (`contains`)

필드 값에 검색값이 부분 문자열로 포함되어 있는 항목을 찾습니다.

```python
# 검색어: "Design"
# 일치: "Design", "Design Task", "My Design Work"
# 불일치: "design" (case_sensitive=true인 경우)
```

### 시작 단어 일치 (`starts_with`)

필드 값이 검색값으로 시작하는 항목을 찾습니다.

```python
# 검색어: "Design"
# 일치: "Design", "Design Task", "Designer"
# 불일치: "My Design", "design"
```

## 필드 경로 구문

`search_field` 파라미터는 JSON Extract Value와 동일한 점 표기법을 사용합니다:

### 기본 객체 접근

```python
search_field = "name"  # 루트 name 필드에서 검색
search_field = "attributes.content"  # 중첩된 content 필드에서 검색
search_field = "user.profile.email"  # 깊게 중첩된 email 필드에서 검색
```

### 배열 인덱싱

```python
search_field = "items[0].title"  # 첫 번째 항목의 title에서 검색
search_field = "users[2].name"  # 세 번째 사용자의 name에서 검색
```

## 예시

### 콘텐츠로 작업 찾기

```python
# 입력 JSON: [
#   {"attributes": {"content": "Design", "status": "wtg"}},
#   {"attributes": {"content": "Model", "status": "wtg"}},
#   {"attributes": {"content": "Design Review", "status": "ip"}}
# ]
# search_field: "attributes.content"
# search_value: "Design"
# search_mode: "exact"
# return_mode: "first"
# 결과: {"attributes": {"content": "Design", "status": "wtg"}}
```

### 특정 상태의 모든 작업 찾기

```python
# 입력 JSON: [
#   {"attributes": {"content": "Design", "status": "wtg"}},
#   {"attributes": {"content": "Model", "status": "wtg"}},
#   {"attributes": {"content": "Review", "status": "ip"}}
# ]
# search_field: "attributes.status"
# search_value: "wtg"
# search_mode: "exact"
# return_mode: "all"
# 결과: [{"attributes": {"content": "Design", "status": "wtg"}},
#        {"attributes": {"content": "Model", "status": "wtg"}}]
```

### 대소문자를 구분하지 않는 검색

```python
# 입력 JSON: [
#   {"name": "Design Task"},
#   {"name": "design review"},
#   {"name": "DESIGN WORK"}
# ]
# search_field: "name"
# search_value: "design"
# search_mode: "contains"
# case_sensitive: false
# return_mode: "all"
# 결과: 세 항목 모두 반환 ("Design", "design", "DESIGN" 모두 일치)
```

### 특정 단어로 시작하는 항목 찾기

```python
# 입력 JSON: [
#   {"title": "Design Task"},
#   {"title": "Design Review"},
#   {"title": "My Design Work"}
# ]
# search_field: "title"
# search_value: "Design"
# search_mode: "starts_with"
# return_mode: "all"
# 결과: [{"title": "Design Task"}, {"title": "Design Review"}]
```

### 복잡한 중첩 구조 검색

```python
# 입력 JSON: [
#   {
#     "relationships": {
#       "entity": {"data": {"name": "convertible", "type": "Asset"}}
#     }
#   }
# ]
# search_field: "relationships.entity.data.name"
# search_value: "convertible"
# search_mode: "exact"
# return_mode: "first"
# 결과: 일치하는 항목 반환
```

## 배열 자동 감지

입력 JSON이 배열을 포함하는 객체인 경우, 노드는 다음과 같은 일반적인 배열 필드 이름을 자동으로 탐색합니다:

- `data`
- `items`
- `results`
- `list`

```python
# 입력 JSON: {"data": [{"name": "Item 1"}, {"name": "Item 2"}]}
# 노드가 "data" 배열 내부를 자동으로 검색합니다.
```

## 사용 사례

- **작업 관리**: 내용, 상태 또는 담당자별로 특정 작업 찾기
- **데이터 필터링**: 특정 기준에 따라 대규모 데이터셋 필터링
- **API 응답 처리**: API 응답 배열 내에서 데이터 검색
- **구성 조회**: 특정 구성 항목 조회
- **콘텐츠 탐색**: 콘텐츠 컬렉션에서 원하는 항목 검색
- **사용자 관리**: 이름, 이메일 또는 역할별로 사용자 찾기

## 관련 노드

- [JSON Extract Value](json_extract_value.md) - 경로를 통해 단일 값 추출
- [JSON Input](json_input.md) - 입력으로부터 JSON 데이터 생성
- [JSON Replace](json_replace.md) - JSON 내의 값 교체
- [Display JSON](display_json.md) - JSON 데이터 표시 및 서식 지정
- [To JSON](../convert/to_json.md) - 다른 데이터 타입을 JSON으로 변환
