# Изменение настроек кластера


После создания кластера {{ KF }} вы можете:

- [{#T}](#change-resource-preset).
- [{#T}](#change-disk-size).
- [Изменить настройки {{ KF }}](#change-kafka-settings).
- [Переместить кластер](#move-cluster) из текущего каталога в другой каталог.
- [Изменить группы безопасности кластера](#change-sg-set).

## Изменить класс и количество хостов {#change-resource-preset}

Вы можете изменить:
- класс и количество хостов-брокеров {{ KF }};
- класс хостов {{ ZK }}. 

{% note warning %}

Количество хостов-брокеров {{ KF }} нельзя уменьшить.

{% endnote %}

{% list tabs %}


- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить класс и количество хостов:
  
  1. Получите информацию о кластере:

     ```
     {{ yc-mdb-kf }} cluster list
     {{ yc-mdb-kf }} cluster get <имя или идентификатор кластера>
     ```

  1. Посмотрите описание команды CLI для изменения кластера:

     ```
     {{ yc-mdb-kf }} cluster update --help
     ```
 
  1. Чтобы увеличить количество хостов-брокеров, выполните команду:
  
     ```
     {{ yc-mdb-kf }} cluster update <имя или идентификатор кластера> --brokers-count <число>
     ```
     
  1. Чтобы изменить класс хоста-брокера, выполните команду:

     ```
     {{ yc-mdb-kf }} cluster update <имя или идентификатор кластера> --resource-preset <класс хоста>
     ```
  
  1. Чтобы изменить класс хоста {{ ZK }}, выполните команду:

     ```
     {{ yc-mdb-kf }} cluster update <имя или идентификатор кластера> \
     --zookeeper-resource-preset <класс хоста>
     ```


- API

  Воспользуйтесь методом API [update](../api-ref/Cluster/update.md) и передайте в запросе:
  - Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  - Список настроек, которые необходимо изменить, в параметре `updateMask` (одной строкой через запятую). Если не задать этот параметр, метод API сбросит на значения по умолчанию все настройки кластера, которые не были явно указаны в запросе.
  - Новую конфигурацию кластера в параметре `configSpec`.

{% endlist %}

## Изменить настройки хранилища {#change-disk-size}

{% note warning %}

На данный момент тип диска для кластера {{ KF }} нельзя изменить после создания.

{% endnote %}

{% list tabs %}


- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить настройки хранилища для хостов:
  
  1. Получите информацию о кластере:

     ```
     {{ yc-mdb-kf }} cluster list
     {{ yc-mdb-kf }} cluster get <имя или идентификатор кластера>
     ```

  1. Посмотрите описание команды CLI для изменения кластера:

     ```
     {{ yc-mdb-kf }} cluster update --help
     ```
 
  1. Чтобы изменить размер дисков хостов-брокеров, выполните команду:
  
     ```
     {{ yc-mdb-kf }} cluster update <имя или идентификатор кластера> --disk-size <размер диска>
     ```
     
     Если не указаны единицы размера, то используются гигабайты.

  1. Чтобы изменить размер дисков хостов {{ ZK }}, выполните команду:

     ```
     {{ yc-mdb-kf }} cluster update <имя или идентификатор кластера> \
     --zookeeper-disk-size <размер диска>
     ```
     
     Если не указаны единицы размера, то используются гигабайты.
     

- API

  Воспользуйтесь методом API [update](../api-ref/Cluster/update.md) и передайте в запросе:
  - Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  - Список настроек, которые необходимо изменить, в параметре `updateMask` (одной строкой через запятую). Если не задать этот параметр, метод API сбросит на значения по умолчанию все настройки кластера, которые не были явно указаны в запросе.
  - Новую конфигурацию кластера в параметре `configSpec`.

{% endlist %}

## Изменить настройки {{ KF }} {#change-kafka-settings}

{% list tabs %}

- Консоль управления

    1. Перейдите на страницу каталога и выберите сервис **{{ mkf-name }}**.
    1. Выберите кластер и нажмите кнопку **Редактировать** на панели сверху.
    1. Измените настройки {{ KF }}, нажав кнопку **Настроить** в блоке **Настройки Kafka**.

        Подробнее см. в разделе [Настройки {{ KF }}](../concepts/settings-list.md).

    1. Нажмите кнопку **Сохранить**.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Чтобы изменить настройки {{ KF }}:

    1. Посмотрите описание команды CLI для изменения настроек кластера:

        ```bash
        {{ yc-mdb-kf }} cluster update --help
        ```

    1. Измените [настройки {{ KF }}](../concepts/settings-list.md#cluster-settings) в команде изменения кластера (в примере приведены не все настройки):

        ```bash
        {{ yc-mdb-kf }} cluster update <имя кластера> \
           --compression-type <тип сжатия> \
           --log-flush-interval-messages <количество сообщений в логе, необходимое для их сброса на диск> \
           --log-flush-interval-ms <максимальное время хранения сообщений в памяти перед сбросом на диск>
        ```


- API

    Воспользуйтесь методом API [update](../api-ref/Cluster/update.md) и передайте в запросе:

    - Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
    - Список настроек, которые необходимо изменить, в параметре `updateMask` (одной строкой через запятую). Если не задать этот параметр, метод API сбросит на значения по умолчанию все настройки кластера, которые не были явно указаны в запросе.
    - Новые значения [настроек {{ KF }}](../concepts/settings-list.md#cluster-settings) в параметре:
        - `configSpec.kafka.kafkaConfig_2_1`, если используете {{ KF }} версии `2.1`;
        - `configSpec.kafka.kafkaConfig_2_6`, если используете {{ KF }} версии `2.6`.

{% endlist %}

## Переместить кластер {#move-cluster}

{% list tabs %}


- API

  Чтобы переместить кластер из текущего каталога в другой, воспользуйтесь методом API [move](../api-ref/Cluster/move.md) и передайте в запросе:
  - Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  - Идентификатор каталога назначения в параметре `destinationFolderId`.

{% endlist %}

## Изменить группы безопасности {#change-sg-set}

{% list tabs %}

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить список [групп безопасности](../concepts/network.md#security-groups) для кластера:

  1. Посмотрите описание команды CLI для изменения кластера:

      ```
      $ {{ yc-mdb-kf }} cluster update --help
      ```

  1. Укажите нужные группы безопасности в команде изменения кластера:

      ```
      $ {{ yc-mdb-kf }} cluster update <имя кластера>
           --security-group-ids <список групп безопасности>
      ```

- API

  Чтобы изменить список [групп безопасности](../concepts/network.md#security-groups) кластера, воспользуйтесь методом API `update` и передайте в запросе:

  - Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md).
  - Список групп в параметре `securityGroupIds`.
  - Список настроек, которые необходимо изменить, в параметре `updateMask`. Если не задать этот параметр, метод API сбросит на значения по умолчанию все настройки кластера, которые не были явно указаны в запросе.

{% endlist %}

{% note warning %}

Может потребоваться дополнительная [настройка групп безопасности](connect.md#configuring-security-groups) для подключения к кластеру.

{% endnote %}