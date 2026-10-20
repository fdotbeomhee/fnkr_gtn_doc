# 워커를 통한 노드 격리 (Node Isolation with Workers) {: #node-isolation-with-workers }

특정 라이브러리는 무거운 의존성(예: PyTorch, CUDA, Diffusers)을 가지거나 서로 충돌하는 Python 패키지 버전을 요구할 수 있습니다. Griptape Nodes는 워커 서브프로세스를 통해 라이브러리를 격리 실행하는 기능을 지원합니다.

## 동작 방식

- 각 격리된 라이브러리는 자체 독립적인 Python 가상 환경(venv)에서 실행되는 별도의 워커 프로세스를 갖습니다.
- 메인 엔진과 워커 프로세스는 IPC(프로세스 간 통신)를 통해 데이터를 교환합니다.
- 한 라이브러리의 충돌이나 크래시가 메인 엔진 전체에 영향을 주지 않습니다.

## 실행 의존성 선언 방법 (How to declare execution dependencies) {: #how-to-declare-execution-dependencies }

두 의존성 집합은 모두 `griptape-nodes-library.json`의 `metadata.dependencies` 아래에 있습니다.

- **`pip_dependencies`**: 노드 모듈을 가져오고 노드를 생성하는 데 필요한 의존성입니다. 라이브러리 venv(`<library>/.venv`)에 설치되며 오케스트레이터가 사용합니다.
- **`pip_dependencies_exec`**: `process`에만 필요한 의존성입니다. `<library>/.venv-exec`에 설치되며, 워커에서만 `sys.path`에 포함됩니다.

```json
{
    "name": "My Library",
    "library_schema_version": "0.14.0",
    "metadata": {
        "author": "<Your Name>",
        "description": "<Description>",
        "library_version": "0.1.0",
        "engine_version": "0.85.0",
        "tags": ["AI", "Custom"],
        "dependencies": {
            "pip_dependencies": [
                "pillow==11.0.0"
            ],
            "pip_dependencies_exec": [
                "torch==2.4.1",
                "transformers==4.45.2"
            ],
            "pip_install_flags": [
                "--extra-index-url",
                "https://download.pytorch.org/whl/cu121"
            ]
        }
    },
    "categories": [],
    "nodes": []
}
```
