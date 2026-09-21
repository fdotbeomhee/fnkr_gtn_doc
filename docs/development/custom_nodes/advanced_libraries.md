# 고급 라이브러리 (Advanced Libraries)

단순한 노드 선언을 넘어 엔진 수명 주기 훅, 동적 노드 등록, 커스텀 API 요청 핸들러를 구현하는 `AdvancedNodeLibrary` 가이드입니다.

## AdvancedNodeLibrary 기본 구조

```python
from griptape_nodes.exe_types.library_types import AdvancedNodeLibrary


class MyAdvancedLibrary(AdvancedNodeLibrary):
    def on_library_registered(self) -> None:
        # 라이브러리가 엔진에 로드될 때 실행되는 초기화 훅
        self.logger.info("고급 라이브러리가 초기화되었습니다.")

    def on_library_unregistered(self) -> None:
        # 라이브러리가 언로드될 때 실행되는 정리 훅
        pass
```

## 동적 노드 등록

런타임에 외부 플러그인이나 모델 목록을 스캔하여 매니페스트에 기재되지 않은 노드 타입을 동적으로 등록할 수 있습니다.
