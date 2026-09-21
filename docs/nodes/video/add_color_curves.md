# AddColorCurves

## 어떤 노드인가요?

AddColorCurves 노드는 FFmpeg의 `curves` 필터를 사용하여 비디오에 색보정(Color grading) 효과를 적용합니다. 이 필터는 비디오의 톤 커브(tonal curves)를 조절하여 정교한 색상 조작을 지원하며, 내장 프리셋과 고급 색보정을 위한 커스텀 커브 옵션을 모두 제공합니다.

## 언제 사용하나요?

다음과 같은 상황에서 AddColorCurves 노드를 사용합니다:

- 비디오에 영화 같은(Cinematic) 색보정 효과를 적용하고 싶을 때
- 여러 비디오 클립의 색상 스타일을 일치시켜야 할 때
- 특정한 시각적 미학(aesthetic)이 요구되는 콘텐츠를 제작할 때
- 비디오의 분위기와 감성을 강화하고 싶을 때
- 전문적인 색상 보정 및 그레이딩이 필요할 때
- 일관된 색상 처리가 필요한 프로젝트를 진행할 때

## 사용 방법

### 기본 설정

1. 워크플로우에 AddColorCurves 노드를 추가합니다.
1. "video" 입력에 비디오 소스를 연결합니다.
1. 커브 프리셋을 선택하거나 커스텀 커브를 정의합니다.
1. 워크플로우를 실행하여 색보정을 적용합니다.

### 파라미터 (Parameters)

- **video**: 색상 커브를 적용할 비디오 콘텐츠입니다. (VideoArtifact 및 VideoUrlArtifact 지원)

- **curve_preset**: 색보정 효과를 위한 내장 커브 프리셋입니다. (기본값: "none")

    - **none**: 커브를 적용하지 않습니다.
    - **color_negative**: 컬러 네거티브 필름 룩
    - **cross_process**: 크로스 프로세스(Cross-processed) 필름 효과
    - **darker**: 전반적으로 어두운 톤
    - **increase_contrast**: 적당한 대비(Contrast) 증가
    - **lighter**: 전반적으로 밝은 톤
    - **linear_contrast**: 선형 대비 조절
    - **medium_contrast**: 중간 수준의 대비 강화
    - **negative**: 흑백 네거티브 효과
    - **strong_contrast**: 드라마틱한 고대비 룩
    - **vintage**: 클래식 빈티지 필름 룩

- **processing_speed**: 처리 속도와 출력 품질 간의 균형을 조절합니다. (기본값: "balanced")

    - **fast**: 가장 빠른 처리 속도, 낮은 품질 (ultrafast 프리셋, CRF 30)
    - **balanced**: 속도와 품질의 균형 잡힌 설정 (medium 프리셋, CRF 23)
    - **quality**: 최고 품질, 느린 처리 속도 (slow 프리셋, CRF 18)

### 출력 (Outputs)

- **video**: 색상 커브가 적용된 비디오이며, 다른 노드에 연결할 수 있는 출력으로 제공됩니다.

## 예시

### 예시 1: 빈티지 효과 적용

1. LoadVideo 노드의 비디오 출력을 AddColorCurves의 "video" 입력에 연결합니다.
1. "curve_preset"을 "vintage"로 설정합니다.
1. 워크플로우를 실행합니다. 비디오에 클래식 빈티지 필름 느낌이 적용됩니다.
1. 출력 파일명은 `{original_filename}_curves_vintage.{format}` 형태가 됩니다.

### 예시 2: 강한 대비(Strong Contrast) 적용

1. 비디오를 AddColorCurves 노드에 연결합니다.
1. "curve_preset"을 "strong_contrast"로 설정합니다.
1. 워크플로우를 실행합니다. 비디오의 대비가 드라마틱하게 강화됩니다.
1. 출력 파일명은 `{original_filename}_curves_strong_contrast.{format}` 형태가 됩니다.

### 예시 3: 크로스 프로세스 효과 적용

1. 비디오를 AddColorCurves 노드에 연결합니다.
1. "curve_preset"을 "cross_process"로 설정합니다.
1. 워크플로우를 실행합니다. 비디오에 크로스 프로세스 필름 룩이 적용됩니다.
1. 출력 파일명은 `{original_filename}_curves_cross_process.{format}` 형태가 됩니다.

## 중요 참고 사항

- AddColorCurves 노드는 고품질 색보정을 위해 FFmpeg의 curves 필터를 사용합니다.
- 내장 프리셋을 통해 일반적인 색보정 효과를 빠르게 적용할 수 있습니다.
- 처리 시간은 비디오의 길이와 해상도에 따라 달라집니다.
- 커브 조절은 비디오의 시각적 분위기와 스타일에 큰 영향을 미칠 수 있습니다.
- 최상의 결과를 얻으려면 다른 색상 조정을 마친 후에 커브를 적용하는 것이 좋습니다.

## 파라미터 권장 사항

### 시네마틱 룩

- "vintage" 또는 "cross_process" 프리셋을 사용하세요.
- 독특한 필름 느낌을 내려면 "color_negative"를 사용해 보세요.

### 고대비 룩

- "strong_contrast" 또는 "negative"를 사용하세요.
- 미묘한 대비 향상을 원할 경우 "increase_contrast"를 사용해 보세요.
