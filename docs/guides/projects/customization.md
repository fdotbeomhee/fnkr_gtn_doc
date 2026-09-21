# 커스터마이징 가이드 (Customization Guide)

모든 커스터마이징은 워크스페이스 디렉터리의 `griptape-nodes-project.yml`에서 수행됩니다. 변경하려는 항목만 포함하면 되며, 나머지는 모두 시스템 기본값에서 상속됩니다.

## outputs 디렉터리 경로 변경

모든 노드 출력을 `renders`라는 폴더로 이동합니다:

```yaml
project_template_schema_version: "0.1.0"
name: "My Project"

directories:
  outputs:
    path_macro: "renders"
```

이제 `{outputs}`를 사용하는 모든 시츄에이션은 `outputs` 대신 `renders` 폴더에 저장됩니다.

## inputs 디렉터리 경로 변경

```yaml
project_template_schema_version: "0.1.0"
name: "My Project"

directories:
  inputs:
    path_macro: "source_files"
```

## 커스텀 디렉터리 추가

클라이언트 납품용 디렉터리를 추가합니다:

```yaml
project_template_schema_version: "0.1.0"
name: "My Project"

directories:
  deliverables:
    path_macro: "client/final"
```

이제 모든 매크로에서 `{deliverables}`를 변수로 사용할 수 있습니다.

## 커스텀 시츄에이션 추가

덮어쓰기(overwrite) 정책으로 최종 렌더링을 deliverables 디렉터리에 저장하는 시츄에이션을 추가합니다:

```yaml
project_template_schema_version: "0.1.0"
name: "My Project"

directories:
  deliverables:
    path_macro: "client/final"

situations:
  save_deliverable:
    macro: "{deliverables}/{workflow_name?:_}{file_name_base}.{file_extension}"
    policy:
      on_collision: overwrite
      create_dirs: true
    description: "Final deliverable for client"
```

## 기존 시츄에이션 수정

파일 이름에 항상 워크플로 이름을 포함하도록 `save_node_output`을 변경합니다:

```yaml
project_template_schema_version: "0.1.0"
name: "My Project"

situations:
  save_node_output:
    macro: "{outputs}/{workflow_name:_}{file_name_base}{_index?:03}.{file_extension}"
```

`macro` 필드만 교체됩니다. 정책(policy), 폴백(fallback) 및 설명(description)은 기본값에서 상속됩니다.

## 환경 변수 추가

매크로에서 사용할 커스텀 값을 정의합니다:

```yaml
project_template_schema_version: "0.1.0"
name: "My Project"

environment:
  CLIENT_CODE: "ACME"
  SEASON: "S03"
```

디렉터리 경로 또는 시츄에이션 매크로에서 참조합니다:

```yaml
directories:
  outputs:
    path_macro: "{CLIENT_CODE}/{SEASON}/renders"
```

## 확장자별 파일 라우팅

각 파일 유형마다 별도의 시츄에이션을 작성하지 않고도 서로 다른 유형의 파일을 서로 다른 하위 폴더로 보내려면 `file_extension_directories`를 사용하세요. 전체 참조는 [파일 확장자 디렉터리](file_extension_directories.md)를 확인하세요.

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

확장자는 매크로로도 해석될 수 있습니다. 다른 모든 것은 `outputs` 아래에 유지하면서 비디오만 공유 드라이브로 라우팅하려면 라우팅 값이 루트를 지시하도록 하는 시츄에이션 매크로를 사용하세요:

```yaml
project_template_schema_version: "0.3.0"
name: "My Project"

file_extension_directories:
  png: "{outputs}/images"
  mp4: "{workspace_dir}/shared/videos"

situations:
  save_node_output:
    macro: "{file_extension_directory?:/}{node_name?:_}{file_name_base}{_index?:03}.{file_extension}"
```

## OS 환경 변수 참조

운영 체제 환경 변수에서 값을 가져옵니다:

```yaml
project_template_schema_version: "0.1.0"
name: "My Project"

environment:
  SHARED_DRIVE: "$STUDIO_SHARED_STORAGE"

directories:
  outputs:
    path_macro: "{SHARED_DRIVE}/renders"
```

OS 환경 변수 `STUDIO_SHARED_STORAGE`가 `/mnt/studio`로 설정되어 있으면 `{outputs}`는 `/mnt/studio/renders`로 해석됩니다.

## 종합 예시

시각 효과(VFX) 프로젝트를 위한 완전한 맞춤형 프로젝트 파일 예시:

```yaml
project_template_schema_version: "0.1.0"
name: "VFX Pipeline"
description: "Customized layout for VFX production"

environment:
  SHOW_CODE: "AURORA"

directories:
  inputs:
    path_macro: "source"
  outputs:
    path_macro: "renders"
  plates:
    path_macro: "source/plates"
  deliverables:
    path_macro: "deliverables/{SHOW_CODE}"

situations:
  save_node_output:
    macro: "{outputs}/{workflow_name?:_}{file_name_base}{_index?:03}.{file_extension}"

  save_deliverable:
    macro: "{deliverables}/{workflow_name?:_}{file_name_base}.{file_extension}"
    policy:
      on_collision: overwrite
      create_dirs: true
    fallback: save_file
    description: "Final deliverable"
```
