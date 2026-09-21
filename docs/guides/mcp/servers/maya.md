# Maya MCP 서버

**[Maya MCP Server](https://github.com/PatrickPalmer/MayaMCP)**는 AI 에이전트가 MCP(Model Context Protocol)를 통해 자연어로 Autodesk Maya와 직접 상호작용하고 제어할 수 있도록 해줍니다. 이 통합을 통해 프롬프트 기반 3D 모델링, 씬(Scene) 생성 및 조작이 가능합니다. 이 서버는 [Patrick Palmer](https://github.com/PatrickPalmer)가 제작한 서드파티 서버이며, Autodesk Maya 또는 Griptape에서 공식 제작한 것이 아닙니다.

!!! warning "서드파티 서버"

    이 MCP 서버는 Griptape 또는 Autodesk Maya에서 공식적으로 지원하지 않습니다. 사용자의 재량에 따라 사용하시고 작업 내용을 반드시 백업해 두세요.

!!! note "사전 요구 사항"

    Maya MCP 서버를 사용하기 전에 다음 사항을 준비해야 합니다:

    1. [Autodesk Maya](https://www.autodesk.com/products/maya) 2023 이상 설치
    1. Python 3.10 이상 설치
    1. [저장소](https://github.com/PatrickPalmer/MayaMCP)에서 Maya MCP 서버 다운로드 및 설정. 자세한 정보는 [설치 가이드](#1-maya-mcp-서버-다운로드-및-설정)를 참조하세요.

## 설치

### 1. Maya MCP 서버 다운로드 및 설정

1. 터미널을 열고 머신에서 저장소를 다운로드할 위치로 이동합니다. 쉽게 찾을 수 있는 위치에 서버를 두는 것을 권장합니다. 예를 들어 Mac에서는 GitHub 저장소를 보관하는 위치인 `$HOME/Documents/GitHub`를 사용할 수 있습니다.

    ```bash
    cd $HOME/Documents/GitHub
    ```

1. **저장소를 복제(Clone)합니다**:

    ```bash
    git clone https://github.com/PatrickPalmer/MayaMCP.git
    cd MayaMCP
    ```

1. **가상 환경을 생성합니다**:

    ```bash
    python -m venv .venv
    ```

1. **가상 환경을 활성화합니다**:

    - **Windows**: `.venv\Scripts\activate.bat`
    - **Mac/Linux**: `source .venv/bin/activate`

1. **의존성 패키지를 설치합니다**:

    ```bash
    pip install -r requirements.txt
    ```

### 2. Maya 실행 및 Command Port 활성화

1. **Autodesk Maya를 엽니다.**

1. **Command Port 활성화** - MCP 서버가 Maya와 통신하려면 이 작업이 필요합니다. Maya의 Script Editor에서 다음 Python 코드를 실행합니다:

    ```python
    import maya.cmds as cmds


    def setup_maya_command_port(port=50007):
        """Setup Maya command port with error handling"""
        try:
            # First, try to close any existing command port on this port
            try:
                cmds.commandPort(name=f"localhost:{port}", close=True)
                print(f"Closed existing command port on localhost:{port}")
            except:
                # No existing port to close, that's fine
                pass

            # Enable the command port
            cmds.commandPort(name=f"localhost:{port}")
            print(f"Command Port successfully enabled on localhost:{port}")
            return True

        except Exception as e:
            print(f"Error setting up command port: {e}")
            return False


    # Run the setup
    if setup_maya_command_port(50007):
        print("Maya MCP server should now be able to connect!")
    else:
        print("Failed to setup command port. Check Maya's Command Port settings in Preferences.")
    ```

!!! warning "세션마다 Command Port 활성화 필요"

    Maya를 시작할 때마다 Command Port를 활성화해야 합니다. Command Port 설정은 Maya 세션 간에 유지되지 않습니다.

!!! tip "더 쉽게 사용하기: Maya 스크립트로 저장"

    이 과정을 더 간편하게 만들려면 Command Port 설정을 Maya 스크립트로 저장할 수 있습니다:

    1. **다음 스크립트를 `enable_mcp_command_port.py`로 저장합니다**:

        ```python
        import maya.cmds as cmds


        def enable_mcp_command_port(port=50007):
            """Setup Maya command port with error handling"""
            try:
                # First, try to close any existing command port on this port
                try:
                    cmds.commandPort(name=f"localhost:{port}", close=True)
                    print(f"Closed existing command port on localhost:{port}")
                except:
                    # No existing port to close, that's fine
                    pass

                # Enable the command port
                cmds.commandPort(name=f"localhost:{port}")
                print(f"Command Port successfully enabled on localhost:{port}")
                return True

            except Exception as e:
                print(f"Error setting up command port: {e}")
                return False
        ```

    1. Maya의 Script Editor에서 **스크립트를 테스트합니다**:

        ```python
        import enable_mcp_command_port

        enable_mcp_command_port.enable_mcp_command_port()
        ```

    1. **다음 옵션 중 하나를 선택합니다**:

        **옵션 A: 셸프(Shelf) 버튼 생성**

        - 2단계의 테스트 코드를 셸프로 드래그하여 버튼을 생성합니다.
        - Command Port를 활성화해야 할 때마다 해당 버튼을 클릭합니다.

        **옵션 B: userSetup.py로 자동 시작**

        - Maya의 userScripts 디렉터리를 찾습니다:
            - **Windows**: `%USERPROFILE%\Documents\maya\2025\scripts\`
            - **macOS**: `~/Library/Preferences/Autodesk/maya/2025/scripts/`
            - **Linux**: `~/maya/2025/scripts/`
        - 기존 `userSetup.py` 파일(없으면 새로 생성)에 다음 줄을 추가합니다:
            ```python
            import enable_mcp_command_port

            enable_mcp_command_port.enable_mcp_command_port()
            ```
        - Maya를 재시작하면 Command Port가 자동으로 활성화됩니다.

!!! tip

    Maya MCP 서버는 Command Port를 통해 Maya와 통신합니다. MCP 서버가 Maya와 처음 통신을 시도할 때 Maya 내에 권한을 요청하는 팝업이 나타날 수 있습니다. 지속적인 통신을 활성화하려면 **"Allow All"**을 클릭하세요.

### 3. Griptape Nodes 구성

1. **Griptape Nodes를 열고** **Settings** → **MCP Servers**로 이동합니다.

1. **+ New MCP Server를 클릭합니다.**

1. **서버를 구성합니다**:

    - **Server Name/ID**: `maya`
    - **Connection Type**: `Local Process (stdio)`
    - **Configuration JSON** (플랫폼에 맞는 적절한 예제를 선택):

    **Windows**:

    ```json
    {
      "transport": "stdio",
      "command": "C:\\path\\to\\MayaMCP\\.venv\\Scripts\\python.exe",
      "args": [
        "C:\\path\\to\\MayaMCP\\src\\maya_mcp_server.py"
      ],
      "cwd": null,
      "encoding": "utf-8",
      "encoding_error_handler": "strict"
    }
    ```

    **macOS/Linux**:

    ```json
    {
      "transport": "stdio",
      "command": "/path/to/MayaMCP/.venv/bin/python",
      "args": [
        "/path/to/MayaMCP/src/maya_mcp_server.py"
      ],
      "cwd": null,
      "encoding": "utf-8",
      "encoding_error_handler": "strict"
    }
    ```

    !!! warning "경로 구성"

        예제 경로를 MayaMCP 프로젝트 디렉터리의 실제 절대 경로로 바꿉니다. macOS/Linux에서는 슬래시(`/`)를, Windows에서는 역슬래시(`\\`)를 사용하세요.

1. **Create Server를 클릭합니다.**

## 사용 가능한 도구

### 기본 도구

- **`list_objects_by_type`** - 씬에 있는 객체 목록 조회 (카메라, 조명, 머티리얼 또는 셰이프별 선택적 필터링 지원)
- **`create_object`** - 기본 객체 생성 (큐브, 원뿔, 구, 원기둥, 카메라, 조명)
- **`get_object_attributes`** - Maya 객체의 속성 목록 조회
- **`set_object_attribute`** - 특정 값으로 객체의 속성 설정
- **`scene_new`** - Maya에서 새 씬 생성
- **`scene_open`** - Maya에 씬 로드
- **`scene_save`** - 현재 씬 저장
- **`select_object`** - 씬에서 객체 선택
- **`clear_selection_list`** - 사용자 선택 목록 지우기
- **`viewport_focus`** - 객체에 초점을 맞추도록 뷰포트 중앙 정렬 및 맞춤

### 고급 모델링 도구

- **`create_advanced_model`** - 세부 파라미터를 사용하여 복잡한 3D 모델 생성 (자동차, 나무, 건물, 컵, 의자)
- **`mesh_operations`** - 모델링 작업 수행 (돌출(extrude), 베벨(bevel), 세분화(subdivide), 불리언(boolean), 결합(combine), 브리지(bridge), 분할(split))
- **`create_material`** - 머티리얼 생성 및 할당 (lambert, phong, wood, marble, chrome, glass 등)
- **`create_curve`** - NURBS 커브 생성 (선, 원, 나선, 헬릭스, 별, 기어 등)
- **`curve_modeling`** - 커브 기반 모델링을 사용하여 지오메트리 생성 (돌출(extrude), 로프트(loft), 회전(revolve), 스윕(sweep) 등)
- **`organize_objects`** - 그룹화, 부모 지정(parenting), 레이아웃, 정렬 및 배치를 통해 객체 정리

## 구성 옵션

구성을 수정하여 Maya 연결을 사용자 지정할 수 있습니다:

```json
{
  "transport": "stdio",
  "command": "/path/to/MayaMCP/.venv/bin/python",
  "args": [
    "/path/to/MayaMCP/src/maya_mcp_server.py"
  ],
  "cwd": "/path/to/MayaMCP",
  "encoding": "utf-8",
  "encoding_error_handler": "strict"
}
```

### 플랫폼별 경로

**Windows**:

```json
{
  "command": "C:\\path\\to\\MayaMCP\\.venv\\Scripts\\python.exe",
  "args": ["C:\\path\\to\\MayaMCP\\src\\maya_mcp_server.py"]
}
```

**macOS/Linux**:

```json
{
  "command": "/path/to/MayaMCP/.venv/bin/python",
  "args": ["/path/to/MayaMCP/src/maya_mcp_server.py"]
}
```

## 활용 예시

다음은 Maya MCP 서버로 생성할 수 있는 몇 가지 예시입니다:

- "바퀴 4개와 스포티한 디자인을 가진 단순한 자동차 모델을 만들어줘"
- "가지가 3개 있고 잎이 울창한 나무를 만들어줘"
- "창문이 있는 건물을 만들고 벽돌 머티리얼을 적용해줘"
- "컵을 만들고 크롬 머티리얼을 적용해줘"
- "의자를 만들고 씬에 배치해줘"
- "나선형 커브를 생성하고 돌출시켜 스프링을 만들어줘"
- "기계 모델링을 위한 기어 모양의 커브를 생성해줘"
- "모든 가구 객체를 함께 그룹화해줘"
- "모든 객체를 월드 원점(0,0,0)에 정렬해줘"
- "새 씬을 만들고 'my_project.ma'로 저장해줘"

## 고급 기능

### 커스텀 모델 생성

`create_advanced_model` 도구는 특정 파라미터를 사용하여 다양한 모델 유형을 지원합니다:

**자동차 모델**:

```json
{
  "model_type": "car",
  "parameters": {
    "wheels": 4,
    "sporty": true,
    "convertible": false
  }
}
```

**나무 모델**:

```json
{
  "model_type": "tree",
  "parameters": {
    "branches": 3,
    "leaf_density": 0.8,
    "type": "pine"
  }
}
```

### 머티리얼 생성

커스텀 속성으로 다양한 머티리얼 유형 생성:

**크롬 머티리얼**:

```json
{
  "material_type": "chrome",
  "color": [0.8, 0.8, 0.8],
  "parameters": {
    "reflectivity": 0.9
  }
}
```

**나무 머티리얼**:

```json
{
  "material_type": "wood",
  "color": [0.6, 0.4, 0.2],
  "parameters": {
    "veinSpread": 0.5,
    "veinColor": [0.3, 0.2, 0.1]
  }
}
```

## 문제 해결

### 일반적인 문제

- **연결 문제**: Maya가 실행 중이고 Command Port가 활성화되어 있는지 확인하고, Griptape Nodes에서 MCP 서버가 올바르게 구성되었는지 검증하며, Python 경로가 올바른 가상 환경을 가리키고 있는지 확인하세요.
- **권한 거부 (Permission Denied)**: Maya가 처음 연결될 때 통신을 활성화하려면 Maya 팝업에서 "Allow All"을 클릭하세요.
- **경로 문제**: 구성에서 절대 경로를 사용하고, MayaMCP 프로젝트 경로가 올바른지 확인하세요.
- **Python 버전**: Python 3.10 이상을 사용하고 있는지 확인하세요.

### 디버깅 팁

1. 먼저 간단한 명령으로 테스트합니다 (예: "씬의 모든 객체 나열").
1. Maya가 실행 중이고 접근 가능한지 확인합니다.
1. 가상 환경이 제대로 활성화되었는지 확인합니다.
1. 구성의 모든 파일 경로가 절대 경로이고 올바른지 확인합니다.
1. 문제가 지속되면 Maya와 MCP 연결을 모두 재시작합니다.

### Maya Command Port

Maya MCP 서버는 통신을 위해 Maya의 기본 Command Port를 사용합니다. 이는 다음을 의미합니다:

- 추가적인 Maya 플러그인이나 애드온이 필요하지 않습니다.
- 통신은 MEL 스크립팅을 통해 이루어집니다.
- Python 코드는 Maya의 Python 인터프리터 내에서 실행됩니다.
- 결과는 Command Port를 통해 반환됩니다.

## 보안 고려 사항

!!! danger "임의 코드 실행"

    Maya MCP 서버는 Maya 환경 내에서 Python 코드를 실행합니다. 이는 강력하지만 잠재적으로 위험할 수 있습니다:

    - MCP 서버를 사용하기 전에 **항상 Maya 작업 내용을 저장하세요**.
    - 가능한 경우 생성된 작업을 검토하세요.
    - 프로덕션 환경에서는 주의하여 사용하세요.
    - 복잡한 작업은 잠재적으로 Maya 씬에 영향을 미칠 수 있음을 유의하세요.

!!! info "Maya Command Port"

    서버는 통신을 위해 Maya의 Command Port를 사용합니다:

    - 이는 외부 통신을 위한 표준 Maya 기능입니다.
    - 추가적인 보안 조치는 구현되어 있지 않습니다.
    - Maya 설치 환경이 안전하고 최신 상태인지 확인하세요.
    - Maya에 대한 외부 접근을 허용할 때의 영향을 고려하세요.

## 참고 자료

- [Maya MCP Server 저장소](https://github.com/PatrickPalmer/MayaMCP) - 공식 저장소 및 설명서
- [Autodesk Maya](https://www.autodesk.com/products/maya) - 공식 Maya 문서
- [Maya Python API](https://help.autodesk.com/view/MAYAUL/2023/ENU/?guid=Maya_SDK_Python_ref_index_html) - Maya 스크립팅 레퍼런스
- [Model Context Protocol](https://modelcontextprotocol.io/) - MCP 사양 및 설명서

## 개발자 노트

Maya MCP 서버는 쉽게 확장할 수 있도록 설계되었습니다. `mayatools/thirdparty` 디렉터리에 Python 파일을 생성하여 새로운 도구를 추가할 수 있습니다. 서버는 런타임에 새 도구를 자동으로 검색하고 등록합니다.

핵심 설계 원칙:

- 도구는 Maya의 Python 환경에서 직접 실행됩니다.
- 도구 파일에 MCP 데코레이터가 필요하지 않습니다.
- 네임스페이스 오염을 방지하기 위해 함수 범위가 제한됩니다.
- 결과는 Maya의 Command Port를 통해 반환됩니다.
- 통신 레이어에 오류 처리가 내장되어 있습니다.
