# 앱 자동 업데이트 비활성화

관리자는 머신 수준의 `policy.json` 파일을 사용하여 Griptape Nodes Desktop의 자동 업데이트를 비활성화할 수 있습니다. 각 설치 환경이 자체적으로 업데이트되도록 두는 대신 조직에서 검증된 앱 버전을 중앙 배포할 때 이 기능을 사용합니다.

이 기능은 Griptape Nodes Desktop 0.24.0 이상이 필요합니다. 이전 버전에서는 해당 파일이 무시됩니다.

## 정책 파일 생성

다음 내용으로 `policy.json` 파일을 생성합니다:

```json
{ "disableUpdates": true }
```

머신의 운영체제에 맞는 경로에 파일을 배치합니다:

| 플랫폼 | 경로 |
| --- | --- |
| macOS | `/Library/Application Support/ai.griptape.nodes.desktop/policy.json` |
| Windows | `%ProgramData%\GriptapeNodes\policy.json` |
| Linux | `/etc/griptape-nodes-desktop/policy.json` |

앱은 해당 디렉토리나 파일을 자동으로 생성하지 않습니다. 사용자가 앱을 처음 실행하기 전에 수동으로 생성하거나 디바이스 관리(MDM) 시스템을 통해 배포하세요. 앱이 이미 실행 중인 경우 먼저 종료해야 합니다.

앱은 시작할 때 정책을 한 번 읽습니다. 파일 변경 사항은 앱을 다음 번에 시작할 때 적용됩니다.

## 사용자가 보게 되는 화면

![업데이트 확인 버튼, 업데이트 동작 메뉴, 릴리즈 채널 메뉴 위에 "Updates are disabled by your organization" 메시지가 표시된 앱 설정의 업데이트 섹션](../assets/img/enterprise/disabling_app_updates-updates_disabled.png)

App Settings(앱 설정)에 **Updates are disabled by your organization** 메시지가 표시됩니다. 다음 컨트롤이 비활성화됩니다:

- **Check for Updates**
- **Update Behavior**
- **Release Channel**

**Show release notes after updates** 옵션은 계속 사용할 수 있으며 관리자가 새 버전을 설치한 후 릴리즈 노트를 표시합니다.

사용자가 macOS의 앱 메뉴 또는 Windows 및 Linux의 **Help** 메뉴에서 **Check for Updates…**를 선택하면 앱에 **Updates are managed by your organization**이 표시됩니다.

정책이 활성화된 동안 앱은 자동 또는 사용자 시작 업데이트 확인을 실행하지 않으며 업데이트 피드로 요청을 보내지 않습니다.

## 앱 업데이트 설치 방법

내장 업데이터가 비활성화된 상태에서는 승인된 설치 프로그램을 관리 대상 머신에 배포하세요. 정책 파일은 앱 설치 경로 외부에 있으므로 기존 버전 위에 새 버전을 설치해도 삭제되지 않습니다. 설치 후에도 업데이트 비활성화 상태가 유지됩니다.

사용자는 앱 내부에서 노드 라이브러리 및 모델을 계속 설치하고 업데이트할 수 있습니다. 각 앱 버전은 해당 버전에 번들로 제공된 엔진을 계속 사용합니다.

## 앱 업데이트 다시 활성화

`policy.json`을 삭제하거나 `disableUpdates` 값을 `false`로 변경한 후 앱을 다시 시작합니다. 업데이트 섹션은 머신의 이전 **Update Behavior** 및 **Release Channel** 설정을 사용합니다.

리터럴 `true` 값만 업데이트를 비활성화합니다. 파일이 없거나 유효하지 않은 JSON이거나 다른 값이 입력되면 업데이트가 사용자 제어 상태로 유지됩니다. 비관리형 머신에는 정책 파일이 필요하지 않습니다.

## 관련 문서

- [앱 설정: 업데이트](../guides/desktop/app_settings.md#updates): 업데이트 동작 및 릴리즈 채널 안내
- [Admin Server](admin_server.md): 관리 대상 머신을 공용 인터넷으로부터 분리 유지하기
