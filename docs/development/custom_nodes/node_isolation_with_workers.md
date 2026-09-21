# 워커를 통한 노드 격리 (Node Isolation with Workers)

특정 라이브러리는 무거운 의존성(예: PyTorch, CUDA, Diffusers)을 가지거나 서로 충돌하는 Python 패키지 버전을 요구할 수 있습니다. Griptape Nodes는 워커 서브프로세스를 통해 라이브러리를 격리 실행하는 기능을 지원합니다.

## 동작 방식

- 각 격리된 라이브러리는 자체 독립적인 Python 가상 환경(venv)에서 실행되는 별도의 워커 프로세스를 갖습니다.
- 메인 엔진과 워커 프로세스는 IPC(프로세스 간 통신)를 통해 데이터를 교환합니다.
- 한 라이브러리의 충돌이나 크래시가 메인 엔진 전체에 영향을 주지 않습니다.

## 라이브러리 매니페스트에서 워커 격리 설정

`griptape_nodes_library.json`에 `isolated: true`를 지정합니다:

```json
{
  "name": "My Heavy Library",
  "version": "1.0.0",
  "isolated": true,
  "python_version": "3.12",
  "dependencies": [
    "torch>=2.2.0",
    "diffusers>=0.27.0"
  ]
}
```
