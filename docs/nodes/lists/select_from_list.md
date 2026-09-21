# Select From List

드롭다운 인터페이스를 사용하여 문자열 리스트에서 항목을 선택합니다.

## 설명 (Description)

Select From List 노드는 사용자가 드롭다운 인터페이스를 통해 문자열 리스트에서 단일 항목을 선택할 수 있도록 합니다. 입력 리스트가 변경되면 선택 옵션을 자동으로 업데이트하며, 가능한 경우 현재 선택 항목을 유지합니다.

## 파라미터 (Parameters)

### 입력 파라미터 (Input Parameters)

| 파라미터 | 타입 | 설명 | 기본값 |
| --------- | ---- | ---------------------------- | ------- |
| `list`    | list | 선택할 항목들의 리스트 | `[]`    |

### 출력 파라미터 (Output Parameters)

| 파라미터 | 타입 | 설명 |
| --------------- | ------ | --------------------------- |
| `selected_item` | string | 현재 선택된 항목 |

## 주요 기능 (Features)

- **동적 드롭다운**: 입력 리스트의 항목으로 드롭다운을 자동으로 채웁니다.
- **선택 유지**: 리스트가 업데이트되더라도 항목이 새 리스트에 남아 있으면 현재 선택을 유지합니다.
- **문자열 변환**: 일관된 비교를 위해 모든 리스트 항목을 문자열로 변환합니다.
- **실시간 업데이트**: 입력 리스트가 변경되면 즉시 업데이트됩니다.
- **안전한 처리**: 빈 리스트나 유효하지 않은 리스트를 안전하게 처리합니다.

## 예시

### 기본 리스트 선택

```python
# 입력 리스트: ["Apple", "Banana", "Cherry", "Date"]
# 사용자 선택: "Banana"
# Output: "Banana"
```

### 상태 선택

```python
# 입력 리스트: ["In Progress", "Not Started", "Waiting", "Completed", "Blocked"]
# 사용자 선택: "Completed"
# Output: "Completed"
```

### 동적 리스트 업데이트

```python
# 초기 리스트: ["Option A", "Option B", "Option C"]
# 사용자 선택: "Option B"
# 리스트 업데이트: ["Option A", "Option B", "Option D", "Option E"]
# 선택 유지: "Option B" (새 리스트에 여전히 존재함)

# 리스트가 다음으로 업데이트된 경우: ["Option X", "Option Y", "Option Z"]
# 선택 초기화: "Option X" (새 리스트의 첫 번째 항목으로 재설정)
```

## 사용 사례 (Use Cases)

- **상태 선택**: 사전 정의된 상태 값에서 선택 (예: "In Progress", "Completed")
- **카테고리 선택**: 카테고리 또는 유형 목록에서 선택
- **구성 설정 선택**: 사용 가능한 구성 옵션 중 선택
- **사용자 인터페이스**: 워크플로우에 드롭다운 선택 UI 제공
- **데이터 필터링**: 생성된 리스트에서 특정 항목 선택

## 워크플로우 예시 (Workflow Examples)

### 상태 관리 워크플로우

```
Create Text List → Select From List → 선택된 상태 처리
     ↓                    ↓
["In Progress",      "Completed"    → 작업 상태 업데이트
 "Not Started", 
 "Waiting", 
 "Completed", 
 "Blocked"]
```

### 동적 옵션 선택

```
API 응답 → 옵션 추출 → Select From List → 선택 항목 사용
   ↓           ↓              ↓
JSON 데이터 → ["Option 1",  → "Option 2"    → 선택된 항목 처리
              "Option 2", 
              "Option 3"]
```

## 동작 방식 (Behavior)

### 선택 로직

1. **초기 로드**: 리스트의 첫 번째 항목을 선택합니다.
1. **리스트 업데이트**:
    - 현재 선택 항목이 새 리스트에 존재하는 경우 → 현재 선택을 유지합니다.
    - 현재 선택 항목이 새 리스트에 없는 경우 → 새 리스트의 첫 번째 항목을 선택합니다.
1. **빈 리스트**: 선택을 지웁니다(빈 문자열).
1. **유효하지 않은 입력**: 선택을 지웁니다(빈 문자열).

### 문자열 변환

일관된 비교를 위해 모든 리스트 항목이 자동으로 문자열로 변환됩니다:

- 숫자: `123` → `"123"`
- 불리언: `True` → `"True"`
- 객체: `{"key": "value"}` → `"{'key': 'value'}"`

## 관련 노드 (Related Nodes)

- [Create Text List](create_text_list.md) - 텍스트 항목 리스트 생성
- [Create List](create_list.md) - 다양한 입력으로부터 리스트 생성
- [Get From List](get_from_list.md) - 인덱스로 리스트에서 항목 가져오기
- [Get Index Of Item](get_index_of_item.md) - 특정 항목의 인덱스 찾기
- [Display List](display_list.md) - 리스트 내용 표시
