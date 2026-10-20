# Hugging Face를 사용하는 노드 설정 (Setup for Nodes that use Hugging Face) {: #setup-for-nodes-that-use-hugging-face }

## 계정 및 토큰 생성 (Account and Token Creation) {: #account-and-token-creation }

이 가이드에서는 Hugging Face 계정을 설정하고 액세스 토큰을 생성하며 Griptape Nodes와 함께 사용하는 데 필요한 모델을 설치하는 과정을 안내합니다.

!!! info

    이미 계정이 있는 경우 [2단계](#create-an-access-token)로 바로 이동하세요.

### 1. Hugging Face에서 새 계정 생성

1. [https://huggingface.co/](https://huggingface.co/) 로 이동합니다.
1. 우측 상단의 **Sign Up**을 클릭합니다.
1. 로봇이 아님을 증명하는 인증 단계를 완료합니다.

<p align="center">
    <img src="../assets/huggingface_00_MainPage.png" alt="HF Site" width="500"/>
</p>

<p align="center">
    <img src="../assets/huggingface_01_signup.png" alt="Signup" width="300"/>
</p>

### 2. 액세스 토큰 생성 (Create an Access Token) {: #create-an-access-token }

1. 계정 설정에 접근합니다.

1. Hugging Face 계정에 로그인합니다.

1. 우측 상단의 프로필 아이콘을 클릭합니다.

1. 드롭다운 메뉴에서 **Settings**를 선택합니다(또는 [Settings](https://huggingface.co/settings/profile/)로 직접 이동).

    <p align="center">
    <img src="../assets/huggingface_02_Settings.png" alt="Settings" width="500"/>
    </p>

!!! warning "이메일 인증 필수"

    토큰 생성 중 문제가 발생하면 이메일 주소를 인증했는지 확인하세요. 계속 진행하기 전에 인증 절차를 완료해야 합니다.

1. 설정 메뉴에서 **Access Tokens**로 이동합니다.

<p align="center">
  <img src="../assets/huggingface_03_AccessTokens.png" alt="Access Tokens" width="500"/>
</p>

1. 우측 상단 영역에서 **Create new token**을 클릭합니다.

1. 필요한 접근 권한 유형으로 **Read**를 선택합니다.

<p align="center">
  <img src="../assets/huggingface_04_TokenRead.png" alt="Token Read" width="500"/>
</p>

1. 토큰에 설명이 포함된 이름(예: GriptapeNodes)을 지정합니다.
1. **Create Token**을 클릭합니다. 새 토큰이 포함된 창이 나타납니다. 표시된 메시지를 주의 깊게 확인하세요. 이 키를 확인하거나 복사할 수 있는 유일한 기회입니다.

<p align="center">
  <img src="../assets/huggingface_05_SaveToken.png" alt="Save Token" width="400"/>
</p>

1. 토큰을 복사하여 안전하게 보관합니다.
1. **Done**을 클릭하여 토큰 창을 닫습니다.

!!! danger "보안 유의사항"

    토큰을 쉽게 찾을 수 있으면서도 안전하게 유지할 수 있도록 비밀번호 관리자나 보안 메모 앱에 저장하는 것이 좋습니다.

    액세스 토큰은 Hugging Face 서비스에 대한 요청에 사용되는 개인 식별자입니다. 절대 타인과 공유하지 마시고 스크린샷, 비디오 또는 화면 공유 세션 중에 노출되지 않도록 주의하세요.

    신용카드 번호처럼 안전하게 취급하세요.

## 필수 파일 설치 (Install Required Files) {: #install-required-files }

이제 계정에 연결된 토큰이 있으므로 명령줄에서 Hugging Face와 상호 작용할 수 있는 Hugging Face CLI(명령줄 인터페이스)를 설치할 수 있습니다.

### 1. Hugging Face CLI 설치

터미널을 열고 다음 명령을 실행합니다:

```bash
pip install -U "huggingface_hub[cli]"
```

자세한 내용은 [공식 CLI 문서](https://huggingface.co/docs/huggingface_hub/main/en/guides/cli)를 참조하세요.

### 2. 토큰으로 로그인

터미널에서 앞서 생성한 액세스 토큰으로 인증합니다:

```bash
huggingface-cli login
```

토큰을 입력하라는 메시지가 표시됩니다.

### 3. 필요에 따라 모델 설치 (Install Models as Required) {: #install-models-as-required }

각 Hugging Face 노드는 특정 모델과 함께 작동하도록 설계되었습니다. 일부 노드는 하나의 모델에서만 작동하지만 다른 노드는 여러 옵션과 호환됩니다. 아래 표는 각 노드에서 사용할 수 있는 모델을 보여줍니다:

| 노드 (Node) | 호환 모델 (Compatible Model(s)) |
| ----------- | ------------------------------- |
| SPAN Upscale | 4x-ClearRealityV1.pth |
| Flux | FLUX.1-dev, FLUX.1-schnell |
| Flux Post Upscale | FLUX.1-dev, FLUX.1-schnell |

## 모델 설치 고려 사항 (Model Installation Considerations) {: #model-installation-considerations }

모델 설치에는 두 가지 옵션이 있습니다. 선택은 특정 워크플로우 요구 사항, 사용 가능한 디스크 공간 및 인터넷 연결 속도에 따라 달라집니다.

1. **선택적 설치 (Selective Installation)**: 사용할 계획인 노드에 필요한 특정 모델만 설치합니다.

    - **장점**: 다운로드 시간 및 디스크 공간 사용량 절약.
    - **단점**: 추가 모델을 설치할 때까지 기능이 제한됨.

1. **전체 설치 (Complete Installation)**: 사용 가능한 모든 모델을 다운로드합니다.

    - **장점**: 추가 다운로드 없이 모든 노드 기능을 완전하게 사용 가능.
    - **단점**: 초기 다운로드 시간이 더 길고 더 많은 디스크 공간이 필요함.

!!! note "다운로드 시간"

    이러한 모델 다운로드는 용량이 상당히 크며 인터넷 연결 속도에 따라 완료하는 데 30분에서 몇 시간까지 걸릴 수 있습니다. 다운로드가 진행되는 동안 마지막 단계로 이동할 수 있습니다.

!!! info "다운로드 위치"

    모델은 Hugging Face Hub 캐시 디렉터리에 다운로드됩니다. 위치를 확인하려면 huggingface-cli를 사용하여 HF_HUB_CACHE를 찾을 수 있습니다.

    터미널에서 다음 명령을 실행하면 여러 항목이 포함된 목록이 출력됩니다:

    ```
    huggingface-cli env
    ```

    `- HF_HUB_CACHE:`로 시작하는 항목을 찾으세요:

    ```
    - HF_HUB_CACHE: /Users/jason/.cache/huggingface/hub
    ```

#### SPAN Upscale 용

```bash
huggingface-cli download skbhadra/ClearRealityV1 4x-ClearRealityV1.pth
```

#### Flux 및 Flux Post Upscale 용

```bash
huggingface-cli download black-forest-labs/FLUX.1-schnell
```

#### FLUX.1-dev 용

```bash
huggingface-cli download black-forest-labs/FLUX.1-dev
```

!!! warning "다운로드 오류"

    다운로드 중에 다음과 같이 시작되는 오류가 발생할 수 있습니다:

    ```
    Cannot access gated repo for url https://huggingface.co...
    ```

    이러한 오류의 끝부분에는 접근 권한을 요청하는 안내 링크가 포함되어 있습니다.

    해당 지침을 따르세요:

    [https://huggingface.co/black-forest-labs/FLUX.1-schnell](https://huggingface.co/black-forest-labs/FLUX.1-schnell) 을 방문하여 접근 권한을 요청합니다. 요청이 성공하면 접근 권한이 부여되었다는 메시지가 표시되며 다시 다운로드를 시도할 수 있습니다.

    <p align="center">
      <img src="../assets/huggingface_06_gated_model.png" alt="Gated model" width="350"/>
    </p>

## Griptape Nodes 설정에 토큰 추가 (Add Your Token to Griptape Nodes settings) {: #add-your-token-to-griptape-nodes-settings }

!!! info "개요"

    이제 Hugging Face 계정을 설정하고 필요한 모델을 설치했으므로 토큰을 사용하도록 Griptape Nodes를 구성해야 합니다. 이 과정은 간단합니다.

### 1. Griptape Nodes 설정 메뉴 열기

1. Griptape Nodes를 실행합니다.
1. 상단 메뉴 바(File 및 Edit 바로 오른쪽)에 있는 **Settings** 메뉴를 찾습니다.
1. **Settings**를 클릭하여 구성 옵션을 엽니다.

<p align="center">
  <img src="../assets/huggingface_07_GN_Settings.png" alt="Settings Menu" width="500"/>
</p>

### 2. API Keys & Secrets에 Hugging Face 토큰 추가

1. 환경설정 에디터(Configuration Editor)의 왼쪽 하단에서 **API Keys and Secrets**를 찾습니다.
1. 클릭하여 이 섹션을 펼칩니다.
1. 아래로 스크롤하여 **HUGGINGFACE_HUB_ACCESS_TOKEN** 필드를 찾습니다.
1. 앞서 생성한 토큰을 이 필드에 붙여넣습니다.
1. 환경설정 에디터를 닫으면 설정이 자동으로 저장됩니다.

<p align="center">
  <img src="../assets/huggingface_08_GN_HFToken.png" alt="Token Configuration" width="500"/>
</p>

!!! success "설정 완료"

    이 단계를 완료하면 Griptape Nodes에서 Hugging Face 노드를 사용할 준비가 완료됩니다!

## 모델 다운로드 제어하기 (Controlling Model Downloads) {: #controlling-model-downloads }

팀 단위로 Griptape Nodes를 관리한다면, 엔진과 모델 라이브러리가 Hugging Face에서 자동으로 다운로드하도록 두는 대신 사용할 수 있는 모델을 직접 정할 수 있습니다. Griptape Nodes는 Hugging Face 자체 패키지인 `huggingface_hub`로 모델을 다운로드하고 찾으며, 이 패키지의 설정은 바꾸지 않습니다. 따라서 표준 Hugging Face 환경 변수로 동작을 제어할 수 있습니다. 변수는 [Hugging Face 환경 변수 문서](https://huggingface.co/docs/huggingface_hub/package_reference/environment_variables)에 설명되어 있습니다. 이 섹션은 이 변수를 설정했을 때 Griptape Nodes가 어떻게 동작하는지 설명합니다.

### 변수를 설정하는 위치

다음 두 위치 중 한 곳에 설정한 후 Griptape Nodes를 다시 시작하세요:

- Griptape Nodes를 실행하는 환경(셸 프로필, 서비스 정의, 컨테이너 등). 여기서 설정한 값이 우선합니다.
- Griptape Nodes 설정 디렉터리(보통 `~/.config/griptape_nodes/.env`)에 있는 `.env` 파일. [설정](../configuration.md)을 참조하세요.

엔진, 워커 프로세스, 모델 다운로드 프로세스는 모두 같은 값을 사용합니다.

### 다운로드 차단하기

[`HF_HUB_OFFLINE=1`](https://huggingface.co/docs/huggingface_hub/package_reference/environment_variables#hfhuboffline)을 설정하면 Hugging Face에 대한 모든 요청이 중단됩니다. 캐시에 이미 있는 모델은 계속 작동합니다. 오프라인 모드가 켜지면 다음과 같이 동작합니다:

- **Model Management**에서 캐시에 없는 모델을 다운로드하면 "Cannot find an appropriate cached snapshot folder for the specified revision on the local disk and outgoing traffic has been disabled."라는 메시지와 함께 실패합니다. 이미 캐시된 모델을 다운로드하면 온라인 연결 없이 성공합니다. 설정의 `models_to_download`에 나열된 모델도 같으며, 엔진은 시작할 때 이를 다운로드합니다.
- **Model Management**에서 검색하면 실패합니다. 검색은 항상 Hugging Face에 요청하기 때문입니다.
- 노드의 모델 드롭다운에는 캐시에 있는 모델이 나열됩니다. 노드가 지원하지만 캐시에 없는 모델은 "Not downloaded"로 표시되며 실행할 수 없습니다.
- 대부분의 모델 노드는 캐시에서만 로드하지만, 일부 노드는 실행할 때 Hugging Face에서 추가 파일을 가져옵니다. 오프라인 모드는 이것도 차단하므로, 이러한 노드에 필요한 모든 파일이 캐시에 있는지 확인하세요.

### 공유 또는 관리되는 캐시 사용하기

[`HF_HOME`](https://huggingface.co/docs/huggingface_hub/package_reference/environment_variables#hfhome) 또는 [`HF_HUB_CACHE`](https://huggingface.co/docs/huggingface_hub/package_reference/environment_variables#hfhubcache)를 설정하면 네트워크 공유처럼 직접 관리하는 캐시를 Griptape Nodes가 사용하도록 지정할 수 있습니다. **Model Management**는 그 캐시의 모델을 나열하고 삭제하며, 모델 드롭다운도 그 캐시에서 읽습니다. 캐시 구조는 [캐시 관리](https://huggingface.co/docs/huggingface_hub/guides/manage-cache)에서 확인할 수 있습니다.

### 모델 미리 다운로드하기

`hf download`로 캐시를 미리 채울 수 있습니다. 방법은 [Hub에서 파일 다운로드](https://huggingface.co/docs/huggingface_hub/guides/download)와 [CLI 가이드](https://huggingface.co/docs/huggingface_hub/guides/cli)에 설명되어 있습니다. 다음 사항에 유의하세요:

- Griptape Nodes가 사용하는 것과 같은 `HF_HOME` 또는 `HF_HUB_CACHE`로 실행하세요. 그렇지 않으면 모델이 다른 캐시에 저장됩니다.
- 저장소 전체를 다운로드하세요. 노드는 캐시에 있는 파일만 로드하므로, 일부 파일만 다운로드하면 드롭다운에는 표시되지만 로드에 실패하는 모델이 생길 수 있습니다.

### 내부 미러 사용하기

`huggingface_hub`는 `HF_ENDPOINT`도 읽습니다. 이 값은 다운로드와 검색을 수행하는 주소입니다. 이를 내부 Hugging Face Hub 미러 주소로 설정하면 자체 서버에서 모델을 제공할 수 있습니다.

### 로컬 모델 파일 사용하기

Modular Diffusion 라이브러리의 **Load Pipeline Component** 노드는 단일 체크포인트 파일이나 로컬 diffusers 형식 폴더에서 컴포넌트를 로드합니다. 따라서 Hugging Face 캐시에 없는 모델 파일도 사용할 수 있습니다.
