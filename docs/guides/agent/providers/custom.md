# Custom (OpenAI 호환)

이 제공자 유형을 사용하면 타사 서비스, 자체 호스팅 모델 서버, 사내 프록시 등 OpenAI Chat Completions API를 구현한 모든 엔드포인트에 Griptape Nodes를 연결할 수 있습니다.

## 사전 요구 사항

다음 정보가 필요합니다:

- 엔드포인트의 **기본 URL(Base URL)** (예: `https://api.openai.com/v1`)
- 해당 엔드포인트의 **API 키(API key)**
- 사용할 **모델 이름(Model name)** (예: `google/gemma-4-e4b`) — 사용자 지정 엔드포인트는 자동 모델 검색을 지원하지 않으므로 필수입니다.

## 제공자 추가하기

1. **Settings → Agent Settings**를 엽니다.
1. **+ Add Provider**를 클릭합니다.
1. **Custom (OpenAI-compatible)**을 선택합니다.

<!-- TODO(#5095): screenshot of the "Add Provider — Configure Custom (OpenAI-compatible)" step -->

구성 항목을 입력합니다:

- **Name** — 모델 드롭다운에서 이 제공자를 식별할 레이블

- **Icon** — 이 제공자를 나타낼 아이콘 선택 (선택 사항)

- **Base URL** — 엔드포인트의 루트 URL

- **API Key Secret** — 기존 시크릿을 선택하거나 **+**를 클릭하여 새로 생성합니다:

    <!-- TODO(#5095): screenshot of the "Create Secret" modal -->

    - **Secret Name** — 환경 변수 이름 (예: `OPENAI_API_KEY`)
    - **Value** — 실제 API 키 값

    저장 후 드롭다운에서 새 시크릿을 선택합니다.

- **Model** — 이 엔드포인트와 함께 사용할 모델 이름을 입력합니다.

**Create Provider**를 클릭합니다.

## 테스트

생성 후 마법사에 확인 화면이 표시됩니다. 응답이 정상적으로 오는지 확인한 후 **Done**을 클릭하세요.

<!-- TODO(#5095): screenshot of the "Provider Added" confirmation step -->

## 관련 문서

- [AI 제공자 개요](./index.md)
- [Ollama](./ollama.md) — 로컬 제공자, API 키 불필요
- [LM Studio](./lm_studio.md) — 로컬 제공자, API 키 불필요
