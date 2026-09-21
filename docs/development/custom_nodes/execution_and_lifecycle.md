# 실행 및 수명 주기 (Execution and Lifecycle)

노드의 수명 주기(Lifecycle), 상태 전이, 비동기 실행 및 콜백 메서드에 대한 상세 가이드입니다.

## 노드 수명 주기 단계

1. **초기화 (`__init__`)**: 노드 인스턴스가 생성되고 파라미터가 정의됩니다.
2. **사전 실행 검증 (`before_process`)**: 입력 파라미터의 유효성을 검사합니다.
3. **실행 (`process` 또는 `aprocess`)**: 노드의 핵심 연산이 수행됩니다.
4. **사후 처리 (`after_process`)**: 출력 파라미터에 결과가 설정되고 상태가 `RESOLVED`로 전이됩니다.

## 동기 실행 vs 비동기 실행

- **동기 실행 (`process`)**: 단순한 데이터 변환, 로컬 연산 등 즉각적인 작업에 적합합니다.
- **비동기 실행 (`async def aprocess`)**: 원격 API 호출, 모델 인퍼런스, 네트워크 요청, 파일 다운로드 등 대기 시간이 발생하는 작업에 필수적입니다.

```python
from griptape_nodes.exe_types.node_types import ControlNode


class AsyncFetchNode(ControlNode):
    async def aprocess(self) -> None:
        url = self.get_parameter_value("url")
        # 비동기 HTTP 요청 처리
        data = await self.fetch_data(url)
        self.parameter_output_values["response"] = data
```

## 연결 및 상태 변경 콜백

- `on_connection_added(self, param_name, connection)`: 새 연결이 추가될 때 호출
- `on_connection_removed(self, param_name, connection)`: 연결이 제거될 때 호출
- `on_parameter_value_changed(self, param_name, old_val, new_val)`: 속성 값이 변경될 때 호출
