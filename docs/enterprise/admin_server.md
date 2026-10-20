# Admin Server (관리 서버)

Admin Server는 사내 네트워크 안에서 실행되며 Griptape Nodes 데스크톱 애플리케이션 인스턴스를 대신하여 Griptape Cloud 요청을 전달합니다. 애플리케이션 인스턴스는 `cloud.griptape.ai` 대신 Admin Server를 가리킵니다. 허용된 대상과 클라이언트 프록시 설정을 구성하면 스토리지 및 모델 다운로드와 같은 기타 HTTPS 트래픽도 터널링할 수 있습니다. 방화벽 제어와 함께 사용하면 개별 애플리케이션 인스턴스를 공용 인터넷에서 분리할 수 있습니다.

## 주요 기능 및 역할

- **단일 아웃바운드 게이트웨이**: 모든 워크스테이션이 사내 Admin Server로 통신하며 Admin Server만 `cloud.griptape.ai`로 HTTPS 트래픽을 송신합니다.
- **경로 필터링 (Forwarding Rules)**: 라이선스 관리(`sessions`, `users`, `organizations`)는 허용하고 모델 프록시나 외부 API 호출 등 불필요한 클라우드 경로는 차단하도록 설정할 수 있습니다.
- **HTTPS 터널링**: 허용된 대상으로 가는 HTTPS 연결을 터널링합니다.
- **무상태(Stateless) 중계**: 라이선스를 직접 검증하거나 토큰을 캐시하지 않으며 워크스테이션의 인증 정보를 Griptape Cloud에 투명하게 전달합니다.

## 구성 (Configuration) {: #configuration }

Admin Server는 설정 파일(`config.yaml`) 또는 환경 변수를 통해 구성됩니다. 환경 변수가 설정 파일보다 항상 높은 우선순위를 갖습니다.

### 환경 변수

| 환경 변수 | 필수 여부 | 설명 | 기본값 |
| --- | --- | --- | --- |
| `GT_CLOUD_API_KEY` | **필수** | 운영자의 Griptape Cloud API 키. 서버 시작 시 소유권을 검증함. | — |
| `SERVER_LISTEN_PORT` | 선택 | Admin Server가 수신 대기할 포트 | `8080` |
| `SERVER_READ_TIMEOUT` | 선택 | 클라이언트 요청 읽기 타임아웃 | `300s` |
| `SERVER_WRITE_TIMEOUT` | 선택 | 클라이언트 응답 쓰기 타임아웃 (스트리밍 모델 호출 지원) | `300s` |
| `UPSTREAM_URL` | 선택 | 업스트림 Griptape Cloud URL | `https://cloud.griptape.ai` |
| `LOG_LEVEL` | 선택 | 로깅 레벨 (`debug`, `info`, `warn`, `error`) | `info` |
| `LOG_FORMAT` | 선택 | 로그 출력 포맷 (`json`, `text`) | `text` |
| `TUNNEL_ALLOWED_HOSTS` | 선택 | 허용된 터널 대상 목록 (쉼표로 구분, 포트 선택 가능) | 비어 있음 |
| `TUNNEL_IDLE_TIMEOUT` | 선택 | 터널 비활성 타임아웃. `0`이면 비활성화 | `5m` |

### 설정 파일 (`config.yaml`) 예시

```yaml
server:
  listen_address: "0.0.0.0"
  listen_port: 8080
  read_timeout: 300s
  write_timeout: 300s

upstream:
  url: "https://cloud.griptape.ai"

logging:
  level: "info"
  format: "text"

forwarding:
  rules:
    - path: "/api/sessions/*"
      allow: true
    - path: "/api/session-renew"
      allow: true
    - path: "/api/session-release"
      allow: true
    - path: "/api/users"
      allow: true
    - path: "/api/organizations"
      allow: true
    - path: "/*"
      allow: false

tunnel:
  allowed_hosts: []  # 비어 있으면 HTTPS 터널을 비활성화합니다.
  idle_timeout: "5m"
```

## 전송 규칙 (Forwarding Rules) {: #forwarding-rules }

`forwarding` 블록을 사용하면 사내 네트워크 밖으로 나갈 수 있는 Cloud API 경로를 제어할 수 있습니다.

- 규칙은 위에서 아래로 순서대로 평가되며 가장 먼저 일치하는 규칙이 적용됩니다.
- 필수 라이선스 경로(`/api/sessions/*`, `/api/session-renew/*`, `/api/session-release/*`, `/api/users/*`, `/api/organizations/*`)를 차단하면 Admin Server가 부팅 시 오류를 발생시키고 실행을 거부합니다. 각 경로는 `/*`로 끝나는 접두사 규칙으로 작성해야 하며, `"/api/users"`나 `"/api/users/"`처럼 정확히 일치하는 규칙은 인정되지 않습니다.
- 차단된 경로에 대한 요청은 `403 Forbidden` (`{"error": "path not permitted"}`) 응답을 반환합니다.
- 전송 규칙은 Cloud 리버스 프록시 요청에만 적용되며 HTTPS 터널에는 적용되지 않습니다. 터널 대상은 [`tunnel.allowed_hosts`](#tunnel)로 제어합니다.

## 터널 (tunnel) {: #tunnel }

`tunnel` 블록은 Cloud 요청과 동일한 리스너 및 포트에서 HTTPS `CONNECT` 요청을 제어합니다. `allowed_hosts` 목록이 비어 있으면 터널이 비활성화됩니다. 설정은 시작 시 읽히므로 변경한 후에는 Admin Server를 다시 시작하세요.

| 키 | 기본값 | 설명 |
| --- | --- | --- |
| `allowed_hosts` | _(비어 있음)_ | 허용된 대상 호스트 이름. 각 항목에 선택적으로 포트를 지정할 수 있습니다. 포트가 없는 항목은 `443` 포트만 허용합니다. |
| `idle_timeout` | `5m` | 양쪽 모두 일정 시간 동안 데이터를 보내지 않으면 터널을 닫습니다. `0`이면 비활성화됩니다. 터널의 전체 지속 시간에는 제한이 없습니다. |

`allowed_hosts`에는 URL이나 경로 규칙이 아니라 호스트 이름을 입력하세요.

| 사용하지 마세요 | 대신 사용 | 허용되는 대상 |
| --- | --- | --- |
| `"https://github.com"` | `"github.com"` | `github.com:443`만 허용됩니다. URL 스킴은 제외하세요. |
| `"github.com/downloads/*"` | `"github.com"` | `github.com:443`의 모든 경로가 허용됩니다. 다운로드 경로만 허용되지 않습니다. 경로 제한은 지원되지 않습니다. |
| `"https://example.com:8443"` | `"example.com:8443"` | `8443` 포트의 `example.com`만 허용됩니다. 포트는 유지하고 URL 스킴은 제외하세요. |
| `*.hf.co` (따옴표 없는 YAML) | `"*.hf.co"` | `hf.co`의 모든 하위 도메인(중첩된 하위 도메인 포함)이 허용되지만 `hf.co` 도메인 자체는 제외됩니다. YAML에서 와일드카드 항목은 따옴표로 감싸야 합니다. |

앞쪽 `*.` 와일드카드만 지원합니다. 잘못된 항목이 있으면 서버가 시작되지 않습니다. TLS는 종단 간(end-to-end)으로 유지되므로 Admin Server는 터널 내부의 경로를 제한하거나 콘텐츠를 검사할 수 없습니다.

!!! warning "리스너 접근 제한"

    Admin Server에 대한 접근을 신뢰할 수 있는 네트워크 클라이언트로 제한하세요. Admin Server에 접근할 수 있는 클라이언트는 구성된 대상으로 터널을 열 수 있습니다.

    와일드카드 도메인은 해당 DNS 운영자를 신뢰하는 경우에만 허용하세요. Admin Server는 허용된 호스트 이름이 확인되는 모든 주소로 연결하므로 호스트 이름 허용 목록은 IP 주소 제한이 아닙니다.

## 상태 확인 (Health Check) {: #health-check }

서버의 가동 상태를 확인하기 위한 헬스체크 엔드포인트가 제공됩니다:

```bash
curl http://localhost:8080/health
```

정상 상태 응답:
```json
{"status": "ok"}
```

## 기타 HTTPS 트래픽 라우팅 (Routing other HTTPS traffic) {: #routing-other-https-traffic }

Admin Server는 라이브러리 설치, 모델 다운로드, 에셋 전송 및 워크플로우에 필요한 기타 서비스의 HTTPS 연결을 터널링할 수 있습니다. 이 기능에는 Admin Server 0.4.0 이상이 필요합니다. Cloud 엔드포인트만 설정해서는 이 트래픽이 라우팅되지 않습니다.

서버의 `config.yaml`에 대상을 추가한 후 서버를 다시 시작하세요. 다음은 일반적인 Griptape Nodes 작업에 권장되는 시작 구성입니다:

```yaml
tunnel:
  allowed_hosts:
    - "gtcazureservicesaprod.blob.core.windows.net"  # Griptape Cloud 에셋 업로드 및 다운로드
    - "github.com"                                 # 노드 라이브러리용 GitHub 리포지토리
    - "pypi.org"                                   # 라이브러리 종속성용 Python 패키지 인덱스
    - "files.pythonhosted.org"                     # PyPI에서 다운로드되는 Python 패키지 파일
    - "huggingface.co"                             # Hugging Face 모델 리포지토리
    - "*.hf.co"                                    # 모델 파일을 제공하는 Hugging Face 하위 도메인
  idle_timeout: "5m"
```

배포 환경에 필요하지 않은 대상은 제거하세요. 리디렉션과 워크플로우에서 사용하는 공급자에 추가 호스트가 필요할 수 있습니다. 허용할 대상만 추가하세요.

### 데스크톱 애플리케이션

데스크톱 HTTPS 라우팅의 사전 요구 사항과 단계는 [Admin Server를 통해 HTTPS 트래픽 라우팅하기](using_the_admin_server.md#route-https-traffic-through-the-admin-server)를 참조하세요.

### 독립 실행형 엔진

데스크톱 앱 외부에서 실행되는 엔진의 경우 엔진의 **실행 환경**에 다음 변수를 설정하세요. `admin-server`를 실제 Admin Server 호스트 이름으로 바꾸세요:

```bash
export GT_CLOUD_BASE_URL=http://admin-server:8080
export HTTPS_PROXY=http://admin-server:8080
export NO_PROXY=localhost,127.0.0.1,admin-server
```

`GT_CLOUD_BASE_URL`은 엔진의 Cloud API 엔드포인트를 설정합니다. `HTTPS_PROXY`는 이를 지원하는 클라이언트가 다른 HTTPS 요청을 Admin Server를 통해 터널링하도록 지정합니다. `NO_PROXY`는 로컬 요청과 Admin Server 요청이 터널을 거치지 않도록 합니다.

이 환경에서 엔진을 실행하거나 서비스 또는 컨테이너 구성에 변수를 설정하세요. 엔진과 다운로드를 수행하는 모든 프로세스가 이 변수를 상속받아야 합니다. 모든 클라이언트가 프록시 환경 변수를 사용하는 것은 아니므로 워크플로우에서 사용하는 클라이언트를 확인하세요.

`HTTP_PROXY`를 Admin Server로 설정하지 **마세요**. Admin Server는 일반 HTTP 포워드 프록시 트래픽을 지원하지 않으며, 이러한 요청은 의도한 대상이 아닌 구성된 Cloud 업스트림으로 전달됩니다.

### 관리되는 데스크톱 설정

`GTN_DESKTOP_SERVER_URL` 및 `GTN_DESKTOP_ROUTE_TRAFFIC_THROUGH_SERVER`를 사용하면 저장된 데스크톱 설정을 재정의할 수 있습니다. 값과 실행 방법은 [데스크톱 환경 변수](../guides/desktop/environment_variables.md)를 참조하세요.

이 설정은 방화벽 적용을 대신하지 않습니다. 프록시 설정을 무시하는 클라이언트의 직접 인터넷 접근을 차단하려면 방화벽 규칙을 사용하세요.

## 문제 해결 (Troubleshooting) {: #troubleshooting }

- **서버가 부팅되지 않고 즉시 종료됨**: `GT_CLOUD_API_KEY`가 올바르게 설정되었는지, 사내 방화벽에서 `https://cloud.griptape.ai`로의 아웃바운드 HTTPS(443 포트) 연결이 허용되어 있는지 확인하세요.
- **클라이언트에서 502 Bad Gateway 발생**: Admin Server와 Griptape Cloud 간의 네트워크 연결이 일시적으로 끊겼거나 업스트림 서버 응답 지연일 수 있습니다. Admin Server 로그를 확인하세요.
- **스트리밍 응답이 30초 후 끊김**: 구버전 Admin Server의 경우 `read_timeout` 및 `write_timeout`이 `30s`로 설정되어 있을 수 있습니다. `300s` 이상으로 설정을 업데이트하세요.
- **403 path not permitted 오류**: 워크스테이션이 요청한 기능의 API 경로가 `forwarding.rules`에서 허용되지 않았습니다. 해당 기능 사용을 허용하려면 `config.yaml`의 규칙을 수정하세요.
- **`403 {"error":"tunnel host not permitted"}`**: 터널 거부 로그의 `target`을 `tunnel.allowed_hosts`와 포트까지 포함하여 비교하세요. 빈 목록은 터널을 비활성화하고, 와일드카드는 기본 도메인을 포함하지 않습니다. 리디렉션으로 다른 호스트에 연결될 수도 있습니다. 허용 목록을 변경한 후에는 다시 시작하고 `TUNNEL_ALLOWED_HOSTS` 재정의가 있는지 확인하세요.
- **`502 {"error":"tunnel host unreachable"}`**: 대상은 허용되었지만 Admin Server가 연결할 수 없습니다. 로그에 표시된 호스트와 포트에 대한 Admin Server의 DNS 및 아웃바운드 방화벽 접근을 확인하세요.
- **일정 시간 동안 활동이 없는 후 HTTPS 터널이 닫힘**: `tunnel.idle_timeout`의 기본값은 `5m`입니다. 클라이언트에 더 긴 일시 중지가 필요하면 이 값을 늘리거나 `"0"`으로 설정하여 유휴 상태에 따른 종료를 비활성화하세요. 이 타임아웃은 활성 다운로드의 지속 시간을 제한하지 않습니다.

## 관련 문서

- [온프레미스 빠른 시작](on_premises_quick_start.md): 단계별 구축 가이드
- [Admin Server 사용하기](using_the_admin_server.md): 워크스테이션 클라이언트 활성화 방법
- [Admin Dashboard](admin_dashboard.md): 라이선스 키 및 권한 관리
- [아키텍처: 온프레미스 구성](../architecture.md#on-premises-configuration): 시스템 네트워크 구조
