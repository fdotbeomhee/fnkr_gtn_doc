# 워크플로우 변수 (Workflow Variables)

**변수(variable)**는 특정 단일 노드가 아닌 플로우에 상주하는 이름이 지정된 값입니다. 한 번 생성하면 `{variable_name}`을 사용하여 텍스트 필드 내부의 인라인을 포함해 해당 값이 표시되어야 하는 모든 위치에서 읽거나 쓸 수 있습니다.

!!! info "매크로 경로 변수와의 차이점"

    [매크로](projects/macros.md)는 프로젝트 시스템이 **파일 경로**를 구성하는 데 사용하는 템플릿 구문(`{outputs}/{file_name_base}.{file_extension}`)입니다. 워크플로우 변수는 워크플로우의 노드 파라미터 전반에 걸쳐 저장하고 재사용하는 사용자 생성 값입니다. 두 시스템은 내부적으로 동일한 `{name}` / `{name:spec}` 구문과 포맷 사양을 공유하며, 텍스트 필드에 `{`를 입력할 때 프로젝트 매크로(`workspace_dir`, `workflow_name` 등)가 사용자 변수와 함께 표시되기도 합니다. 하지만 매크로 경로 템플릿은 파일 이름을 빌드하는 방법을 설명하는 반면, 워크플로우 변수는 실행 중에 사용자가 정의하고 변경하는 값입니다. 저장 경로를 작성 중인 경우 [매크로](projects/macros.md)를 참조하세요.

## 변수를 사용하는 경우

워크플로우 전체의 세 프롬프트 필드에 모두 동일한 클라이언트 이름이 필요하다고 가정해 보겠습니다. 한 소스 노드에서 필요한 모든 필드로 연결 선을 연결할 수도 있지만, 캔버스가 복잡해지고 와이어를 드래그하는 대신 필드에 값을 직접 입력하려는 경우 문제가 발생할 수 있습니다.

변수는 연결 선 없이 이 문제를 해결합니다. 한 번 설정한 후 원하는 만큼 여러 텍스트 파라미터 내에서 `{project_name}`을 참조할 수 있습니다. 변수 값을 변경하면 이를 참조하는 모든 필드가 다음에 실행될 때 새 값을 가져옵니다.

## 변수 생성하기

노드 라이브러리의 **Variables** 카테고리에는 변수를 생성하고 관리하기 위한 노드 모음이 있습니다:

<!-- screenshot (#5166): the Variables category in the node library panel, showing Create Variable, Set Variable, Get Variable, Has Variable, Set Variables from Data, and Set Variable Substitution -->

- **Create Variable** — 이름, 유형 및 초기 값을 사용하여 변수를 생성하거나 현재 플로우에 해당 이름의 변수가 이미 있는 경우 업데이트합니다. 임의의 출력을 `value` 입력에 연결하면 해당 연결에서 변수의 유형이 자동으로 유추됩니다.
- **Set Variable** — 변수 값을 설정하며 변수가 아직 없는 경우 먼저 생성합니다. `variable_name` 필드는 이미 스코프 내에 있는 변수의 드롭다운과 선택 시 이름 필드가 나타나는 **Create new variable** 옵션을 제공합니다.
- **Set Variables from Data** — 딕셔너리, JSON/YAML 문자열 또는 키-값 쌍 목록을 한 단계로 여러 변수로 변환합니다. 이미 JSON 데이터(파싱된 구성, API 응답)가 있고 키마다 **Set Variable** 노드를 하나씩 연결하는 대신 각 키가 자체 변수가 되도록 하려는 경우에 유용합니다.
- **Get Variable** — 인라인 `{name}` 치환을 지원하지 않는 노드에 연결할 수 있도록 변수의 현재 값을 일반 출력으로 읽어옵니다.
- **Has Variable** — 변수의 존재 여부를 확인하여 분기 처리를 수행할 수 있습니다(예: 플로우가 처음 실행될 때만 변수 생성).

<!-- screenshot (#5166): a Create Variable node configured with variable_name "project_name" and a text value wired into its value input -->

**Create Variable** 및 **Set Variable**은 항상 노드 자체의 플로우(아래에 설명된 플로우 스코프)에 변수를 생성합니다. 현재 전역 변수를 직접 생성하는 노드는 없습니다. 이러한 노드의 **scope** 설정(**Advanced** 아래)은 새 변수가 생성되는 위치가 아니라 기존 변수를 *검색*할 위치를 제어합니다.

이 노드들은 다루는 변수를 한 번 확인된 값이 아닌 라이브 상태로 취급합니다. **Create Variable**은 워크플로우나 노드가 실행될 때마다 자체를 미확인(unresolved) 상태로 되돌려 항상 현재 `value`를 다시 적용합니다. **Get Variable**, **Set Variable**, **Has Variable**은 확인 상태가 요청될 때마다 변수의 실제 현재 값을 확인하고, 마지막으로 읽거나 쓴 내용과 더 이상 일치하지 않으면 미확인으로 표시합니다. 따라서 수동 실행 없이도 다른 곳(다른 **Set Variable** 노드, 다른 플로우)에서 변경된 내용을 가져옵니다.

## 스코프 (Scopes)

모든 변수 요청(생성, 가져오기, 설정, 나열)은 조회가 검색할 위치를 제어하는 **스코프(scope)**를 가집니다:

| 스코프 | 동작 |
| ------ | ---- |
| `hierarchical` (기본값) | 현재 플로우를 검색한 다음 루트까지 각 상위 플로우를 검색하고 전역 변수를 검색합니다. 첫 번째 일치 항목이 우선합니다. |
| `current_flow_only` | 노드가 있는 정확한 플로우만 검색합니다. 상위 플로우와 전역 변수는 완전히 무시합니다. |
| `global_only` | 전역 변수(어떤 플로우에도 속하지 않는 변수)만 검색합니다. |
| `all` | 단일 값을 확인하기보다는 주로 열거(선택기 또는 목록 채우기)를 위한 모든 플로우의 모든 변수입니다. |

대부분의 경우 `hierarchical`을 사용합니다. 최상위 플로우에서 `project_name`을 한 번 정의하면 그 안에 중첩된 모든 하위 플로우에서 재선언 없이 `{project_name}`을 읽을 수 있습니다. 중첩된 플로우가 자체 `project_name`을 정의하면 해당 중첩 정의는 내부의 모든 항목에 대해 상위 정의를 **가립니다(shadows)**. 상위 값은 변경되지 않고 유지되며 중첩 플로우를 벗어나면 다시 나타납니다.

간단히 말해, 읽으려는 위치에 가장 가까운 정의가 우선하며 플로우 스코프 변수가 전역 변수보다 우선합니다.

## 변수 유형 (Variable types)

변수의 `type`은 노드 파라미터에서 볼 수 있는 것과 동일한 유형 레이블(`str`, `int`, `float`, `bool`, `json`, 이미지 유형 등)입니다. **Create Variable** 및 **Set Variable**은 `value` 입력에 연결된 모든 항목에서 유형을 자동으로 유추합니다. `value`를 연결하지 않고 노드에 직접 리터럴을 입력하는 경우가 아니면 직접 설정할 필요가 없습니다.

`str` 또는 `int` 값(`bool` 제외)을 보유하는 변수만 텍스트 필드의 `{name}` 토큰에 인라인으로 치환될 수 있습니다(아래 [인라인 치환](#텍스트-필드의-인라인-치환-inline-substitution-in-text-fields) 참조). 다른 유형(JSON, 이미지, 리스트)을 보유하는 변수는 **Get Variable** / **Set Variable**과 함께 정상적으로 작동합니다. 이미지를 텍스트로 인라인 렌더링하는 합리적인 방법이 없기 때문에 단순 토큰으로 텍스트 필드에 삽입할 수 없을 뿐입니다.

## 실행 중 값 설정 및 읽기

노드 외부에서 엔진의 요청 API를 대상으로 직접 스크립팅하는 경우 관련 요청은 `CreateVariableRequest`, `GetVariableValueRequest`, `SetVariableValueRequest`이며 각각 `name`과 `lookup_scope`를 사용합니다. `SetVariableValueRequest`는 해당 변수를 참조하는 다운스트림의 이미 확인된 노드를 미확인 상태로 만듭니다. 따라서 편집 중간에 변수를 변경하면 이전 값에서 계산된 모든 항목이 올바르게 무효화됩니다.

인라인 치환의 경우 일반 변수 조회 위에 한 단계가 더 추가됩니다. 노드가 실행될 때 엔진은 파라미터 값의 모든 `{name}` 토큰을 **사용자 워크플로우 변수와 프로젝트의 읽기 전용 매크로 값**(`workspace_dir`, `workflow_name` 및 모든 프로젝트 템플릿 디렉터리)의 병합된 세트에 대해 확인합니다. 이름이 충돌하는 경우 사용자 변수가 우선합니다. 이 병합을 통해 텍스트 필드에서 하나는 프로젝트에서 오고 다른 하나는 사용자가 생성한 변수에서 오더라도 `{workflow_name}`과 `{project_name}`을 나란히 참조할 수 있습니다.

## 텍스트 필드의 인라인 치환 (Inline substitution in text fields)

이를 지원하는 텍스트 파라미터 내부에서 `{`를 입력하면 에디터에 소스별로 그룹화된 현재 스코프 내의 모든 변수와 프로젝트 매크로를 나열하는 변수 선택기가 팝업됩니다:

<!-- screenshot (#5166): a text parameter field with the { picker open, showing grouped variables and project macros -->

하나를 선택하거나 이름을 직접 입력하고 중괄호를 닫으면(`{project_name}`) 실행 시 해당 값이 치환됩니다. 이름이 스코프 내의 어떤 항목으로도 확인되지 않으면 토큰은 변경되지 않은 채 텍스트에 유지되므로(또는 선택적 토큰의 경우 조용히 삭제됨 — 아래 참조) 오타로 인해 잘못된 값이 조용히 생성되지 않습니다.

`{literal text like this}`와 같은 텍스트가 토큰으로 처리되지 않고 그대로 통과되기를 원하는 경우 **Set Variable Substitution** 노드를 사용하여 워크플로우별로 이 동작을 끌 수 있습니다.

### 서식 지정자 (Format specs)

토큰은 `:` 뒤에 서식 지정자를 포함할 수 있으며 왼쪽에서 오른쪽으로 적용됩니다. [매크로 → 문자열 변환](projects/macros.md#문자열-변환)에 자세히 문서화된 것과 동일한 구문 및 사양 목록을 사용합니다:

| 지정자 (Spec) | 결과 (Result) |
| ------------- | ------------- |
| `:lower` | 모두 소문자 |
| `:upper` | 모두 대문자 |
| `:title` | 제목 대소문자 (Title Case) |
| `:snake` | snake_case |
| `:pascal` | PascalCase |
| `:camel` | camelCase |
| `:screaming_snake` | SCREAMING_SNAKE_CASE |
| `:slug` | url-safe-slug |
| `:dot` | dot.case |
| `:abbrev` | 각 단어의 첫 글자 |
| `:trim` | 앞/뒤 공백 제거 |

```
{project_name}            → "Autumn Campaign"
{project_name:lower}      → "autumn campaign"
{project_name:slug}       → "autumn-campaign"
{project_name:snake:upper} → "AUTUMN_CAMPAIGN"
```

매크로에서 이어져 동일하게 작동하는 두 가지 구문이 더 있습니다:

- **선택적 마커 `?`** — `{project_name?}`은 변수를 찾을 수 없을 때 리터럴 토큰을 남기는 대신 빈 문자열로 렌더링되므로 변수가 설정되지 않았을 때 구문을 자연스럽게 생략하는 문장을 작성할 수 있습니다.
- **기본값 `|default`** — `{project_name|untitled}`는 `project_name`이 스코프에 없으면 `untitled`로 치환합니다.

숫자 패딩(`:03`), 시퀀스 슬롯(`{###}`), 선행 구분 기호(`:^prefix`)도 동일한 엔진에 의해 파싱되며 텍스트 필드 내에서 기술적으로 유효하지만, 자동 증가 카운터가 있는 파일 이름을 작성하기 위해 존재합니다. 해당 동작이 필요한 경우 [매크로](projects/macros.md)를 참조하세요. 일반 텍스트 치환의 경우 위의 변환 및 선택적/기본값 사양이 일반적인 사용 사례를 다룹니다.

## 실습 예제: 여러 프롬프트에 걸친 프로젝트 이름

워크플로우가 컨셉 아트를 생성하고 여러 프롬프트 필드에서 동일한 프로젝트 이름이 필요하다고 가정해 보겠습니다.

1. 플로우 상단 근처에 **Create Variable** 노드를 추가합니다. `variable_name`을 `project_name`으로 설정하고 `value`에 `Autumn Campaign`을 직접 입력한 후(리터럴의 경우 연결 불필요) `variable_type`은 유추된 `str`로 유지합니다.

1. 프롬프트 작성 노드의 텍스트 필드에 다음을 입력합니다:

    ```
    A cinematic key art poster for {project_name}, dramatic lighting, wide shot
    ```

1. 플로우의 다른 위치에 있는 두 번째 프롬프트 필드에 다음을 입력합니다:

    ```
    Concept sketch, {project_name:slug} style guide, muted palette
    ```

1. 플로우를 한 번 실행합니다. 두 프롬프트 모두 "Autumn Campaign"(또는 사용자가 입력한 내용)을 가져오며 두 번째 프롬프트는 슬러그 사양이 적용된 `autumn-campaign`을 렌더링합니다.

<!-- screenshot (#5166): the finished flow with a Create Variable node feeding project_name into two prompt fields that show the resolved {project_name} text -->

새로운 실행을 위해 프로젝트를 변경하려면 **Create Variable** 노드에서 값을 편집하거나 다른 소스를 연결하기만 하면 됩니다. `{project_name}`을 참조하는 모든 필드는 아무것도 다시 연결할 필요 없이 다음 번 플로우가 실행될 때 업데이트됩니다.
