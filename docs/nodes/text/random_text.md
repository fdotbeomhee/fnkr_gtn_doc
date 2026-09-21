# Random Text

Random Text 노드는 입력 텍스트에서 무작위 콘텐츠를 선택하거나, 입력이 제공되지 않은 경우 무작위 콘텐츠를 생성할 수 있는 노드입니다. 무작위 문자, 단어, 문장 또는 단락 선택을 지원합니다.

## 파라미터 (Parameters)

### Input Text

- **Type**: String
- **Mode**: Input/Property
- **Description**: 무작위 콘텐츠를 선택할 텍스트입니다. 비어 있는 경우, 선택 유형에 따라 무작위 콘텐츠를 생성합니다.
- **UI Options**: Multiline 활성화

### Seed

- **Type**: Integer
- **Mode**: Property
- **Description**: 재현 가능한 무작위 선택을 위한 시드 값(0-10,000)입니다. 동일한 시드를 사용하면 동일한 무작위 선택 결과가 생성됩니다.
- **UI Options**: 0-10,000 범위의 슬라이더

### Selection Type

- **Type**: String

- **Mode**: Property

- **Description**: 선택하거나 생성할 콘텐츠 유형:

    - `character`: 무작위 문자 선택
    - `word`: 무작위 단어 선택
    - `sentence`: 무작위 문장 선택 또는 새 문장 생성
    - `paragraph`: 무작위 단락 선택 또는 새 단락 생성

### Output

- **Type**: String
- **Mode**: Output
- **Description**: 무작위로 선택되거나 생성된 콘텐츠
- **UI Options**: Multiline 활성화

## 동작 방식

- 입력 텍스트가 제공된 경우:

    - 노드는 선택 유형에 따라 입력에서 무작위 콘텐츠를 선택합니다.
    - 문장 및 단락의 경우 적절한 구분 기호를 기준으로 입력을 분할합니다.
    - 일치하는 콘텐츠를 찾을 수 없는 경우 새로운 콘텐츠 생성으로 전환(fallback)됩니다.

- 입력 텍스트가 제공되지 않은 경우:

    - 문자(character): 영문자, 숫자, 구두점에서 무작위 문자를 생성합니다.
    - 단어(word): 무작위 영어 단어를 생성합니다.
    - 문장(sentence): AI 에이전트를 사용하여 자연스러운 문장을 생성합니다.
    - 단락(paragraph): AI 에이전트를 사용하여 일관성 있는 단락을 생성합니다.

## 예시

### 무작위 콘텐츠 선택

```python
# Input text: "Hello world! This is a test. How are you today?"
# Selection type: word
# Output: "world" (무작위로 선택된 단어)
```

### 무작위 콘텐츠 생성

```python
# Input text: "" (비어 있음)
# Selection type: sentence
# Output: "The quick brown fox jumps over the lazy dog." (AI 생성 문장)
```

## 중요 참고 사항

- seed 파라미터는 동일한 값을 사용할 때 재현 가능한 결과를 보장합니다.
- AI 에이전트로 콘텐츠를 생성하는 동안 노드에 로딩 메시지가 표시됩니다.
