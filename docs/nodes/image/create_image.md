# GenerateImage

## 어떤 노드인가요?

GenerateImage 노드는 Griptape Cloud 서비스를 활용하여 텍스트 프롬프트를 기반으로 이미지를 생성하는 노드입니다. Griptape Cloud Image Generation Driver 및 Prompt Driver와 연동되어 텍스트 설명을 고품질 이미지로 변환합니다.

## 언제 사용하나요?

다음과 같은 상황에서 GenerateImage 노드를 사용합니다:

- 워크플로우 내에서 동적으로 이미지를 생성해야 할 때
- 텍스트 설명을 바탕으로 시각적 콘텐츠를 제작하고자 할 때
- 자연어로 묘사된 개념을 시각화해야 할 때

## 사용 방법

### 기본 설정

1. 워크플로우에 GenerateImage 노드를 추가합니다.
1. 생성하고자 하는 이미지를 설명하는 텍스트 프롬프트를 입력합니다.
1. 워크플로우를 실행합니다!

### 파라미터 (Parameters)

- **agent**: 프롬프트 및 이미지 생성 작업을 처리하는 에이전트 (Agent 또는 dict)
- **image_generation_driver**: 이미지 생성에 사용할 드라이버 (기본값: None)
- **prompt**: 생성할 이미지에 대한 텍스트 설명 (string)
- **enhance_prompt**: 이미지 품질 향상을 위해 프롬프트를 자동 보강할지 여부 (boolean, 기본값: True)

### 출력 (Outputs)

- **output**: ImageUrlArtifact 형식의 생성된 이미지

## 예시

이미지를 생성하고 저장하는 간단한 워크플로우:

1. 워크플로우에 GenerateImage 노드를 추가합니다.
1. agent 파라미터에 Agent 노드를 연결합니다.
1. prompt를 "A serene mountain landscape at sunset with a lake reflecting the orange sky" (주황빛 하늘이 반사되는 호수가 있는 고요한 일몰의 산 풍경)로 설정합니다.
1. 생성된 이미지를 저장하기 위해 output을 SaveImage 노드에 연결합니다.

## 중요 참고 사항

- Griptape Cloud 인증을 위해 환경 변수에 `GT_CLOUD_API_KEY`를 설정해야 합니다.
- 이 노드는 기본 모델로 'dall-e-3'을 사용하고 기본 품질로 'hd'를 사용합니다.
- 프롬프트 보강(enhance_prompt)은 이미지 품질을 높일 수 있지만 의도한 프롬프트와 다소 다르게 해석될 수 있습니다.
