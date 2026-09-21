# 파일 확장자 디렉터리 (File Extension Directories)

`file_extension_directories`는 파일 확장자를 파일 유형별 라우팅에 사용되는 폴더 조각으로 매핑하는 프로젝트 템플릿 설정입니다. 상황 매크로가 파생 변수 `{file_extension_directory}`를 참조할 때 프로젝트 시스템은 이 테이블에서 파일의 확장자를 조회하고 연결된 값으로 치환합니다.

일반적인 용도: 각 유형별로 별도의 상황을 작성하지 않고도 이미지, 비디오, 오디오 및 문서를 `outputs/` 아래의 별도 하위 폴더에 보관할 수 있습니다.

## 빠른 예제

```yaml
project_template_schema_version: "0.3.0"
name: "My Project"

file_extension_directories:
  png: "images"
  jpg: "images"
  mp4: "videos"
  wav: "audio"

situations:
  save_node_output:
    macro: "{outputs}/{file_extension_directory?:/}{node_name?:_}{file_name_base}{_index?:03}.{file_extension}"
```

이 구성의 경우:

```
file_extension="png" → outputs/images/Node_render.png
file_extension="mp4" → outputs/videos/Node_render.mp4
file_extension="xyz" → outputs/Node_render.xyz        (매핑되지 않음: 슬롯 축소)
```

`{file_extension_directory?:/}`의 `?:/`는 슬롯을 선택 사항으로 만들고 값이 있을 때 후행 `/`를 추가합니다. 따라서 매핑되지 않은 확장자는 실패하는 대신 상황의 디렉터리 루트에 저장됩니다.

## 두 가지 값 형식

값은 **일반 이름(plain name)** 또는 **매크로(macro)**일 수 있습니다.

### 일반 이름

```yaml
file_extension_directories:
  png: "images"
```

문자열이 그대로 사용됩니다. 확인 과정이 발생하지 않습니다. 이는 일반적인 경우이며 오버헤드가 없습니다.

### 매크로 값

```yaml
file_extension_directories:
  mp4: "{outputs}/videos"
  wav: "{workspace_dir}/shared/audio"
```

`{...}`를 포함하는 값은 상황 매크로로 치환되기 전에 프로젝트의 내장 변수, 디렉터리 정의 및 호출자가 제공한 컨텍스트(예: `node_name`)를 기준으로 확인됩니다.

매크로 값을 사용하면 단일 `file_extension_directories` 테이블이 유형별로 별도의 상황을 작성하지 않고도 특정 유형을 완전히 다른 루트(예: 공유 드라이브의 비디오)로 다시 라우팅할 수 있습니다.

### 매크로 값이 참조할 수 있는 항목

| 소스 | 예시 | 사용 가능 여부 |
| --- | --- | --- |
| 내장 변수 | `{workspace_dir}`, `{workflow_dir}`, `{project_dir}`, `{project_name}`, `{static_files_dir}` | 예 |
| 디렉터리 정의 | `{outputs}`, `{inputs}`, `{temp}`, 모든 사용자 정의 디렉터리 | 예 |
| 호출자 제공 컨텍스트 | `{node_name}`, `{parameter_name}`, `{sub_dirs}`, `{_index}` | 예 |
| 파일 이름 부분 | `{file_name_base}`, `{file_extension}` | **아니요** — 라우팅은 파일 이름 레이어가 아님 |

파일 이름 부분은 의도적으로 제외됩니다. `file_extension_directories`는 파일이 들어갈 폴더를 결정하는 *라우팅* 레이어입니다. 파일 이름은 상황 매크로의 파일 이름 섹션에 속합니다.

## 확인 작동 방식

`file_extension_directory`는 **파생 변수**입니다. 내장 변수가 아니며 호출자가 직접 제공하지 않습니다. 대신 프로젝트 시스템은 상황의 매크로 템플릿이 이를 참조할 때마다 작은 파생 규칙을 실행합니다:

1. 호출자가 상황을 지정하고 변수(`file_extension` 포함)를 제공합니다.
1. 상황 매크로가 확인되기 전에 파생 규칙이 실행됩니다:
    - 호출자가 이미 `file_extension_directory`를 설정한 경우 규칙은 관여하지 않습니다(호출자 우선).
    - 그렇지 않은 경우 규칙은 현재 프로젝트의 `file_extension_directories` 테이블에서 `file_extension`(대소문자 구분 없음)을 조회합니다.
    - 값이 일반 문자열인 경우 직접 변수의 값이 됩니다.
    - 값이 매크로인 경우 먼저 구체적인 경로 문자열로 확인됩니다.
1. 결과 값이 변수 모음에 주입되고 상황 매크로가 평소와 같이 확인됩니다.

조회에 실패한 경우(빈 확장자, 로드된 프로젝트 없음, 매핑되지 않은 확장자 또는 확인 오류) 변수는 단순히 설정되지 않습니다. 선택적 형식 `{file_extension_directory?:/}`를 사용하는 상황 매크로는 폴더 접두사 없이 깔끔하게 처리됩니다. 필수 형식 `{file_extension_directory}`를 사용하는 매크로는 누락된 필수 변수와 마찬가지로 확인에 실패합니다.

## 상황 매크로와의 상호 작용

라우팅 접두사를 앞에 붙이거나 부모를 다시 지정하는 특별한 엔진 로직은 없습니다. 상황 매크로 템플릿에 명시된 내용이 그대로 적용됩니다.

| 상황 매크로 형태 | 라우팅 동작 |
| --- | --- |
| `{outputs}/{file_extension_directory?:/}{file_name_base}.{ext}` | 라우팅은 `{outputs}` 아래의 **하위 폴더**입니다. 값은 상대 경로여야 합니다. |
| `{file_extension_directory?:/}{file_name_base}.{ext}` | 라우팅이 **루트**를 지시합니다. `{outputs}`에서 완전히 벗어나 리디렉션하기 위해 값은 절대 경로일 수 있습니다. |
| 절대값을 갖는 `{outputs}/{file_extension_directory?:/}...` | 문자열 연결 — `outputs//Volumes/share/videos/foo.mp4` — 의도하지 않은 결과가 발생합니다. |

필요한 라우팅 종류와 일치하는 상황 매크로 형태를 선택하세요.

## 오버레이 병합 동작

`file_extension_directories`는 `environment`와 동일하게 항목별로 병합됩니다:

- 오버레이에 없는 키는 기본 템플릿에서 상속됩니다.
- 오버레이에 있는 키는 해당 확장자에 대한 기본 항목을 오버라이드합니다.
- 오버레이에서 `null` 값을 가진 키는 툼스톤(삭제 표시) 처리되어 기본 항목이 삭제됩니다.

```yaml
# 기본의 이미지 라우팅을 상속하고, mp4를 다른 곳으로 보내며, csv 라우팅을 삭제합니다.
file_extension_directories:
  mp4: "{workspace_dir}/shared/videos"
  csv: null
```

## 호출자 오버라이드

모든 호출자는 변수 모음에 `file_extension_directory`를 미리 채울 수 있습니다. 이미 설정되어 있으면 파생 규칙이 관여하지 않고 호출자의 값이 그대로 사용됩니다. 이를 통해 UI 수준 설정(예: 명시적 출력 폴더 오버라이드)이 동일한 상황 매크로에 참여하면서도 분류 체계를 우회할 수 있습니다.

## 기본값에 포함된 내용

시스템 기본값에는 일반적인 이미지, 비디오, 오디오, 텍스트 및 Python 소스 확장자에 대한 항목이 포함되어 있으며, 각각 `images`, `videos`, `audio`, `text`, `python` 하위 폴더로 라우팅됩니다. 개별 항목을 재정의하거나, 새 항목을 추가하거나, 원하지 않는 항목을 툼스톤 처리할 수 있습니다.\n