# 에이전트 안내서 (For Agents)

이 페이지는 [docs.griptapenodes.com](https://docs.griptapenodes.com/)이 AI 코딩 에이전트, MCP 스킬, 그리고 엔진의 실제 API를 기반으로 작업하려는 모든 도구를 위해 제공하는 머신 판독 가능(machine-readable) 인터페이스를 설명합니다.

이 사이트를 렌더링하는 동일한 파일들이 후처리된 마크다운(post-processed markdown) 형태로도 게시되어 에이전트가 직접 가져올 수 있습니다. 스니펫, 매크로, mkdocstrings 출력물이 이미 확장되어 있어 `raw.githubusercontent.com`에서는 제공되지 않는 완전한 형태를 제공합니다.

## 인터페이스 구성

- [`/llms.txt`](https://docs.griptapenodes.com/en/stable/llms.txt)는 [llms.txt 규약](https://llmstxt.org/)에 따른 선별된 색인 파일입니다. 주요 페이지(스크립팅, 프로젝트 시스템, 커스텀 노드 개발, MCP 연동, 노드 레퍼런스)를 짧은 설명과 절대 URL이 포함된 명명된 섹션별로 그룹화합니다.
- [`/llms-full.txt`](https://docs.griptapenodes.com/en/stable/llms-full.txt)는 내비게이션에 포함된 모든 페이지의 후처리된 마크다운을 하나로 연결한 전체 문서입니다. 전체 엔진 문서를 단일 컨텍스트 윈도우에 포함하고자 할 때 사용하세요.
- 모든 문서 페이지는 HTML 렌더링 외에도 독립형 마크다운 파일로 제공됩니다. 해당 URL은 렌더링된 페이지 URL 끝에 `/index.md`가 추가된 형태입니다. 예를 들어 [`/development/custom_nodes/parameters/index.md`](https://docs.griptapenodes.com/en/stable/development/custom_nodes/parameters/index.md) 및 [`/retained_mode/index.md`](https://docs.griptapenodes.com/en/stable/development/retained_mode/index.md)와 같습니다. 최상위 페이지는 `/<page>/index.md`에 위치하며, 중첩된 하위 페이지는 렌더링된 URL 구조를 그대로 반영합니다.

## 상황별 사용 가이드

- 전체 코퍼스를 가져오지 않고 어떤 내용이 있는지 탐색하려면 **`/llms.txt`**를 사용하세요. 훑어보기에 충분히 작으며, 섹션 설명을 통해 어떤 페이지가 어떤 주제를 다루는지 알 수 있습니다.
- 단일 프롬프트 그라운딩 문서가 필요하고 컨텍스트 예산이 충분한 경우 **`/llms-full.txt`**를 사용하세요. 이는 페이지별 마크다운 파일과 동일한 내용이며, llms.txt 섹션에 선언된 순서대로 연결되어 있습니다.
- 필요한 페이지를 이미 알고 있는 경우(예: 커스텀 노드를 작성 중이며 파라미터 레퍼런스가 필요한 경우, 또는 스크립팅 중이며 `retained_mode.md`가 필요한 경우) **개별 페이지별 `.md`**를 사용하세요.

## 엔진 그라운딩을 위한 핵심 페이지

에이전트가 초기 부트스트래핑을 위해 소수의 페이지만 참조하도록 하려는 경우, 다음 5개 페이지가 엔진의 핵심 1차(first-party) API 영역 대부분을 포괄합니다:

- [스크립팅 (retained mode)](https://docs.griptapenodes.com/en/stable/development/retained_mode/index.md)
- [커스텀 노드 개발 개요](https://docs.griptapenodes.com/en/stable/development/custom_nodes/index.md)
- [노드 개발 시작하기](https://docs.griptapenodes.com/en/stable/development/custom_nodes/getting_started/index.md)
- [프로젝트 시스템 개요](https://docs.griptapenodes.com/en/stable/guides/projects/index.md)
- [MCP 연동 개요](https://docs.griptapenodes.com/en/stable/guides/mcp/index.md)

## 안정성

- 인터페이스는 기존 문서 배포 파이프라인을 통해 `main` 브랜치를 추적합니다. 하나의 라이브 인터페이스가 유지되며, 버전별 URL(예: `/v0.40/llms.txt`)은 현재 게시되지 않습니다.
- 페이지 세트는 `mkdocs.yml`의 `llmstxt` 플러그인 블록에 의해 관리됩니다. 새 문서 페이지를 추가한다고 해서 `/llms.txt`나 `/llms-full.txt`에 자동으로 포함되지는 않으며, 해당 섹션 아래에 수동으로 추가되어야 합니다. `nodes/` 아래의 레퍼런스 페이지는 glob을 통해 자동으로 반영됩니다.
