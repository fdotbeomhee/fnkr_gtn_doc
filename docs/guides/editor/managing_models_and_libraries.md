# 모델 및 라이브러리 관리 (Managing Models and Libraries)

일부 노드는 AI 모델을 로컬에서 실행하므로 실행 전에 디스크에 모델 파일이 있어야 합니다. 또 어떤 노드는 아직 설치하지 않은 라이브러리에서 제공됩니다. 두 가지 모두 에디터의 **Manage** 메뉴에서 **Model Management** 및 **Library Management** 창을 통해 관리됩니다. 이 페이지에서는 두 기능을 모두 설명합니다.

<!-- screenshot (#5166): the Manage menu open, showing Model Management and Library Management items -->

라이브러리의 기본 개념(설치 격리 방식, Shared vs. Isolated 실행 모드, 문제 발생 시 대처법)은 [라이브러리](../libraries.md)를 참조하세요. 이 페이지에서는 두 관리 창 자체에 대해 중점적으로 다룹니다.

## 모델 관리 (Model Management)

모델을 검색, 다운로드 및 정리하려면 **Manage → Model Management**를 엽니다.

이 창에서 수행하는 모든 작업은 터미널에서 `gtn models` 명령어를 통해서도 사용할 수 있습니다(검색, 다운로드, 목록 조회, 삭제, 다운로드 상태 확인). 자세한 내용은 [CLI 참조](../../reference/command_line_interface.md#models)를 확인하세요.

<!-- screenshot (#5166): the Model Management window with the search box, filter chips, and a couple of installed models listed -->

### 모델 검색

검색 상자에 입력하면 입력하는 동안 Hugging Face에서 일치하는 모델을 검색합니다. 일치하는 결과가 상자 아래의 드롭다운에 나타나며 각 모델의 ID, 설명(있는 경우), 다운로드 수, 좋아요 수 및 몇 가지 태그가 표시됩니다. 이미 설치된 모델은 **Installed**로 표시됩니다. 결과를 클릭하여 선택하면 엔진이 확인한 모델 크기와 Hugging Face에서 모델 페이지를 여는 링크가 표시됩니다.

정확한 모델 ID를 이미 알고 있다면 드롭다운에서 검색하여 선택하는 대신 직접 입력할 수도 있습니다.

### 모델 다운로드

모델을 선택한 상태에서 **Download**를 클릭합니다. 다운로드는 백그라운드에서 시작되므로 계속 작업하거나 창을 닫거나 Library Management로 전환하더라도 다운로드가 계속 진행됩니다.

### 진행 상황 추적

**Downloads** 섹션에는 활성 상태이거나 최근에 완료된 모든 다운로드가 상태 배지와 함께 나열됩니다:

| 상태 | 의미 |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Downloading** | 진행 중입니다. 진행률 표시줄과 전체 크기 중 완료된 GB 용량 및 백분율이 표시됩니다. |
| **Completed** | 완료되었습니다. 진행률 표시줄이 녹색으로 채워집니다. |
| **Failed** | 다운로드가 완료되지 않았습니다. 오류 메시지가 항목 아래에 표시됩니다(원인이 게이트 모델인 경우 Hugging Face 접근 요청 링크 포함 — 아래 [게이트 모델은 토큰이 필요함](#게이트-모델은-토큰이-필요함) 참조). |

목록 위의 **All / Downloads / Models** 칩을 사용하여 활성 다운로드만 표시하거나 설치된 모델만 표시하도록 필터링할 수 있습니다.

### 다운로드 취소

다운로드 중인 항목의 휴지통 아이콘을 클릭하여 취소합니다. 먼저 **Cancel Download** 확인 창이 나타납니다. **Keep Downloading**을 누르면 취소하지 않고 계속 다운로드하며, **Cancel Download**를 누르면 다운로드를 중지하고 부분 다운로드 기록을 삭제합니다.

<!-- screenshot (#5166): the Cancel Download confirmation dialog -->

### 모델 삭제

**Installed Models** 섹션에는 로컬 경로 및 크기와 함께 이미 다운로드한 모든 항목이 나열됩니다. 항목을 제거하려면 휴지통 아이콘을 클릭합니다. 먼저 **Delete Model** 대화 상자가 나타나 확인합니다. 삭제는 영구적이며 로컬 저장소에서 파일이 제거되므로 해당 모델에 의존하는 모든 워크플로우는 모델을 다시 다운로드하거나 다른 모델을 지정할 때까지 실행에 실패합니다.

<!-- screenshot (#5166): the Delete Model confirmation dialog -->

### 게이트 모델은 토큰이 필요함

Hugging Face의 일부 모델은 게이트(gated)되어 있습니다. 모델 페이지에서 접근 권한을 요청해야 하며, 다운로드하기 전에 Griptape Nodes에 Hugging Face 액세스 토큰을 구성해야 합니다. 토큰이 구성되어 있지 않으면 Model Management 창 상단에 해결 방법을 설명하는 배너가 표시됩니다. Hugging Face에서 토큰을 생성한 다음 **Settings → API Keys & Secrets**에서 `HF_TOKEN`으로 추가하세요. 배너의 링크를 클릭하면 해당 설정 위치로 바로 이동합니다.

특정 게이트 모델에 대한 접근 권한을 요청하는 방법을 포함한 전체 단계는 [Hugging Face를 사용하는 노드 설정](../integrations/hugging_face.md)을 참조하세요.

## 라이브러리 관리 (Library Management)

라이브러리를 설치, 업데이트 및 제거하려면 **Manage → Library Management**를 엽니다. 터미널에서는 `gtn libraries download <git-url>` 및 `gtn libraries sync` 명령어로 설치 및 업데이트를 수행할 수 있습니다([CLI 참조](../../reference/command_line_interface.md#libraries) 확인).

<!-- screenshot (#5166): the Library Management window with the filter bar and a few installed libraries listed -->

상단의 필터 표시줄을 사용하면 설치된 라이브러리를 이름으로 검색하고 **All / Updates / Errors** 칩을 통해 목록을 좁힐 수 있습니다. **Errors** 칩은 로드에 실패한 라이브러리를 찾는 가장 빠른 방법입니다. 칩 옆에 있는 두 개의 아이콘 버튼을 통해 **모든 라이브러리의 업데이트 확인**을 실행하거나 목록을 **새로고침**할 수 있습니다.

라이브러리 행을 클릭하여 확장하면 설명, Git 원격 저장소 및 ref(Git으로 설치된 라이브러리의 경우), 엔진이 보고한 로드 관련 문제를 확인할 수 있습니다.

### 라이브러리 설치

**Add Library**를 클릭하여 **Add Library** 대화 상자를 엽니다. 커뮤니티 라이브러리를 호스팅하는 GitHub 저장소 등의 Git URL을 붙여넣고 **Install**을 클릭합니다.

<!-- screenshot (#5166): the Add Library dialog with a Git URL entered and Advanced Options expanded -->

에디터는 실제로 복제하기 전에 먼저 저장소를 검사하고 라이브러리의 이름, 설명, 버전 및 노드 수가 포함된 확인 화면을 표시하므로 원하는 라이브러리가 아닌 경우 취소할 수 있습니다.

**Advanced Options**(URL 필드 아래의 확장 메뉴)를 통해 다음을 설정할 수 있습니다:

- **Branch / Tag / Commit** — 저장소의 기본 브랜치 대신 특정 ref를 설치합니다.
- **Download Directory** — 기본 라이브러리 디렉터리가 아닌 다른 위치로 라이브러리를 복제하려는 경우 설정합니다. 폴더 아이콘을 사용하여 디렉터리를 찾을 수 있습니다.

무엇을 설치해야 할지 모르겠다면 폼 아래의 **Browse Community Libraries**를 클릭하세요. 추천 라이브러리 디렉터리가 열리며 URL을 복사하여 위 필드에 붙여넣을 수 있습니다.

대상 디렉터리에 동일한 라이브러리의 이전 설치본이나 관련 없는 파일이 이미 있는 경우 덮어쓸지 묻는 확인 메시지가 표시됩니다. 덮어쓰기는 기존 항목(Git 체크아웃의 커밋되지 않은 변경 사항 포함)을 삭제하고 새 설치로 교체하는 파괴적 작업입니다. 로컬 수정 사항이 있는 라이브러리를 업데이트할 때도 동일한 덮어쓰기 확인 메시지가 표시됩니다.

### 라이브러리 업데이트

Git 원격 저장소가 있는 라이브러리를 확장하고 **Check for Updates**를 클릭하여 원격 저장소와 비교합니다. 업데이트가 있는 경우 해당 행에 **Update** 버튼(엔진이 대상 버전을 알고 있는 경우 **Update to 1.4.0**과 같이 표시됨)이 나타납니다. 버튼을 클릭하여 업데이트를 적용하세요. 방금 릴리스된 불안정한 버전을 바로 가져오지 않도록 최소 유지 시간이 지나야 업데이트 자격이 주어지므로, 업데이트가 보류 중인 경우 Update 버튼 대신 적격 시점까지 남은 시간이 표시됩니다.

확장된 행에서 라이브러리를 다른 브랜치, 태그 또는 커밋으로 직접 전환할 수도 있습니다. 현재 ref를 클릭하여 직접 수정한 다음 확인하세요.

라이브러리의 종속성이 자동으로 설치되지 않은 경우, 확장된 행의 **Install Dependencies**를 클릭하면 라이브러리를 다시 복제하지 않고 종속성 설치 단계만 다시 시도합니다.

### 라이브러리 제거

Library Management 창 자체는 디스크에서 라이브러리 파일을 삭제하지 않습니다. 라이브러리를 완전히 삭제하거나, 제거하지 않고 비활성화하거나, Shared 또는 Isolated 실행 모드를 설정하는 방법에 대해서는 라이브러리 가이드의 [라이브러리 토글 및 제거](../libraries.md#라이브러리-토글-및-제거)를 참조하세요.
