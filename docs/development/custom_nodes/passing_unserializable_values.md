# 직렬화할 수 없는 값 전달 (Passing Values That Cannot Be Serialized) {: #passing-values-that-cannot-be-serialized }

일부 값은 데이터로 변환할 수 없습니다. Diffusers 파이프라인, 잠재 텐서(latent tensor), 열린 파일 핸들, 실행 중인 드라이버가 이에 해당합니다. 이러한 값에는 JSON으로 표현할 방법이 없습니다. 라이브러리의 노드가 워커 서브프로세스에서 실행되는 경우([워커를 통한 노드 격리 (Node Isolation with Workers)](node_isolation_with_workers.md) 참조), 파라미터 값은 오케스트레이터와 워커 사이에서 JSON으로 전달됩니다. 따라서 노드에서 다음 노드로 이러한 값을 전달하려면 별도의 처리가 필요합니다.

요약하면 다음과 같습니다. **값을 생성하는 출력에 `serializable=False`를 표시하고 객체를 그 출력에 할당합니다. 소비하는 쪽은 아무것도 선언하지 않아도 객체를 일반적인 방식으로 읽을 수 있습니다.**

```python
class LoadPipeline(ControlNode):
    def __init__(self, **kwargs) -> None:
        super().__init__(**kwargs)
        self.add_parameter(
            Parameter(
                name="pipeline",
                output_type="Pipeline",
                tooltip="The loaded pipeline",
                serializable=False,
                allowed_modes={ParameterMode.OUTPUT},
            )
        )

    def process(self) -> None:
        self.parameter_output_values["pipeline"] = load_pipeline(...)


class Generate(ControlNode):
    def __init__(self, **kwargs) -> None:
        super().__init__(**kwargs)
        # 소비하는 쪽에서는 선언이 필요하지 않습니다.
        self.add_parameter(Parameter(name="pipeline", input_types=["Pipeline"], tooltip="Pipeline to run"))

    def process(self) -> None:
        pipeline = self.get_parameter_value("pipeline")  # 실제 객체
        ...
```

## 실제 동작 방식 (What actually happens) {: #what-actually-happens }

객체는 생성된 프로세스에 그대로 남아 있습니다. 값이 프로세스 경계를 넘어가려고 할 때 엔진은 짧은 문자열인 불투명한 **키(key)**로 값을 대체하여 전송합니다. 소비 노드가 파라미터를 읽으면 키는 다시 객체로 변환됩니다.

이 방식에서 다음 세 가지 사항을 이해해 두어야 합니다.

- **노드 자신의 딕셔너리에는 항상 실제 객체가 들어 있습니다.** `parameter_output_values["pipeline"]`에 파이프라인을 할당한 후 바로 다시 읽으면 키가 아닌 파이프라인 객체를 얻습니다. 쓰기 시에는 아무 값도 대체되지 않습니다.
- **그래프가 프로세스를 벗어나지 않으면 이 과정은 전혀 일어나지 않습니다.** 라이브러리가 실행 의존성을 선언하지 않으면 값은 이전과 같이 참조로 전달됩니다.
- **생산자만 선언합니다.** 키는 아무것도 선언하지 않은 소비자에게 연결을 따라 전달됩니다. 그래서 위 예제의 소비 파라미터는 일반 `Parameter`입니다.

## `serializable=False`의 의미 (What `serializable=False` means) {: #what-serializable-false-means }

이 설정에는 두 가지 효과가 있습니다.

- **값이 저장되지 않습니다.** 값이 저장된 워크플로우 파일에서 제외되므로, 워크플로우를 다시 열면 해당 노드가 다시 실행됩니다.
- **값이 생성된 프로세스에 남습니다.** 워커에서 실행되는 노드의 출력인 경우, 엔진은 값을 보관하고 그 자리에 키를 전송합니다. 텍스트, 숫자, 그리고 이들로 구성된 리스트나 딕셔너리 같은 일반 데이터는 그대로 전송됩니다.

직렬화할 수 없는 값(파이프라인, 텐서, 드라이버 등)인 경우에만 출력에 `serializable=False`를 표시하십시오.

## 비용이 큰 리소스를 여러 실행에서 재사용하기 (Reusing an expensive resource across runs) {: #reusing-an-expensive-resource-across-runs }

위 내용은 노드 간에 전달되는 값을 다룹니다. 한 번 빌드하여 재사용하려는 *리소스*(예: 로드에 30초가 걸리는 파이프라인)는 다시 생성할 수 있는 키가 필요합니다. 이를 위해 노드에는 다음과 같은 작은 API가 있습니다.

```python
def process(self) -> None:
    key = self.local_objects.key_for(self._config_hash())
    pipeline = self.local_objects.get(key)
    if pipeline is None:
        pipeline = build_pipeline(...)
        self.local_objects.put(pipeline, key=self._config_hash(), on_drop=release_vram)
    ...
```

| 호출                                             | 동작                                                                          |
| ------------------------------------------------ | ---------------------------------------------------------------------------- |
| `local_objects.put(value, *, key, on_drop=None)` | `value`를 지정한 키로 보관하고, 네임스페이스가 적용된 전체 키를 반환합니다                  |
| `local_objects.get(key)`                         | 객체를 반환합니다. 이 프로세스가 객체를 보관하지 않으면 `None`을 반환합니다             |
| `local_objects.key_for(suffix)`                  | 아무것도 보관하지 않고, 선택한 접미사에 대한 전체 키를 반환합니다                      |
| `local_objects.drop(key)`                        | 객체 하나를 해제합니다. 해당 객체를 보관하고 있었는지 여부를 반환합니다                 |
| `local_objects.drop_all()`                       | 라이브러리가 이 프로세스에서 보관하는 모든 객체를 해제합니다. "캐시 지우기" 노드가 호출하는 메서드입니다 |

이미 사용한 키로 `put`을 호출하면 기존에 보관된 객체를 먼저 해제합니다. 단, 같은 객체인 경우에는 해제하지 않습니다. 따라서 해시가 변경되지 않은 상태에서 다시 빌드하더라도 이전 객체가 해제되지 않은 채로 남지 않습니다.

## 객체가 점유한 자원 해제하기 (Freeing what the object holds) {: #freeing-what-the-object-holds }

Python 참조를 마지막으로 제거한다고 해서 VRAM이 해제되지는 않습니다. 파라미터에 `on_local_object_drop`을 지정하거나(또는 `put`에 `on_drop`을 전달하면), 객체가 해제될 때 엔진이 해당 함수를 호출합니다.

```python
Parameter(
    name="pipeline",
    output_type="Pipeline",
    tooltip="The loaded pipeline",
    serializable=False,
    allowed_modes={ParameterMode.OUTPUT},
    on_local_object_drop=lambda pipeline: pipeline.to("cpu"),
)
```

이 훅은 캐시에 속하므로, 캐시가 객체를 놓아주는 시점에 실행됩니다.

- **노드가 다시 실행되고** 캐시가 해당 파라미터에 대해 새 객체를 보관하는 경우. 이전에 보관하던 객체는 해제됩니다.
- **노드가 삭제되고** 해당 객체를 참조하는 다른 대상이 없는 경우.
- **라이브러리가 언로드되거나**, 모든 라이브러리가 다시 로드되는 경우.
- **워크플로우가 닫히거나 지워지는** 경우.

훅은 객체 하나당 한 번 실행됩니다. 객체 하나가 두 개의 출력에 놓여 있어도 마찬가지입니다.

이 훅이 다루지 않는 경우는 캐시에 전달되지 않은 객체입니다. 라이브러리가 실행 의존성을 선언하지 않으면 프로세스 경계가 없으므로 아무것도 캐시되지 않고, 값은 이전과 같이 참조로 전달됩니다. 이 경우 캐시가 해제할 대상이 없으며, 객체를 해제하는 작업은 이전과 같이 개발자가 직접 수행해야 합니다. 노드 실행 중에 덮어쓴 객체도 마찬가지입니다. 노드 실행이 끝났을 때 파라미터가 보유한 값만 캐시에 들어갑니다. 훅이 실행되지 않는 경우가 두 가지 더 있습니다. 라이브러리 하나를 업데이트하거나 해당 git ref를 전환해도 현재는 워커가 재시작되지 않으므로, 해당 워커가 보유한 객체가 새 코드에서도 남아 있습니다.

## 할 수 없는 작업 (What you cannot do) {: #what-you-cannot-do }

**리스트 또는 딕셔너리 파라미터는 값을 보관할 수 없습니다.** `ParameterList`와 `ParameterDictionary`는 자식 파라미터로부터 값을 구성하므로, 보관할 단일 객체가 없고 해제 훅을 둘 위치도 없습니다. 이러한 파라미터에 `serializable=False`를 선언하면 이전과 같이 리스트가 워크플로우 저장에서 제외되기는 하지만, 캐싱은 전혀 수행되지 않습니다.

일반 `Parameter`에 `serializable=False`를 표시하여 배치 전체를 출력하십시오. 텐서의 리스트는 보관 관점에서 하나의 객체로 취급되며, 이 방식은 정상적으로 동작합니다. 보관된 값을 소비하는 `ParameterList`는 사용할 수 있습니다. 각 행이 자신의 키를 가지고 있으며, 컨테이너에 대해 `get_parameter_value`를 호출하면 객체를 얻을 수 있습니다. 보관된 값에는 `get_parameter_list_value` 대신 `get_parameter_value`를 사용하십시오. `get_parameter_list_value`는 반복 가능한 모든 값을 평탄화하므로, 텐서의 리스트를 개별 행으로 분해하기 때문입니다.

**객체는 생성된 프로세스를 벗어날 수 없습니다.** 캐시는 라이브러리가 아니라 워커에 속합니다. 하나의 워커는 여러 라이브러리를 호스팅할 수 있으며, 이들은 캐시를 공유합니다. 따라서 같은 워커에 있는 다른 라이브러리가 키를 받으면 정상적으로 키를 해석합니다. 그러나 *다른* 프로세스(다른 워커 또는 오케스트레이터)에서 키를 읽는 것은 동작하지 않습니다.

> Attempted to read the value for parameter 'pipeline' on node 'Generate'. Failed
> due to: it is held in another process, and an object cannot leave the process
> that built it. Read it from a node that runs in the same place as the one that
> made it, or have that node output a saved file instead.

두 라이브러리는 둘 다 자체 실행 venv를 선언하지 않은 경우에만 같은 워커를 공유합니다. 이 사항은 그래프 작성자가 확인할 수 없습니다. 따라서 객체를 *다른* 라이브러리의 노드로 전달하는 라이브러리를 배포하는 경우, 같은 워커에 호스팅되는 것에 의존하지 마십시오. 대신 파일을 기록하고 그 경로나 URL을 전달하십시오. 자신의 라이브러리 안에서는 항상 같은 워커에 있습니다.

`put`으로 지정한 키는 워커 안에서 라이브러리 이름으로 네임스페이스가 적용됩니다. 따라서 다른 라이브러리가 같은 문자열을 자체 캐시에 사용하더라도 충돌하지 않습니다.

**입력은 캐시되지 않습니다.** 입력을 캐시하면 상대 측이 해석할 수 없는 키가 생성되기 때문입니다. 객체는 전송하는 프로세스에 있는 반면, 노드는 다른 곳에서 실행되기 때문입니다. 캐시에는 출력만 저장됩니다. 따라서 워커에서 실행되는 노드에 입력 값으로 전달된 객체는 이전과 같이 전송 방식이 처리하는 대로 전달됩니다. 해당 객체가 그곳에서 필요하다면, 객체를 생성하는 노드를 같은 라이브러리에서 실행하거나 파일로 기록하십시오.

**재로드 후에는 아무것도 보관되지 않습니다.** 키는 실행 중인 프로세스의 메모리를 참조하기 때문입니다.

## 나타날 수 있는 오류와 그 의미 (Errors you may see, and what they mean) {: #errors-you-may-see-and-what-they-mean }

| 메시지에 포함된 내용                                                                                             | 의미                                                                                                                                                                         |
| ------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `it is no longer available, which happens after the workflow is reloaded or the node that made it is re-run` | 키는 이 프로세스에서 만들어졌지만, 키가 가리키던 객체가 이후 해제되었습니다. 생성 노드를 다시 실행하십시오. 이는 의도된 동작이며, 객체를 잃어버린 것이 아닙니다. |
| `held in another process`                                                                                    | 객체를 생성한 프로세스가 아닌 다른 곳에서 객체를 읽고 있습니다. 대부분 검증 또는 값 훅에서 발생하며, 이들은 워커가 아니라 오케스트레이터에서 실행됩니다.                    |
| `nothing is connected to it`                                                                                 | 입력이 연결되지 않았습니다.                                                                                                                                                  |

두 번째 오류는 의도된 동작입니다. 키는 할당할 때마다 고유하므로, 이전 실행에서 받은 키를 가진 소비자는 새로운 객체로 조용히 해석되는 대신 연결이 끊긴 키를 발견하게 됩니다.

## 저장 및 메타데이터 (Saving and metadata) {: #saving-and-metadata }

보관된 값은 저장된 워크플로우에 기록되지 않습니다. 보관된 값을 가진 노드는 **UNRESOLVED** 상태로 다시 열리며, 생산자가 다시 실행되어 값을 교체합니다. 워크플로우 메타데이터와 이미지 사이드카는 해당 파라미터를 키를 기록하지 않고 생략된 것으로 보고합니다. 이 과정을 위해 별도로 수행할 작업은 없습니다. 이는 동일한 선언이 처리하는 결과입니다.

## 체크리스트 (Checklist) {: #checklist }

- 생산 출력에 `serializable=False`를 선언합니다. 객체를 해제하는 작업이 참조를 제거하는 것 이상을 필요로 하는 경우 `on_local_object_drop`도 지정합니다.
- 소비 파라미터는 아무것도 선언하지 않습니다.
- 생산자와 소비자는 같은 워커에서 실행됩니다. 하나의 라이브러리 안에서는 자동으로 적용됩니다.
- 값을 직렬화할 수 없는 출력에만 `serializable=False`를 표시합니다.
- 라이브러리가 실행 의존성을 선언하지 않으면 해제 훅에 의존하지 마십시오. 프로세스 안에 캐시되는 객체가 없으므로 해제되는 객체도 없습니다.
- 배치는 `ParameterList` 출력이 아닌 일반 파라미터에 지정합니다.
