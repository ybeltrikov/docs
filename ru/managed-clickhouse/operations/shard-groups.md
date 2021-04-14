# Управление группами шардов

Вы можете сгруппировать несколько [шардов](../concepts/sharding.md) кластера {{ CH }} в _группу шардов_ и затем размещать таблицы в этой группе.

## Получить список групп шардов в кластере {#list-shard-groups}

{% list tabs %}

  - Консоль управления

    1. Перейдите на страницу каталога и выберите сервис **{{ mch-name }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку **Группы шардов**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы получить список групп шардов в кластере, выполните команду:

  ```
  $ {{ yc-mdb-ch }} shard-groups list
       --cluster-name=<имя кластера>
  ```

  Имя кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- API

  Воспользуйтесь методом API [listShardGroups](../api-ref/Cluster/listShardGroups.md): передайте идентификатор требуемого кластера в параметре `clusterId` запроса.

  Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).

{% endlist %}

## Получить детальную информацию о группе шардов {#get-shard-group}

{% list tabs %}

  - Консоль управления

    1. Перейдите на страницу каталога и выберите сервис **{{ mch-name }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку **Группы шардов**.
    1. Выберите группу шардов для просмотра детальной информации о ней.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы получить детальную информацию о группе шардов в кластере, выполните команду:

  ```
  $ {{ yc-mdb-ch }} shard-groups get
       --cluster-name=<имя кластера>
       --name=<имя группы шардов>
  ```

  Имя кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- API

  Воспользуйтесь методом API [getShardGroup](../api-ref/Cluster/getShardGroup.md) и передайте в запросе:
  - Идентификатор требуемого кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  - Имя группы шардов в параметре `shardGroupName`. Чтобы узнать имя, [получите список групп шардов](#list-shard-groups) в кластере.

{% endlist %}

## Создать группу шардов {#create-shard-group}

{% list tabs %}

  - Консоль управления

    1. Перейдите на страницу каталога и выберите сервис **{{ mch-name }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку **Группы шардов**.
    1. Нажмите кнопку **Создать группу шардов**.
    1. Заполните поля формы и нажмите кнопку **Применить**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы создать группу шардов в кластере, выполните команду:

  ```
  $ {{ yc-mdb-ch }} shard-groups create
       --cluster-name=<имя кластера>
       --name=<имя группы шардов>
       --description=<описание группы шардов>
       --shards=<список имен шардов, которые нужно включить в группу>
  ```

  Имя кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

  Имена шардов можно запросить со [списком шардов в кластере](shards.md#list-shards).

- Terraform

    Чтобы создать группу шардов:

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

    1. Добавьте к описанию кластера {{ mch-name }} блок описания группы шардов `shard_group`.

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<имя кластера>" {
          ...
          shard_group {
            name        = "<имя группы шардов>"
            description = "<необязательное описание группы шардов>"
            shard_names = [
              # Список шардов, входящих в группу
              "<имя шарда 1>",
              ...
              "<имя шарда N>"
            ]
          }
        }
        ```

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/mdb_clickhouse_cluster).

- API

  Воспользуйтесь методом API [createShardGroup](../api-ref/Cluster/createShardGroup.md) и передайте в запросе:
  - Идентификатор кластера, в котором требуется создать группу, в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  - Имя группы шардов в параметре `shardGroupName`.
  - Список имен шардов, которые требуется включить в группу, в параметре `shardNames`. Чтобы узнать имена, [получите список шардов](shards.md#list-shards) в кластере.
  - При необходимости, описание группы шардов в параметре `description`.

{% endlist %}

## Изменить группу шардов {#update-shard-group}

{% list tabs %}

  - Консоль управления

    1. Перейдите на страницу каталога и выберите сервис **{{ mch-name }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку **Группы шардов**.
    1. Нажмите значок ![image](../../_assets/dots.svg) для нужной группы шардов и выберите пункт **Редактировать**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить группу шардов в кластере, выполните команду:

  ```
  $ {{ yc-mdb-ch }} shard-groups update
       --cluster-name=<имя кластера>
       --name=<имя группы шардов>
       --description=<новое описание группы шардов>
       --shards=<новый список имен шардов, которые нужно включить в группу>
  ```

  Эта команда заменяет существующий список шардов в группе новым, который был передан команде в параметре `--shards`. Перед выполнением команды убедитесь, что вы включили в новый список все необходимые шарды.

  Имя кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

  Имя группы шардов можно запросить со [списком групп шардов в кластере](#list-shard-groups).

  Имена шардов можно запросить со [списком шардов в кластере](shards.md#list-shards).

- Terraform

    Чтобы изменить группу шардов:

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

    1. Измените в описании кластера {{ mch-name }} блок `shard_group` с нужной группой шардов.

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<имя кластера>" {
          ...
          shard_group {
            name        = "<новое имя группы шардов>"
            description = "<новое описание группы шардов>"
            shard_names = [
              # Новый список входящих в группу шардов
              "<имя шарда 1>",
              ...
              "<имя шарда N>"
            ]
          }
        }
        ```

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/mdb_clickhouse_cluster).

- API

  Воспользуйтесь методом API [updateShardGroup](../api-ref/Cluster/updateShardGroup.md) и передайте в запросе:
  - Идентификатор кластера, в котором требуется изменить группу, в параметре `clusterId`.  Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  - Имя группы шардов в параметре `shardGroupName`. Чтобы узнать имя, [получите список групп шардов](#list-shard-groups) в кластере.
  - При необходимости, новое описание группы шардов в параметре `description`.
  - При необходимости, новый список имен шардов, которые требуется включить в группу, в параметре `shardNames`. Чтобы узнать имена, [получите список шардов](shards.md#list-shards) в кластере. Этот список заменит собой текущий: убедитесь, что вы включили в новый список все необходимые шарды.
  - Имена изменяемых параметров в параметре `updateMask`. Если не задать этот параметр, то метод API заменит все настройки группы шардов на значения по умолчанию.

{% endlist %}

## Удалить группу шардов {#delete-shard-group}

Удаление группы шардов не затрагивает входящие в нее шарды — они остаются в кластере.

Таблицы, созданные поверх удаляемой группы, остаются, но становятся неработоспособными: попытки выполнить запрос к ним приведут к ошибкам. Однако такие таблицы можно удалить до или после удаления группы шардов.

{% list tabs %}

  - Консоль управления

    1. Перейдите на страницу каталога и выберите сервис **{{ mch-name }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку **Группы шардов**.
    1. Нажмите значок ![image](../../_assets/dots.svg) для нужной группы шардов и выберите пункт **Удалить**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы удалить группу шардов в кластере, выполните команду:

  ```
  $ {{ yc-mdb-ch }} shard-groups delete
       --cluster-name=<имя кластера>
       --name=<имя группы шардов>
  ```

  Имя кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

  Имя группы шардов можно запросить со [списком групп шардов в кластере](#list-shard-groups).

- Terraform

    Чтобы удалить группу шардов:

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

    1. Удалите из описания кластера {{ mch-name }} блок описания нужной группы шардов `shard_group`.

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите удаление ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/mdb_clickhouse_cluster).

- API

  Чтобы удалить группу шардов в кластере, воспользуйтесь методом API [deleteShardGroup](../api-ref/Cluster/deleteShardGroup.md) и передайте в запросе:
  - Идентификатор кластера, из которого требуется удалить группу, в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  - Имя группы шардов в параметре `shardGroupName`. Чтобы узнать имя, [получите список групп шардов](#list-shard-groups) в кластере.

{% endlist %}