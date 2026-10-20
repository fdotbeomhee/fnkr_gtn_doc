# Cedar 정책 (Cedar Policies) {: #cedar-policies }

Griptape Nodes 권한 템플릿은 Amazon이 오픈 소스로 공개한 권한 부여 언어인 [Cedar](https://www.cedarpolicy.com/)를 사용합니다. [권한 에디터(Permission Editor)](admin_dashboard.md#permission-editor)에서 **Permission Builder**는 선택 사항을 Cedar로 변환합니다. **Raw Cedar** 템플릿을 작성하거나 빌더가 생성한 Cedar를 감사해야 할 때 이 페이지를 참조하세요.

빌더의 기능 카탈로그로 필요한 규칙을 표현할 수 없는 경우에만 Raw Cedar를 선택하세요.

## 정책 입력 (Policy inputs) {: #policy-inputs }

Cedar는 `(principal, action, resource, context)` 네 가지 입력으로 각 결정을 내립니다. Griptape Nodes는 각 권한 검사 지점과 라이선스 데이터를 이 Cedar 입력에 매핑합니다.

| 구성 요소 | 포함하는 내용 | 용도 |
| --- | --- | --- |
| `principal` | 고정된 자리 표시자 `User::"<anonymous>"`입니다. | 없음. 제약을 두지 마세요. |
| `action` | 권한 검사 지점입니다(예: `Action::"LoadLibrary"`). | 규칙이 다루는 작업을 지정합니다. |
| `resource` | 검사 지점에서 확인된 라이브러리, 노드 유형, 프로젝트, 모델 또는 코덱입니다. | 특정 대상이나 그 대상의 속성과 일치시킵니다. |
| `context` | 활성 프로젝트, 엔진, 라이브러리 및 라이선스 정보입니다. | 규칙을 하나의 프로젝트나 라이선스 유형으로 범위 지정합니다. |

## 결정 결합 (Combining decisions) {: #combining-decisions }

Cedar는 여러 템플릿을 결합할 때 다음 두 가지 규칙을 따릅니다.

1. 일치하는 `permit` 문 중 하나 이상이 작업을 허용해야 합니다.
1. 일치하는 `forbid` 문은 다른 템플릿의 `permit`을 포함하여 모든 `permit`보다 우선합니다.

빌더는 다음 Cedar 패턴을 생성합니다.

- **Exploration (Allow all)**은 `permit(principal, action, resource);`로 시작한 후 예외에 대한 `forbid` 문을 추가합니다.
- **Production (Deny All)**은 모든 항목을 허용하는 기본 `permit`을 생략하고 허용할 작업에 대한 `permit` 문을 추가합니다.

!!! warning "기본 거부와 강제 차단"

    Cedar는 하나 이상의 문이 작업을 허용하지 않으면 해당 작업을 거부합니다. 따라서 `forbid` 문만 있는 정책 집합은 해당 문과 일치하는 작업만이 아니라 모든 작업을 거부합니다. 차단 목록을 만들려면 `forbid` 문과 함께 모든 항목을 허용하는 `permit`을 사용하세요.

    ```cedar
    permit(principal, action, resource);

    forbid(principal, action, resource)
    when { resource has lifecycle_stage && resource.lifecycle_stage == "LABS" };
    ```

    `permit`을 생략한다고 해서 강제 차단이 되지는 않습니다. 다른 템플릿의 `permit`이 작업을 허용할 수 있습니다. 어떤 `permit`도 거부를 무시하지 않아야 하는 경우에는 `forbid`를 사용하세요. Cedar는 라이선스 키에 연결된 모든 템플릿을 하나의 정책 집합으로 평가하므로 `permit`과 `forbid`를 서로 다른 템플릿에 나눠 둘 수도 있습니다.

## 검사 지점 (Checkpoints) {: #checkpoints }

아래의 각 작업은 엔진이 제어하는 작업을 나타냅니다. 거부의 결과는 검사 지점에 따라 다릅니다.

| 작업 | 발생 시점 | 리소스 | 거부 시 나타나는 모습 |
| --- | --- | --- | --- |
| `Action::"LoadLibrary"` | 라이브러리가 메타데이터 단계를 지나 로드될 때 | `Library` | 라이브러리가 사용할 수 없는 상태로 표시되며, 오류 아이콘에 이유가 나타납니다. |
| `Action::"InstantiateNode"` | 노드가 생성될 때 | `NodeType` | 실제 노드 대신 Error Proxy 노드가 표시됩니다. 거부된 노드 유형은 라이브러리에도 미리 표시됩니다. |
| `Action::"LoadProject"` | 프로젝트 템플릿을 읽을 때 | `Project` | 프로젝트 로드에 실패합니다. |
| `Action::"ActivateProject"` | 프로젝트가 현재 프로젝트가 될 때 | `Project` | 전환에 실패하고 현재 프로젝트가 그대로 유지됩니다. |
| `Action::"OfferModel"` | 모델 선택기가 생성될 때 | `Model` | 해당 모델이 선택기에서 필터링됩니다. |
| `Action::"InvokeModel"` | 노드가 모델을 호출할 때 | `Model` | 호출이 실패합니다. |
| `Action::"ReadVideoCodec"` | 비디오를 읽으려고 할 때 및 선택기가 생성될 때 | `VideoCodec` | 읽기가 거부되거나 코덱이 필터링됩니다. |
| `Action::"WriteVideoCodec"` | 비디오를 쓰려고 할 때 및 선택기가 생성될 때 | `VideoCodec` | 쓰기가 거부되거나 코덱이 필터링됩니다. |

## 리소스 속성 (Resource attributes) {: #resource-attributes }

표에 별도로 명시되지 않는 한 각 리소스 속성은 선택 사항입니다. 선택 속성을 읽기 전에 `has`를 사용하세요. 필수 패턴은 [선택 속성 보호하기](#guard-optional-attributes)를 참조하세요.

### `Library`

| 속성 | 유형 | 존재 조건 |
| --- | --- | --- |
| `id` | string | 항상 존재합니다. 라이브러리 이름입니다. |
| `lifecycle_stage` | string | 라이브러리가 단계를 선언한 경우에 존재합니다. |

### `NodeType`

| 속성 | 유형 | 존재 조건 |
| --- | --- | --- |
| `id` | string | 항상 존재합니다. 노드 유형 이름입니다. |
| `executes_arbitrary_code` | bool | 항상 존재합니다. 노드의 라이브러리 선언이 임의의 Python 실행을 표시하면 `true`입니다. |
| `lifecycle_stage` | string | 노드가 단계를 선언했거나 라이브러리로부터 단계를 상속받은 경우에 존재합니다. |
| `model_ids` | set<string> | 노드가 모델 사용을 선언한 경우에 존재합니다. |
| `provider_ids` | set<string> | 노드가 모델 또는 공급자 사용을 선언한 경우에 존재합니다. |
| `model_families` | set<string> | 노드가 선언한 모델이 라이브러리 모델 카탈로그에서 제품군으로 확인되는 경우에 존재합니다. |

### `Project`

| 속성 | 유형 | 존재 조건 |
| --- | --- | --- |
| `id` | string | 항상 존재합니다. 프로젝트의 불투명한 id이며, 에디터에서 만든 프로젝트는 GUID입니다. |
| `name` | string | 템플릿이 이름을 알 수 있을 만큼 로드된 경우에 존재합니다. |

사람이 읽을 수 있는 규칙에는 `name`을 사용하고, 정확히 일치해야 할 때는 `id`를 사용하세요. `permit`에서는 `id`를 우선 사용하세요. 프로젝트 이름은 템플릿이 로드된 후에만 사용할 수 있으며, 일치하지 않는 `permit`은 거부로 이어집니다.

### `Model`

| 속성 | 유형 | 존재 조건 |
| --- | --- | --- |
| `id` | string | 항상 존재합니다. 안정적인 카탈로그 모델 키입니다. |
| `provider_id` | string | 키가 모델 카탈로그에서 확인되는 경우에 존재합니다. |
| `model_families` | set<string> | 확인된 모델이 제품군을 선언한 경우에 존재합니다. 해당 제품군 하나만 포함합니다. |
| 단일 모델 대신 공급자와 일치시키려면 [엔터티 계층 구조](#entity-hierarchy)를 참조하세요. |  |  |

### `VideoCodec`

| 속성 | 유형 | 존재 조건 |
| --- | --- | --- |
| `id` | string | 항상 존재합니다. 확인된 코덱 이름입니다(예: `h264`, `hevc`). |
| `container_format` | string | 컨테이너를 알 수 있는 경우에 존재합니다(예: `mp4`, `mov`). |

## 엔터티 계층 구조 (Entity hierarchy) {: #entity-hierarchy }

### `ModelProvider`

각 `Model`은 해당 모델을 제공하는 공급자 아래에 위치합니다. 따라서 `in`을 사용하면 모델을 하나씩 지정하지 않고도 공급자의 모든 모델을 포함할 수 있습니다.

예를 들면 다음과 같습니다.

```cedar
forbid(principal, action, resource)
unless { resource in ModelProvider::"anthropic" };
```

공급자 id는 `anthropic` 또는 `ollama`처럼 라이브러리의 [`model_catalog`](../development/custom_nodes/authoring_libraries.md)에 있는 공급자 키입니다.

## 컨텍스트 정보 (Context facts) {: #context-facts }

!!! warning "컨텍스트 정보 보호하기"

    모든 컨텍스트 정보는 선택 사항입니다. Griptape Nodes는 확인할 수 있는 정보만 포함하고 나머지는 생략합니다. 따라서 값을 읽기 전에 각 항목을 `has`로 확인하세요.

    레코드를 먼저 보호한 후 그 안의 값을 읽으세요.

    ```cedar
    when {
      context has loaded_libraries &&
      context.loaded_libraries.names.contains("My Library")
    }
    ```

| 정보 | 유형 | 참고 |
| --- | --- | --- |
| `active_project.id` | string | 엔진 레지스트리 키로 사용되는 불투명한 프로젝트 id입니다. |
| `active_project.name` | string | 템플릿이 로드된 경우의 표시 이름입니다. 별도의 `has` 보호가 필요합니다. |
| `engine.id` | string | 활성 엔진의 id입니다. |
| `loaded_libraries.names` | set<string> | 지금까지 로드된 라이브러리의 이름입니다. |
| `license_id` | string | 라이선스 키의 id입니다. |
| `org_id` | string | 라이선스가 속한 조직입니다. |
| `license_type` | string | `"headless"` 또는 `"interactive"` 중 하나입니다. |

## 프로젝트 범위 (Project scope) {: #project-scope }

빌더는 Project-scoped 템플릿의 모든 문에 연결된 프로젝트를 사용하여 다음 조건을 추가합니다.

```cedar
when { context has active_project && context.active_project.id == "<project id>" }
```

Griptape Nodes는 활성 프로젝트 체인의 각 프로젝트에 대해 정책을 평가합니다. 이로 인해 다음 두 가지 결과가 생깁니다.

**상위 프로젝트를 포함하세요.** 프로젝트가 상위 프로젝트를 상속하면 정책은 체인의 각 프로젝트에 대해 실행됩니다. 먼저 활성 프로젝트를 확인하고, 그다음 각 상위 프로젝트를 확인합니다. 모든 실행이 작업을 허용해야 합니다. 따라서 상위 프로젝트에 범위가 지정된 `forbid`는 하위 프로젝트를 차단하며, 프로젝트 허용 목록에는 체인의 모든 프로젝트가 포함되어야 합니다.

예를 들어 **Shot 42**가 **Studio Defaults**를 상속받는다고 가정하겠습니다. Shot 42에서 라이브러리를 허용하려면 두 프로젝트 id 모두에 대한 `permit`이 필요합니다.

```cedar
permit(principal, action == Action::"LoadLibrary", resource)
when {
  context has active_project &&
  context.active_project.id == "<Shot 42 id>"
};

permit(principal, action == Action::"LoadLibrary", resource)
when {
  context has active_project &&
  context.active_project.id == "<Studio Defaults id>"
};
```

첫 번째 `permit`만 있으면 Shot 42 검사는 성공하지만 Studio Defaults 검사는 거부됩니다. Studio Defaults와 일치하는 `forbid`가 있으면 Shot 42에서도 작업이 거부됩니다.

**기본 프로젝트를 고려하세요.** 별도로 설정하지 않으면 엔진은 [기본 프로젝트](../guides/projects/projects.md#the-system-defaults)로 시작하며, 이 프로젝트의 id는 `<system-defaults>`입니다. 이 id를 명시적으로 허용할 필요는 없습니다. 명시적인 `forbid` 규칙은 여전히 적용됩니다.

## 거부 주석 (Denial annotations) {: #denial-annotations }

Cedar는 작업을 거부한 규칙을 식별하지만, 사용자에게 부족한 권한이 무엇인지는 설명하지 않습니다. Griptape Nodes는 세 가지 주석을 사용하여 이 정보를 추가합니다. 세 주석은 모두 Griptape의 규칙이며, Cedar는 정책 평가 중에 이를 무시합니다.

| 주석 | 목적 |
| --- | --- |
| `@id("<slug>")` | 규칙의 안정적인 이름입니다. 위치 기반 대체 이름 대신 거부 메시지에 표시됩니다. |
| `@capability("<name>")` | 사용자에게 없는 권한입니다(예: `arbitrary-code-execution`). |
| `@advice("<text>")` | 해결 방법입니다. 사용자가 읽게 되는 문장입니다. |

```cedar
@id("nodes/no-arbitrary-code")
@capability("arbitrary-code-execution")
@advice("Nodes that run arbitrary code are not available on this license. Ask your studio admin to enable them.")
forbid(principal, action == Action::"InstantiateNode", resource)
when { resource has executes_arbitrary_code && resource.executes_arbitrary_code };
```

작성하는 모든 `forbid`에 이 주석을 추가하세요. `@advice`가 없으면 사용자는 정책 id만 보고 거부를 해결하는 방법은 알 수 없습니다.

`@capability`의 값은 자유 형식이며 Cedar가 검증하지 않습니다. 템플릿 전체에서 거부 메시지를 일관되게 유지하려면 빌더가 생성하는 이름을 재사용하는 것이 좋습니다.

- `library`
- `library-lifecycle`
- `node-lifecycle`
- `arbitrary-code-execution`
- `project`
- `model`
- `model-provider`
- `model-family`
- `video-codec`

## 예제 (Examples) {: #examples }

아래의 각 예제는 완전한 Raw Cedar 정책입니다.

### 임의 코드 실행 노드 차단하기

이 예제는 모든 항목을 허용하는 기본 `permit`을 포함하므로 완전한 차단 목록 템플릿입니다.

```cedar
permit(principal, action, resource);

@id("nodes/no-arbitrary-code")
@capability("arbitrary-code-execution")
@advice("Nodes that run arbitrary code are not available on this license.")
forbid(principal, action == Action::"InstantiateNode", resource)
when { resource has executes_arbitrary_code && resource.executes_arbitrary_code };
```

### 안정되지 않은 라이브러리 및 노드 차단하기

라이브러리와 노드에 별도의 문을 사용하세요. 그러면 각 거부 메시지에 사용자에게 없는 권한이 표시됩니다.

```cedar
permit(principal, action, resource);

@id("lifecycle/no-experimental-libraries")
@capability("library-lifecycle")
@advice("Experimental libraries are blocked on this license. Ask your studio admin which libraries are approved.")
forbid(principal, action == Action::"LoadLibrary", resource)
when {
  resource has lifecycle_stage &&
  (resource.lifecycle_stage == "LABS" || resource.lifecycle_stage == "ALPHA")
};

@id("lifecycle/no-experimental-nodes")
@capability("node-lifecycle")
@advice("Experimental nodes are blocked on this license. Use a STABLE or BETA alternative.")
forbid(principal, action == Action::"InstantiateNode", resource)
when {
  resource has lifecycle_stage &&
  (resource.lifecycle_stage == "LABS" || resource.lifecycle_stage == "ALPHA")
};
```

### 두 모델 공급자 허용하기

`unless` 절은 일치 조건을 반전하여 아래에 지정한 두 공급자를 제외한 모든 공급자를 차단합니다. `in` 연산자는 리소스에 일치하는 상위 항목이 없으면 오류 대신 false를 반환하므로 `has` 보호가 필요하지 않습니다.

모델을 제한할 때는 두 모델 검사 지점을 모두 지정하세요. `OfferModel`은 거부된 모델을 선택기에서 제거하고, `InvokeModel`은 이미 해당 모델에 연결된 노드를 차단합니다.

```cedar
permit(principal, action, resource);

@id("models/approved-providers")
@capability("model-provider")
@advice("Only Anthropic and OpenAI models are approved on this license.")
forbid(principal, action in [Action::"OfferModel", Action::"InvokeModel"], resource)
unless { resource in ModelProvider::"anthropic" || resource in ModelProvider::"openai" };
```

### 모델 제품군 차단하기

`model_families`는 집합(set)이므로 값을 일치시키려면 `contains`를 사용하세요.

```cedar
permit(principal, action, resource);

@id("models/no-claude-3")
@capability("model-family")
@advice("Claude 3 models are retired on this license. Use Claude 4.")
forbid(principal, action in [Action::"OfferModel", Action::"InvokeModel"], resource)
when { resource has model_families && resource.model_families.contains("Claude 3") };
```

### 특정 프로젝트 허용하기

엔진은 프로젝트 로드와 활성화를 별도로 제어하므로 이 규칙은 두 검사 지점을 모두 지정합니다.

```cedar
permit(principal, action, resource);

@id("projects/approved")
@capability("project")
@advice("This project is not on your license. Ask your studio admin for access.")
forbid(principal, action in [Action::"LoadProject", Action::"ActivateProject"], resource)
unless {
  resource has id &&
  (resource.id == "8f2c1a04-9d3e-4b7a-9f10-2c5d6e8a1b33" || resource.id == "c07b5e91-4a2d-4f88-bd63-1e9f7a205c48")
};
```

### 코덱 쓰기 제한하기

이 문은 쓰기 검사 지점만 지정합니다.

```cedar
permit(principal, action, resource);

@id("video/no-prores-writes")
@capability("video-codec")
@advice("Writing ProRes is not permitted on this license. Render to H.264 instead.")
forbid(principal, action == Action::"WriteVideoCodec", resource)
when { resource has id && resource.id == "prores" };
```

### 규칙을 한 프로젝트로 범위 지정하기

Cedar는 여러 `when` 절을 AND로 결합하므로 프로젝트 범위를 리소스 조건과 분리하여 유지할 수 있습니다.

```cedar
permit(principal, action, resource);

@id("show-a/no-experimental-nodes")
@capability("node-lifecycle")
@advice("Show A is locked to stable nodes.")
forbid(principal, action == Action::"InstantiateNode", resource)
when {
  context has active_project &&
  context.active_project has name &&
  context.active_project.name == "Show A"
}
when { resource has lifecycle_stage && resource.lifecycle_stage == "LABS" };
```

### Headless 라이선스 제한하기

```cedar
permit(principal, action, resource);

@id("headless/no-model-invocation")
@capability("model")
@advice("Headless licenses cannot call models on this plan.")
forbid(principal, action == Action::"InvokeModel", resource)
when { context has license_type && context.license_type == "headless" };
```

## 주의 사항 (Gotchas) {: #gotchas }

### 선택 속성 보호하기 (Guard optional attributes) {: #guard-optional-attributes }

없는 속성을 읽으면 오류가 발생합니다. Cedar는 오류가 포함된 조건을 충족되지 않은 것으로 처리하고, 엔진은 깔끔하게 평가할 수 없는 작업을 거부합니다. 따라서 보호되지 않은 읽기는 `forbid`가 해당 속성이 없는 리소스를 놓치게 하거나, 해당 정보를 포함하지 않는 올바른 작업을 차단하게 할 수 있습니다.

```cedar
// Wrong. Denies every node type with no declared lifecycle stage.
forbid(principal, action == Action::"InstantiateNode", resource)
when { resource.lifecycle_stage == "LABS" };

// Right.
forbid(principal, action == Action::"InstantiateNode", resource)
when { resource has lifecycle_stage && resource.lifecycle_stage == "LABS" };
```

`&&`는 단축 평가되므로 보호 조건을 가장 앞에 두세요.

각 수준에서 중첩된 정보도 보호하세요. `context has active_project`가 있다고 해서 활성 프로젝트에 `name`이 있다는 보장은 없습니다.

```cedar
when {
  context has active_project &&
  context.active_project has name &&
  context.active_project.name == "Show A"
}
```

### 이름의 오타 확인하기

엔진은 Cedar 정책을 스키마와 대조하여 검증하지 않습니다. `resource.lifecycle_stge`나 `Action::"LoadLibrry"` 같은 오타도 구문 분석에는 통과하지만 아무것과도 일치하지 않으므로, 규칙이 작동하지 않는 것처럼 보이게 됩니다.

### 필요한 모든 검사 지점 허용하기

Production 템플릿이 `LoadLibrary`만 허용하면 라이브러리 로드는 작동하지만, 기본 거부 때문에 다른 검사 지점의 작업은 차단됩니다. 워크플로우에 필요한 모든 작업을 지정하거나, 해당 작업을 허용하는 다른 템플릿을 연결하세요.

```cedar
// Permits only library loading.
permit(principal, action == Action::"LoadLibrary", resource);

// Broad permit covering every current checkpoint.
permit(principal, action in [
  Action::"LoadLibrary",
  Action::"InstantiateNode",
  Action::"LoadProject",
  Action::"ActivateProject",
  Action::"OfferModel",
  Action::"InvokeModel",
  Action::"ReadVideoCodec",
  Action::"WriteVideoCodec"
], resource);
```
