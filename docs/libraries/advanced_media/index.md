# Advanced Media 라이브러리 (Advanced Media Library)

모델을 **로컬**에서 실행하는 고급 미디어 생성 및 조작 노드 모음입니다. 디퓨전 파이프라인, 이미지 및 비디오 전처리(깊이, 윤곽선, 포즈, 세그멘테이션), LoRA 툴링, 얼굴 감지 기능 등을 제공합니다.

- **리포지토리**: [griptape-ai/griptape-nodes-library-advanced-media](https://github.com/griptape-ai/griptape-nodes-library-advanced-media)
- **요구 사항**: GPU 사용을 강력히 권장합니다. 모델 다운로드를 위한 Hugging Face 계정 및 액세스 토큰이 필요합니다 ([Hugging Face 모델](../../guides/integrations/hugging_face.md) 참조).
- **노드 카테고리**: 노드 선택기 내 audio, diffusion, image, video, LoRA, utils

## 설치 방법

Advanced Media 라이브러리는 `gtn init` 실행 시 설치 여부를 묻습니다. 이때 등록하거나 나중에 `gtn init`을 다시 실행하여 `y`를 입력할 수 있습니다. 자세한 방법은 [자주 묻는 질문(FAQ)](../../faq.md#초기-설정-후-advanced-media-library를-설치하려면-어떻게-해야-하나요)을 참조하세요.

또는 다른 라이브러리와 마찬가지로 에디터에서 **Manage → Library Management**를 열고 **Add Library**를 클릭한 후 다음 URL을 붙여넣어 설치할 수 있습니다:

```text
https://github.com/griptape-ai/griptape-nodes-library-advanced-media
```

일반적인 설치, 업데이트 및 문제 해결 지원은 [라이브러리 가이드](../../guides/libraries.md)를 참조하세요.

## 노드 참조

이 라이브러리는 총 28개의 노드를 제공합니다. 현재 문서화된 노드는 다음과 같습니다:

- [Diffusion Pipelines](../../nodes/advanced_media_library/diffusion_pipelines.md) — 🤗 Diffusers 파이프라인을 구축 및 캐싱하고 이를 사용하여 이미지를 생성합니다.
- [YOLOv8 Face Detection](../../nodes/advanced_media_library/yolov8_face_detection.md) — 얼굴을 감지하고 바운딩 박스/마스크를 출력합니다.

!!! warning "Diffusers 라이브러리와 함께 사용 시 주의사항"

    Advanced Media 라이브러리와 [Diffusers 라이브러리](../diffusers/index.md)는 업스트림 종속성(`diffusers`, `transformers`, `torch`)을 공유하며 각자 자체 인메모리 파이프라인 캐시를 유지합니다. 동일한 세션에서 두 라이브러리를 모두 사용하면 중복된 모델 가중치의 복사본 두 개가 VRAM/RAM에 동시에 유지될 수 있습니다. 단일 세션에서는 하나의 라이브러리를 사용하는 것을 권장합니다.

## 지원 및 문의

버그를 발견했거나 기능 요청이 있으신가요?
[이슈를 등록해 주세요](https://github.com/griptape-ai/griptape-nodes-library-advanced-media/issues).
