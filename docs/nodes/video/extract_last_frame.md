# ExtractLastFrame

## 어떤 노드인가요?

ExtractLastFrame 노드는 비디오의 마지막 프레임을 이미지로 추출하여 워크플로우에서 사용할 수 있도록 출력합니다.

## 언제 사용하나요?

다음과 같은 상황에서 ExtractLastFrame 노드를 사용합니다:

- 워크플로우에서 사용하기 위해 비디오의 마지막 프레임을 추출해야 할 때

## 사용 방법

### 기본 설정

1. 워크플로우에 ExtractLastFrame 노드를 추가합니다.
1. `video` 파라미터에 입력을 연결하거나 비디오를 수동으로 선택합니다.
1. `last_frame_image` 출력을 이미지 입력이 필요한 다른 노드에 연결합니다.

### 파라미터 (Parameters)

- **video**: 마지막 프레임을 추출할 비디오 콘텐츠 (VideoArtifact 및 VideoUrlArtifact 지원)

### 출력 (Outputs)

- **last_frame_image**: 비디오에서 추출된 마지막 프레임 (ImageUrlArtifact 형태)

## 예시

Image-to-Video 모델을 사용할 때 여러 짧은 비디오 클립을 연결하여 하나의 긴 비디오를 만들고자 할 수 있습니다.

1. 워크플로우에 ExtractLastFrame 노드를 추가합니다.
1. Image-to-Video 노드의 출력을 ExtractLastFrame 노드의 **video** 입력에 연결합니다.
1. ExtractLastFrame 노드 또는 워크플로우가 실행되면 비디오의 마지막 프레임이 노드에 표시됩니다.

## 중요 참고 사항

- 일반적인 비디오 형식(mp4, avi, mov 등)을 지원합니다.
- 비디오 플레이어 컨트롤을 통해 비디오를 재생, 일시 정지 및 탐색(scrub)할 수 있습니다.

## 자주 묻는 질문 및 문제 해결

- **비디오가 표시되지 않음**: 이 노드에 비디오 소스가 올바르게 연결되어 있는지 확인하세요.
