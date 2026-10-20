# 데스크톱 환경 변수 (Desktop Environment Variables) {: #desktop-environment-variables }

이 변수를 사용하여 Griptape Nodes Desktop을 시작할 때 구성합니다.

## 설정 위치 (Where to set them) {: #where-to-set-them }

데스크톱 앱이 시작되기 전에 실행 셸, 관리형 런처 또는 사용자 세션 환경을 통해 필요한 변수를 설정하세요. 데스크톱 전용 변수는 엔진의 `.env` 파일에 넣지 마세요.

macOS에서 Finder 또는 Dock으로 연 앱은 셸 export가 아니라 `launchd` 환경을 상속합니다. 변수를 제공하려면 관리형 런처 또는 LaunchAgent를 사용하세요.

변수를 추가, 변경 또는 제거한 후에는 앱을 완전히 종료한 다음 다시 실행하세요. 엔진만 재시작하는 것으로는 충분하지 않습니다.

## `GTN_DESKTOP_SERVER_URL`

`http` 또는 `https` origin(예: `http://admin-server:8080`)으로 저장된 서버 엔드포인트를 재정의합니다. 경로는 모두 제거됩니다. 유효한 값은 저장된 설정보다 우선합니다. 비어 있거나 잘못된 값은 무시되며, 잘못된 값은 로그에 기록됩니다.

## `GTN_DESKTOP_ROUTE_TRAFFIC_THROUGH_SERVER`

저장된 HTTPS 라우팅 설정을 재정의합니다. 라우팅을 활성화하려면 `1` 또는 `true`를, 비활성화하려면 `0` 또는 `false`를 사용하세요(대소문자 구분 없음). 비어 있거나 잘못된 값은 무시됩니다. 라우팅은 기본적으로 꺼져 있습니다.

라우팅은 라이선스 자격 증명이 있고 기본값이 아닌 서버 엔드포인트가 있는 경우에만 적용됩니다. 허용된 대상이 구성된 Admin Server 0.4.0 이상이 필요합니다. [Admin Server를 통해 HTTPS 트래픽 라우팅하기](../../enterprise/using_the_admin_server.md#route-https-traffic-through-the-admin-server)를 참조하세요.

두 서버 재정의 중 하나를 제거하면 다음 실행 시 해당 사용자 저장 설정이 복원됩니다.

## `GTN_DESKTOP_DISABLE_UPDATES`

`1` 또는 `true`(대소문자 구분 없음)로 설정하면 해당 실행에서 자동 및 수동 앱 업데이트 확인이 모두 비활성화됩니다. 다른 값은 업데이트를 비활성화하지 않습니다. `policy.json`에 `disableUpdates: true`가 있으면 `0` 또는 `false`로 설정해도 업데이트를 다시 활성화할 수 없습니다.

정책을 강제하려면 [머신 수준 업데이트 정책](../../enterprise/disabling_app_updates.md)을 사용하세요. 사용자가 실행 환경을 제어하므로 환경 재정의는 강제력이 없습니다.

## `GRIPTAPE_NODES_LICENSE`

해당 실행의 라이선스 토큰을 제공하며, 저장된 활성 라이선스보다 우선합니다. 비어 있지 않은 값이 있으면 토큰이 잘못되었더라도 앱에서 활성 자격 증명을 전환할 수 없습니다. 잘못된 토큰은 저장된 라이선스로 대체되지 않습니다. 비어 있는 값은 무시됩니다.

토큰은 저장된 라이선스나 엔진의 `.env` 파일에 기록되지 않습니다. 변수를 제거하면 다음 실행 시 저장된 라이선스 선택이 복원됩니다. 토큰을 비밀번호처럼 취급하고 공유 스크립트나 버전 관리에 넣지 마세요.
