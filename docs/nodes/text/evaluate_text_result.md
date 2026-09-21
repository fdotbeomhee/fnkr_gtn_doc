# Evaluate Text Result

Evaluate Text Result 노드는 Griptape의 Eval Engine을 사용하여 특정 기준에 따라 텍스트 출력을 평가할 수 있게 해줍니다. 이 노드는 AI가 생성한 콘텐츠를 검증하거나, 사실적 정확성을 확인하거나, 텍스트 출력의 품질을 평가할 때 유용합니다.

## 입력 (Inputs)

- **Examples** (속성): 사전 설정된 예시 중에서 선택하거나 자체 평가를 생성합니다.

    - 옵션:

        - Choose a preset..
        - Paraphrase
        - Factual
        - Analogy

- **Input** (입력/속성): 평가할 입력 텍스트

    - 여러 줄 텍스트 입력 지원

- **Expected Output** (입력/속성): 기대되거나 기준이 되는 참조 출력 텍스트

    - 한 줄 텍스트 입력

- **Actual Output** (입력/속성): 평가할 실제 출력 텍스트

    - 한 줄 텍스트 입력

- **Criteria** (입력/속성): 사용할 평가 기준

    - 여러 줄 텍스트 입력 지원
    - 예시: "Does the output accurately paraphrase the input without losing meaning?"

## 출력 (Outputs)

- **Score** (출력): 평가 점수를 나타내는 0에서 1 사이의 부동 소수점(float) 값

    - 1.0은 완벽히 일치함을 나타냄
    - 0.0은 완전히 불일치함을 나타냄

- **Reason** (출력): 평가 결과에 대한 상세한 설명

    - 해당 점수가 부여된 이유에 대한 피드백 제공
    - 발견된 불일치 사항 설명

## 예시

### Paraphrase Evaluation (패러프레이징 평가)

```python
Input: "The quick brown fox jumps over the lazy dog."
Expected Output: "A swift brown fox leaps above a sleeping dog."
Actual Output: "A fast fox jumps over a dog that's not awake."
Criteria: "Does the output accurately paraphrase the input without losing meaning?"
```

### Factual Evaluation (사실 관계 평가)

```python
Input: "The capital of France is Paris."
Expected Output: "Paris is the capital city of France."
Actual Output: "France's capital is Paris."
Criteria: "Is the output factually correct based on the input?"
```

### Analogy Evaluation (유추 평가)

```python
Input: "A bird is to sky as a fish is to ______."
Expected Output: "water"
Actual Output: "concrete"
Criteria: "Does the output correctly complete the analogy?"
```

## 중요 참고 사항

- 이 노드는 Griptape의 Eval Engine을 사용하여 평가를 수행합니다.
- 평가는 제공된 기준을 바탕으로 진행됩니다.
- 점수는 0과 1 사이로 정규화됩니다.
- Reason은 평가에 대한 상세한 피드백을 제공합니다.
- 사전 설정된 예시를 사용하거나 자체 맞춤형 평가를 생성할 수 있습니다.
