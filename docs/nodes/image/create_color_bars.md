# CreateColorBars

## 어떤 노드인가요?

CreateColorBars 노드는 비디오 캘리브레이션, 디스플레이 테스트, 색상 정확도 검증에 사용되는 표준 컬러 바(Color Bar) 테스트 패턴을 생성하는 노드입니다. SMPTE, EBU, ARIB 표준을 비롯하여 PLUGE, 존 플레이트(Zone Plate), 크로마 램프(Chroma Ramp) 등 40가지 이상의 다양한 테스트 패턴 생성을 지원합니다.

## 언제 사용하나요?

다음과 같은 상황에서 이 노드를 사용합니다:

- 비디오 디스플레이 및 모니터 캘리브레이션
- 색상 정확도 및 밝기 레벨 테스트
- 비디오 프로덕션 워크플로우용 테스트 패턴 생성
- 디스플레이 설정 및 색 공간(Color Space) 처리 검증
- 컬러 그레이딩용 레퍼런스 이미지 생성
- 비디오 인코딩 및 디코딩 파이프라인 테스트
- 디스플레이 품질 평가 패턴 생성

## 사용 방법

### 기본 설정

1. 워크플로우에 CreateColorBars 노드를 추가합니다.
1. "bar_type" 드롭다운에서 생성할 컬러 바 유형을 선택합니다.
1. 테스트 패턴의 너비(width)와 높이(height)를 설정합니다 (기본값: 1920x1080).
1. 생성된 컬러 바 이미지가 "image" 출력을 통해 제공됩니다.

### 파라미터 (Parameters)

#### 주요 설정

- **bar_type**: 생성할 컬러 바 유형 (문자열, 기본값: "SMPTE 219-100 Bars")

    선택 가능한 주요 옵션:

    - **SMPTE 패턴**: SMPTE 219-100 Bars, SMPTE 75% Bars, SMPTE Bars, SMPTE 219+i Bars
    - **풀 필드(Full Field) 패턴**: 100% Full Field Bars, 75% Full Field Bars, 75% Bars Over Red
    - **국제 표준**: EBU Bars (유럽), ARIB 28-100, ARIB 28-75, ARIB 28+i (일본)
    - **HD 패턴**: HD Color Bars
    - **단색(Solid Color)**: Full Field White, Blue, Cyan, Green, Magenta, Red, Yellow
    - **테스트 패턴**: Zone Plate, Tartan Bars, Multi Burst, Bowtie
    - **그레이스케일 램프**: Stair 5 Step, Stair 5 Step Vert, Stair 10 Step, Stair 10 Step Vert
    - **그라데이션 패턴**: Y Ramp Up, Y Ramp Down, Vertical Ramp
    - **크로마 패턴**: Legal Chroma Ramp, Full Chroma Ramp, Chroma Ramp
    - **캘리브레이션 패턴**: Pluge (고급 옵션 포함), Pathological EG, Pathological PLL
    - **특수 패턴**: AV Delay Pattern 1, AV Delay Pattern 2, Bouncing Box

- **width**: 픽셀 단위의 컬러 바 이미지 너비 (정수, 기본값: 1920)

- **height**: 픽셀 단위의 컬러 바 이미지 높이 (정수, 기본값: 1080)

#### PLUGE 전용 파라미터

bar_type으로 "Pluge"를 선택했을 때만 표시되는 파라미터입니다:

- **pluge_ire_setup**: PLUGE 패턴 캘리브레이션을 위한 IRE 설정 유형 (문자열, 기본값: "NTSC 7.5 IRE")

    - **NTSC 7.5 IRE**: 표준 NTSC 셋업 레벨 (기본값)
    - **PAL 0 IRE**: 0 IRE 블랙 레벨을 사용하는 PAL 표준
    - **RGB Full Range**: RGB 풀 레인지(0-255) 캘리브레이션

- **pluge_bar_count**: 표시할 PLUGE 바 개수 (정수, 기본값: 3, 범위: 2-5)

    - 2개: 블랙 레벨 및 블랙 바로 위 레벨
    - 3개: 슈퍼 블랙(Super Black), 블랙 레벨, 블랙 바로 위 레벨 (표준)
    - 4-5개: 블랙 레벨 사이의 중간 값 포함

- **pluge_orientation**: PLUGE 바의 방향 (문자열, 기본값: "vertical")

    - **vertical**: 세로 방향 배치 (기본값)
    - **horizontal**: 가로 방향 배치

### 출력 (Outputs)

- **image**: ImageUrlArtifact 형식의 생성된 컬러 바 이미지

## 예시

### 기본 컬러 바 생성

1. 워크플로우에 CreateColorBars 노드를 추가합니다.
1. bar_type 드롭다운에서 "SMPTE 75% Bars"를 선택합니다.
1. width를 1920으로, height를 1080으로 설정합니다.
1. 결과를 확인하기 위해 "image" 출력을 DisplayImage 노드에 연결합니다.
1. (선택 사항) 테스트 패턴을 저장하려면 SaveImage 노드에 연결합니다.

### PLUGE 캘리브레이션 패턴

1. CreateColorBars 노드를 추가합니다.
1. bar_type 드롭다운에서 "Pluge"를 선택합니다.
1. PLUGE 전용 파라미터가 자동으로 나타납니다.
1. 캘리브레이션 환경을 설정합니다:
    - 비디오 표준에 맞게 pluge_ire_setup 설정 (예: NTSC는 "NTSC 7.5 IRE", PAL은 "PAL 0 IRE")
    - 표준 캘리브레이션을 위해 pluge_bar_count를 3으로 설정
    - 수직(vertical) 또는 수평(horizontal) 방향 선택
1. 출력을 DisplayImage에 연결하여 캘리브레이션 패턴을 확인합니다.

### 커스텀 해상도 테스트 패턴

1. CreateColorBars 노드를 추가합니다.
1. 원하는 패턴 유형(예: "HD Color Bars")을 선택합니다.
1. 사용자 정의 해상도를 지정합니다:
    - width: 3840 (4K 너비)
    - height: 2160 (4K 높이)
1. 지정한 해상도에 맞춰 패턴이 생성됩니다.

## 중요 참고 사항

- **실시간 미리보기**: bar_type, width, height 또는 PLUGE 파라미터를 변경하면 이미지가 실시간으로 재생성됩니다.
- **SMPTE 표준**: SMPTE 75% Bars 및 SMPTE Bars는 SDTV용 SMPTE ECR 1-1978 표준에 따라 75% 강도 레벨을 사용합니다.
- **IRE 단위**: PLUGE 패턴은 정밀한 블랙 레벨 캘리브레이션을 위해 IRE(Institute of Radio Engineers) 단위를 사용합니다.
- **무손실 PNG 포맷**: 생성된 모든 이미지는 무손실 품질을 유지하기 위해 PNG 파일로 저장됩니다.

## 주요 활용 사례

### 비디오 프로덕션

- 방송 표준 준수를 위한 SMPTE 컬러 바 생성
- 컬러 그레이딩 워크플로우용 레퍼런스 패턴 생성
- 표준 테스트 패턴을 이용한 비디오 인코딩 파이프라인 검증

### 디스플레이 캘리브레이션

- PLUGE 패턴을 활용한 블랙 레벨 및 밝기 캘리브레이션
- 디스플레이 선명도 및 초점 테스트를 위한 존 플레이트(Zone Plate) 생성
- 감마 커브 검증을 위한 그레이스케일 램프 생성

### 품질 테스트

- 비디오 처리 장비 테스트를 위한 패솔로지컬(Pathological) 패턴 생성
- 색 공간 처리 확인을 위한 크로마 램프 생성
- 주파수 응답 특성 테스트를 위한 멀티 버스트(Multi-burst) 패턴 생성

## 기술 세부 정보

- **이미지 형식**: PNG (무손실)
- **색 공간**: RGB
- **기본 해상도**: 1920x1080 (Full HD)
- **지원 표준**: SMPTE, EBU, ARIB
- **IRE 변환 공식**: RGB = (IRE / 100) * 255

이 노드는 전문 비디오 제작, 방송 및 디스플레이 캘리브레이션 워크플로우에 적합한 업계 표준 테스트 패턴을 생성합니다.
