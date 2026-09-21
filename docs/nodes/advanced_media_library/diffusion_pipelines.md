# Diffusion Pipelines

!!! warning "Hugging Face Diffusion Pipeline 노드를 사용하려면 사전 설정 단계가 필요합니다"

    Hugging Face 계정 설정, 액세스 토큰 생성, 그리고 이 노드가 완벽하게 동작하는 데 필요한 모델 설치 방법은 [이 가이드](../../guides/integrations/hugging_face.md)를 참조하세요.

## 어떤 노드인가요?

Diffusion Pipeline 시스템은 효율적인 이미지 생성을 위해 서로 보완하는 두 개의 노드로 구성됩니다:

- **Diffusion Pipeline Builder**: 여러 실행 노드에서 재사용할 수 있도록 🤗 Diffusers Pipeline을 빌드하고 캐시합니다.
- **Generate Image (Diffusion Pipeline)**: 캐시된 파이프라인을 사용하여 이미지를 생성합니다.

이러한 모듈식 접근 방식을 통해 파이프라인을 한 번만 구성하고 여러 번 재사용할 수 있어 성능과 리소스 효율성을 향상시킬 수 있습니다. 이 시스템은 동적 파라미터를 통해 다양한 제공업체(Provider)와 모델을 지원합니다.

## 지원되는 제공업체 (Supported Providers)

Diffusion Pipeline Builder는 여러 AI 모델 제공업체를 지원합니다:

- **Flux** - 고품질 텍스트-투-이미지(Text-to-Image) 생성
- **Qwen** - 멀티모달 기능
- **Stable Diffusion** - 인기 있는 오픈 소스 디퓨전 모델
- **Allegro** - 비디오 생성 기능
- **Amused** - 효율적인 마스크 기반 이미지 모델링
- **AudioLDM** - 텍스트 기반 오디오 생성
- **WAN** - 특화된 이미지 생성
- **Wuerstchen** - 효율적인 디퓨전 아키텍처
- **Custom** - 커스텀 파이프라인 구성 및 자체 제공 모델 지원

## 언제 사용하나요?

다음과 같은 경우에 이 노드들을 사용합니다:

- 다양한 모델 아키텍처를 사용하여 텍스트 설명으로부터 이미지를 생성할 때
- 크리에이티브 프로젝트를 위해 고급 이미지 생성 모델을 활용하고자 할 때
- 다양한 제공업체 및 모델 구성을 실험해 보고자 할 때
- 여러 생성 작업에 걸쳐 캐시된 파이프라인을 재사용하여 성능을 최적화할 때
- 오디오, 비디오 또는 멀티모달 생성을 위한 특화 모델을 다룰 때

## 사용 방법

### 기본 설정

Diffusion Pipeline 시스템은 두 개의 노드로 구성된 워크플로를 사용합니다:

1. **Builder 구성**:

    - 워크플로에 "Diffusion Pipeline Builder" 노드를 추가합니다.
    - 원하는 제공업체(Flux, Stable Diffusion 등)를 선택합니다.
    - 제공업체별 파라미터(모델, LoRA, 최적화)를 구성합니다.
    - Builder를 실행하여 파이프라인을 캐시합니다.

1. **이미지 생성**:

    - "Generate Image (Diffusion Pipeline)" 노드를 추가합니다.
    - Builder의 pipeline 출력을 런타임 노드에 연결합니다.
    - 생성 파라미터(프롬프트, 크기, 스텝 수 등)를 구성합니다.
    - 런타임 노드를 실행하여 이미지를 생성합니다.

### Pipeline Builder 파라미터 (Pipeline Builder Parameters)

Builder 노드는 선택된 제공업체에 따라 동적으로 변경되는 파라미터를 제공합니다:

- **provider**: 지원되는 제공업체(Flux, Stable Diffusion 등) 중 선택
- **제공업체별 파라미터**: 모델 선택, LoRA 구성, 최적화 설정
- **pipeline**: 캐시된 파이프라인 구성이 포함된 출력 연결

### 런타임 파라미터 (Runtime Parameters)

런타임 노드의 파라미터는 연결된 파이프라인을 기반으로 동적으로 생성됩니다:

- **pipeline**: Builder 노드로부터의 입력 연결
- **동적 생성 파라미터**: 프롬프트, 크기, 추론 스텝 수, 가이던스 스케일(Guidance Scale)
- **output_image**: ImageArtifact 형태의 생성된 이미지
- **seed**: 난수 생성을 위한 정수형 시드 값
- **logs**: 이미지 생성 프로세스의 상세 로그

!!! note "동적 파라미터"

    두 노드 모두 선택 사항에 따라 자동으로 조정되는 동적 파라미터를 사용합니다. 다른 제공업체를 선택하거나 다른 파이프라인을 연결하면 사용 가능한 파라미터가 변경됩니다.

### 고급 기능

- **파이프라인 캐싱 (Pipeline Caching)**: 효율적인 재사용을 위해 빌드된 파이프라인은 구성 해시(Hash)를 사용하여 캐시됩니다.
- **LoRA 지원 (LoRA Support)**: 모델 커스터마이징을 위한 LoRA 어댑터를 로드하고 구성할 수 있습니다.
- **최적화 옵션 (Optimization Options)**: 성능 향상을 위한 다양한 최적화 기능을 활성화할 수 있습니다.
- **실시간 미리보기 (Real-time Previews)**: 생성 중 중간 이미지 미리보기를 제공합니다(추론 속도가 다소 느려질 수 있음).
- **연결 유지 (Connection Preservation)**: 런타임 노드는 파이프라인이 변경되어도 파라미터 연결을 유지합니다.

## 수동 메모리 설정 (Manual Memory Settings)

Diffusion Pipeline Builder는 기본적으로 `memory_optimization_strategy`가 **Manual**로 설정되어 제공됩니다. Manual 모드가 기본값인 이유는 Automatic 전략이 다소 보수적이기 때문입니다. Automatic 모드는 모델을 적재하는 데 필요한 모든 최적화를 활성화하므로 고성능 GPU에서 병목을 유발하고 생성 속도를 늦출 수 있습니다. 반면 수동 모드는 [🤗 Diffusers 메모리 최적화 개념](https://huggingface.co/docs/diffusers/main/en/optimization/memory)에 대한 이해가 필요한 일련의 토글 옵션을 제공합니다.

이 섹션에서는 각 수동 모드 파라미터의 역할, 트레이드오프, 그리고 활성화 시점에 대한 가이드를 설명합니다.

### `attention_slicing`

- **역할**: 어텐션(Attention) 연산을 한 번에 수행하지 않고 순차적인 슬라이스로 나누어 계산하여 어텐션 수행 중 최대 VRAM 사용량을 줄입니다.
- **트레이드오프**: 속도를 희생하여 메모리를 절약합니다(대체로 5–20% 느려짐).
- **활성화 시점**: 생성 중 메모리 부족(OOM) 오류가 발생할 때, 특히 VRAM이 8GB 미만인 GPU, 통합 메모리가 64GB 미만인 Apple Silicon(MPS), 또는 CPU 환경에서 활성화하세요. 메모리 여유가 있다면 비활성화 상태로 두세요.

### `vae_slicing`

- **역할**: VAE 잠재 공간(Latent)을 단일 텐서 대신 배치 슬라이스로 나누어 디코딩합니다.
- **트레이드오프**: VAE 디코딩 단계에서 속도 저하를 거의 유발하지 않으면서 피크 메모리 사용량을 낮춥니다.
- **활성화 시점**: 단일 배치에서 여러 이미지를 생성할 때나 고해상도 디코딩 시 마지막 단계에서 VRAM이 부족할 때 활성화하세요. 켜두어도 성능 부담이 적으며 배치 크기가 1인 경우에는 사실상 추가 비용이 없습니다.

### `transformer_layerwise_casting`

- **역할**: 트랜스포머(또는 UNet) 가중치를 fp8(`float8_e4m3fn`)로 저장하고 연산 중에만 각 레이어를 bfloat16으로 업캐스팅합니다.
- **트레이드오프**: bfloat16 대비 트랜스포머 가중치 메모리를 약 절반으로 줄이지만, 레이어별 캐스팅으로 인한 약간의 속도 저하와 일부 모델에서의 경미한 품질 저하가 발생할 수 있습니다.
- **활성화 시점**: 가중치 압축을 거쳐야만 모델이 VRAM에 들어가지만 완전한 양자화(Quantization)는 원치 않을 때 활성화하세요. 파이프라인이 사전 양자화되어 있거나 레이어별 캐스팅을 지원하지 않는 경우 노드가 알림 로그를 출력하고 이 토글을 무시합니다.

### `cpu_offload_strategy`

- **역할**: GPU 메모리 상주량을 줄이기 위해 파이프라인 컴포넌트를 CPU RAM과 GPU VRAM 사이에서 이동시킵니다.
- **선택 항목**:
    - **None** — 모든 컴포넌트가 GPU에 상주합니다. 가장 빠르지만 가장 많은 VRAM을 요구합니다.
    - **Model** — 한 번에 하나의 전체 서브모델(예: 텍스트 인코더, 트랜스포머, VAE)만 GPU에 상주하며, 나머지는 CPU RAM에 보관되었다가 필요할 때 스왑됩니다. 적당한 VRAM 절감 효과와 완만한 속도 저하가 있습니다.
    - **Sequential** — 훨씬 더 세분화되어 개별 `nn.Module` 레이어가 필요에 따라 GPU로 스트리밍됩니다. VRAM 절감 효과가 가장 크지만 속도 저하도 가장 큽니다(수 배 느려질 수 있음).
- **선택 기준**:
    - 파이프라인이 여유 있게 적재되는 경우 **None**을 선택합니다.
    - 전체를 상주시키기에는 몇 GB 정도 메모리가 부족한 경우 **Model**을 선택합니다.
    - 그렇지 않으면 로드할 수 없는 모델을 실행하기 위한 최후의 수단으로 **Sequential**을 선택합니다.

### `quantization_mode`

- **역할**: 추론 전에 `optimum-quanto`를 통해 파이프라인 가중치를 `fp8`, `int8`, 또는 `int4`로 양자화합니다.
- **트레이드오프**: 비트 폭이 줄어들수록 품질 손실 위험이 증가하는 대신 상당한 메모리 절감 효과(`int4` ≈ bfloat16 크기의 1/4)를 얻습니다. 첫 실행 시 일회성 양자화 비용도 발생합니다.
- **활성화 시점**: 오프로딩을 적용해도 모델이 메모리에 들어가지 않거나 다른 작업(더 큰 배치, 더 긴 컨텍스트, 추가 LoRA 등)을 위해 VRAM을 확보하고자 할 때 활성화하세요. 일반적으로 `fp8`이 안전한 시작점이며, 꼭 필요한 경우에만 `int8`/`int4`로 낮추세요.

### 메모리가 부족한 경우 (OOM 발생 시)

다음 순서대로 하나씩 단계를 올려보세요: `vae_slicing` 활성화 → `attention_slicing` 활성화 → `cpu_offload_strategy`를 `Model`로 변경 → `transformer_layerwise_casting` 활성화 → `quantization_mode` 단계 낮추기(`fp8` → `int8` → `int4`) → `cpu_offload_strategy`를 `Sequential`로 변경.

### 설정이 고민될 때: Automatic으로 전환

Automatic 모드는 감지된 디바이스에 모델을 맞추는 데 필요한 최적화만 활성화하는 메모리 인식 의사결정 트리(`pipeline_utils.py`의 `_automatic_optimize_diffusion_pipeline` 참조)를 통해 파이프라인을 실행합니다. 고사양 하드웨어에서는 수동으로 미세 조정한 구성보다 느리지만, 일일이 설정을 고르고 싶지 않을 때 안전한 대체 수단이 됩니다.

기본 개념에 대한 자세한 내용은 [🤗 Diffusers 메모리 최적화 가이드](https://huggingface.co/docs/diffusers/main/en/optimization/memory)를 참조하세요.

## 성능 최적화 (Performance Optimization)

- **파이프라인 재사용**: 하나의 Builder에 여러 런타임 노드를 연결하여 한 번 빌드하고 여러 번 생성하세요.
- **캐시 관리**: 파이프라인은 자동으로 캐시되어 워크플로 실행 간에 재사용됩니다.
- **메모리 관리**: 하드웨어 환경에 맞게 Builder에서 최적화 설정을 구성하세요.
- **미리보기 설정**: 더 빠른 생성을 원하면 중간 미리보기를 비활성화하세요.

## 자주 묻는 질문 및 문제 해결

- **API 키 누락**: Hugging Face API 토큰이 `HUGGINGFACE_HUB_ACCESS_TOKEN`으로 설정되어 있는지 확인하세요. 설정 방법은 [이 가이드](../../guides/integrations/hugging_face.md)에 나와 있습니다.
- **파이프라인을 찾을 수 없음**: 캐시 오류가 발생하면 Builder 노드가 정상적으로 실행되었는지 확인하세요.
- **메모리 제약**: 대형 모델이나 고해상도 생성에는 상당한 양의 GPU 메모리가 필요할 수 있습니다.
- **제공업체 호환성**: 선택한 모델이 지정한 파이프라인 유형과 호환되는지 확인하세요.
