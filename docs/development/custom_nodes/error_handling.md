# 모범 사례 및 오류 처리 (Best Practices and Error Handling)

커스텀 노드를 개발할 때 예외를 안전하게 처리하고 사용자 친화적인 피드백을 제공하는 모범 사례입니다.

## 오류 처리 원칙

1. **명확한 사용자 메시지**: 내부 스택 트레이스 대신 문제의 원인과 해결 방법을 설명하는 메시지를 제공하세요.
2. **상태 복구**: 오류 발생 시 노드가 `UNRESOLVED` 또는 명확한 에러 상태로 전이되도록 처리합니다.
3. **유효성 검사**: `process()` 실행 전 필수 파라미터 유효성을 먼저 검사합니다.

## 예제: 안전한 API 호출 및 예외 처리

```python
from griptape_nodes.exe_types.core_types import Parameter
from griptape_nodes.exe_types.node_types import ControlNode


class SafeApiNode(ControlNode):
    def process(self) -> None:
        api_key = self.get_parameter_value("api_key")
        if not api_key:
            raise ValueError("API Key가 설정되지 않았습니다. 노드 설정을 확인하세요.")

        try:
            # API 호출 로직
            response = self.call_remote_api(api_key)
            self.parameter_output_values["result"] = response
        except ConnectionError as e:
            self.logger.error(f"네트워크 연결 실패: {e}")
            raise RuntimeError(f"원격 서버에 연결할 수 없습니다: {e}") from e
        except Exception as e:
            self.logger.error(f"예상치 못한 오류: {e}")
            raise
```

## 비밀 정보 (Secrets) 다루기

API 키나 토큰 같은 민감한 정보는 워크플로우 파일에 평문으로 저장되지 않도록 `.env` 환경 변수 또는 시크릿 매니저를 통해 관리합니다.
