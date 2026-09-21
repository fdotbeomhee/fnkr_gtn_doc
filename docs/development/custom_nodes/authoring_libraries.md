# 라이브러리 작성 가이드 (Authoring Libraries)

재사용 가능한 노드 모음을 독립된 패키지로 배포하기 위한 라이브러리 작성 표준 가이드입니다.

## 라이브러리 디렉토리 구조

```text
my_nodes_library/
├── griptape_nodes_library.json   # 라이브러리 메니페스트
├── pyproject.toml                # 패키지 빌드 및 의존성 정의
├── README.md
└── src/
    └── my_nodes_library/
        ├── __init__.py
        ├── nodes/
        │   ├── text_nodes.py
        │   └── image_nodes.py
        └── utils/
```

## 라이브러리 매니페스트 (`griptape_nodes_library.json`)

```json
{
  "name": "My Custom Nodes Library",
  "author": "My Studio",
  "version": "1.0.0",
  "description": "스튜디오 전용 커스텀 AI 노드 모음",
  "node_types": [
    {
      "class_name": "MyCustomNode",
      "module_path": "my_nodes_library.nodes.text_nodes",
      "category": "Custom/Text"
    }
  ]
}
```
