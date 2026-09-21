# Admin Server (관리 서버)

Admin Server는 Griptape Nodes 데스크톱 애플리케이션과 Griptape Cloud 사이에서 동작하는 온프레미스 프록시 서비스입니다. 사내 네트워크 내에서 실행되어 단일 송신(egress) 지점을 제공하므로 각 워크스테이션을 인터넷에 직접 노출하지 않고도 방화벽을 통해 하나의 호스트만 허용하여 라이선스 검증 및 승인된 클라우드 기능을 이용할 수 있습니다.

## 주요 기능 및 역할

- **단일 아웃바운드 게이트웨이**: 모든 워크스테이션이 사내 Admin Server로 통신하며 Admin Server만 `cloud.griptape.ai`로 HTTPS 트래픽을 송신합니다.
- **경로 필터링 (Forwarding Rules)**: 라이선스 관리(`sessions`, `users`, `organizations`)는 허용하고 모델 프록시나 외부 API 호출 등 불필요한 클라우드 경로는 차단하도록 설정할 수 있습니다.
- **무상태(Stateless) 중계**: 라이선스를 직접 검증하거나 토큰을 캐시하지 않으며 워크스테이션의 인증 정보를 Griptape Cloud에 투명하게 전달합니다.

## 구성 (Configuration)

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
```

## 전송 규칙 (Forwarding Rules)

`forwarding` 블록을 사용하면 사내 네트워크 밖으로 나갈 수 있는 Cloud API 경로를 제어할 수 있습니다.

- 규칙은 위에서 아래로 순서대로 평가되며 가장 먼저 일치하는 규칙이 적용됩니다.
- 필수 라이선스 경로(`/api/sessions/*`, `/api/session-renew`, `/api/session-release`, `/api/users`, `/api/organizations`)를 차단하면 Admin Server가 부팅 시 오류를 발생시키고 실행을 거부합니다.
- 차단된 경로에 대한 요청은 `403 Forbidden` (`{"error": "path not permitted"}`) 응답을 반환합니다.

## 상태 확인 (Health Check)

서버의 가동 상태를 확인하기 위한 헬스체크 엔드포인트가 제공됩니다:

```bash
curl http://localhost:8080/health
```

정상 상태 응답:
```json
{"status": "ok"}
```

## 문제 해결 (Troubleshooting)

- **서버가 부팅되지 않고 즉시 종료됨**: `GT_CLOUD_API_KEY`가 올바르게 설정되었는지, 사내 방화벽에서 `https://cloud.griptape.ai`로의 아웃바운드 HTTPS(443 포트) 연결이 허용되어 있는지 확인하세요.
- **클라이언트에서 502 Bad Gateway 발생**: Admin Server와 Griptape Cloud 간의 네트워크 연결이 일시적으로 끊겼거나 업스트림 서버 응답 지연일 수 있습니다. Admin Server 로그를 확인하세요.
- **스트리밍 응답이 30초 후 끊김**: 구버전 Admin Server의 경우 `read_timeout` 및 `write_timeout`이 `30s`로 설정되어 있을 수 있습니다. `300s` 이상으로 설정을 업데이트하세요.
- **403 path not permitted 오류**: 워크스테이션이 요청한 기능의 API 경로가 `forwarding.rules`에서 허용되지 않았습니다. 해당 기능 사용을 허용하려면 `config.yaml`의 규칙을 수정하세요.

## 관련 문서

- [온프레미스 빠른 시작](on_premises_quick_start.md): 단계별 구축 가이드
- [Admin Server 사용하기](using_the_admin_server.md): 워크스테이션 클라이언트 활성화 방법
- [Admin Dashboard](admin_dashboard.md): 라이선스 키 및 권한 관리
- [아키텍처: 온프레미스 구성](../architecture.md#온프레미스-구성): 시스템 네트워크 구조
