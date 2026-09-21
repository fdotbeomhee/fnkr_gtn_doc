# SearchReplaceText

## 어떤 노드인가요?

SearchReplaceText 노드는 여러 줄 텍스트 콘텐츠에서 찾기 및 바꾸기 작업을 수행할 수 있는 노드입니다. 단순 텍스트 바꾸기와 정규 표현식 기반 찾기 및 바꾸기를 모두 지원합니다.

## 언제 사용하나요?

다음과 같은 경우에 SearchReplaceText 노드를 사용하세요:

- 특정 패턴을 대체하여 여러 줄 텍스트를 수정해야 할 때
- 대소문자를 구분하거나 구분하지 않는 텍스트 바꾸기를 수행하고 싶을 때
- 복잡한 패턴 일치를 위해 정규 표현식을 사용해야 할 때
- 패턴의 모든 일치 항목 또는 첫 번째 일치 항목만 바꾸고 싶을 때
- 여러 줄이나 여러 단락으로 구성된 텍스트로 작업해야 할 때

## 사용 방법

### 기본 설정

1. 워크플로우에 SearchReplaceText 노드를 추가합니다.
1. 입력 텍스트를 연결하거나 설정합니다 (여러 줄 가능).
1. 검색 패턴(search pattern)을 설정합니다.
1. 바꿀 텍스트(replacement text)를 설정합니다 (여러 줄 가능).
1. 필요한 추가 옵션을 구성합니다.
1. 출력을 텍스트 입력을 받는 노드에 연결합니다.

### 파라미터 (Parameters)

- **input_text**: 찾기 및 바꾸기를 수행할 여러 줄 텍스트 (문자열)
- **search_pattern**: 검색할 텍스트 또는 패턴 (문자열)
- **replacement_text**: 검색 패턴을 대체할 여러 줄 텍스트 (문자열)
- **case_sensitive**: 대소문자를 구분하여 검색할지 여부 (부울, 기본값: true)
- **use_regex**: 검색 패턴을 정규 표현식으로 처리할지 여부 (부울, 기본값: false)
- **replace_all**: 모든 일치 항목을 바꿀지, 첫 번째 항목만 바꿀지 여부 (부울, 기본값: true)

### 출력 (Outputs)

- **output**: 찾기 및 바꾸기가 수행된 후의 여러 줄 텍스트 (문자열)

## 예시

### 단순 텍스트 바꾸기

1. 워크플로우에 SearchReplaceText 노드를 추가합니다.
1. 입력 텍스트를 다음과 같이 설정합니다:
    ```
    Hello World
    Welcome to Griptape
    ```
1. search_pattern을 "World"로 설정합니다.
1. replacement_text를 "Griptape"로 설정합니다.
1. 출력 결과는 다음과 같습니다:
    ```
    Hello Griptape
    Welcome to Griptape
    ```

### 대소문자 비구분 바꾸기

1. 워크플로우에 SearchReplaceText 노드를 추가합니다.
1. 입력 텍스트를 다음과 같이 설정합니다:
    ```
    Hello WORLD
    Welcome to the WORLD
    ```
1. search_pattern을 "world"로 설정합니다.
1. replacement_text를 "Griptape"로 설정합니다.
1. case_sensitive를 false로 설정합니다.
1. 출력 결과는 다음과 같습니다:
    ```
    Hello Griptape
    Welcome to the Griptape
    ```

### 정규 표현식 예시

!!! example "기본 정규식 패턴"

    ```
    Input: "Line 1\nLine 2\nLine 3"
    Search Pattern: "Line \\d"
    Replacement: "Item"
    Use Regex: true
    Output: "Item\nItem\nItem"
    ```

    이 패턴은 "Line" 뒤에 임의의 숫자 하나(`\d`)가 오는 패턴과 일치합니다.

!!! example "숫자 제거"

    ```
    Input: "Product123, Item456, Order789"
    Search Pattern: "\\d+"
    Replacement: ""
    Use Regex: true
    Output: "Product, Item, Order"
    ```

    이 패턴은 하나 이상의 숫자(`\d+`)와 일치합니다.

!!! example "단어 경계"

    ```
    Input: "cat in the hat"
    Search Pattern: "\\bcat\\b"
    Replacement: "dog"
    Use Regex: true
    Output: "dog in the hat"
    ```

    이 패턴은 완전한 단어로 나타날 때만 단어 "cat"과 일치합니다.

!!! example "여러 줄"

    ```
    Input: "First line\nSecond line\nThird line"
    Search Pattern: "^.*$"
    Replacement: "New line"
    Use Regex: true
    Output: "New line\nNew line\nNew line"
    ```

    이 패턴은 전체 줄과 일치합니다 (`^` 시작, `.*` 모든 문자, `$` 끝).

## 정규식 참고 자료

!!! note "자주 사용되는 정규식 패턴"

    | Pattern | Description | Example |
    | ------- | ----------- | ------- |
    | `\n` | 줄바꿈 일치 | `Line 1\nLine 2` |
    | `\s` | 모든 공백 문자 일치 | `Hello\sWorld` |
    | `\d` | 모든 숫자 일치 | `\d+` matches "123" |
    | `[a-z]` | 모든 소문자 일치 | `[a-z]+` matches "hello" |
    | `[A-Z]` | 모든 대문자 일치 | `[A-Z]+` matches "WORLD" |
    | `.` | 모든 문자 일치 | `a.c` matches "abc" |
    | `*` | 0개 이상 일치 | `a*` matches "", "a", "aa" |
    | `+` | 1개 이상 일치 | `a+` matches "a", "aa" |
    | `?` | 0개 또는 1개 일치 | `a?` matches "", "a" |
    | `\b` | 단어 경계 | `\bcat\b` matches "cat" but not "catch" |

!!! warning "정규식 모드"

    정규식 모드를 사용할 때 검색 패턴의 특수 문자는 정규식 문법으로 처리됩니다. 특수 문자를 문자 그대로 일치시키려면 반드시 이스케이프해야 합니다.

!!! tip "일반 텍스트 모드"

    정규식 모드를 사용하지 않을 때 검색 패턴은 리터럴 텍스트로 처리되며 특수 문자는 자동으로 이스케이프됩니다. 단순 텍스트 교체에는 이 방식이 더 안전합니다.

## 중요 참고 사항

- 정규 표현식을 사용할 때는 특수 문자를 올바르게 이스케이프해야 합니다.
- 대소문자 비구분 검색은 일반 텍스트와 정규 표현식 모두에서 작동합니다.
- 검색 패턴을 찾지 못한 경우 원본 텍스트가 변경 없이 그대로 반환됩니다.
- 유효하지 않은 정규 표현식을 입력하면 원본 텍스트가 그대로 반환됩니다.
- 대소문자 비구분 교체 시 노드는 텍스트의 원래 대소문자 형식을 보존합니다.
- 입력 및 출력 텍스트 모두에서 줄바꿈이 유지됩니다.
- 정규식 모드를 사용할 때 검색 패턴에서 `\n`을 사용하여 줄바꿈과 일치시킬 수 있습니다.

## 자주 묻는 질문 및 문제 해결

- use_regex가 활성화되었을 때 정규 표현식 구문 오류 발생
- 대소문자 구분 및 비구분 작업을 혼용할 때 예기치 않은 결과 발생
- 매우 큰 텍스트와 복잡한 정규 표현식 사용 시 성능 저하 발생
- 정규식 모드를 사용하지 않을 때 줄바꿈 처리 오류

## 추가 자료

더 포괄적인 정규식 예제와 패턴은 다음을 확인하세요:

- [Python Regular Expression HOWTO](https://docs.python.org/3/howto/regex.html)
- [Regex101](https://regex101.com/) - 대화형 정규식 테스트 및 디버깅
- [Regular-Expressions.info](https://www.regular-expressions.info/) - 상세한 정규식 튜토리얼
