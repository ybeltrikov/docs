## Таблица {#table}
Таблица в YDB — это [реляционная таблица](https://en.wikipedia.org/wiki/Table_(database)), содержащая набор связанных данных, состоящий из строк и столбцов. каждая из строк представляет собой набор ячеек, предназначенных для хранения значений определенных типов в соответствии со схемой данных. Схема данных определяет имена (names) и типы (types) столбцов таблицы. Пример схемы данных показан на рисунке ниже.

![Datamodel_of_a_relational_table](../../_assets/datamodel_rtable.png)

<small>Рисунок 1 — пример схемы таблицы</small>

На рисунке 1 приведена схема таблицы ```Series``` из четырех столбцов c именами ```SeriesId```, ```ReleaseDate```, ```SeriesInfo``` и ```Title``` и типами данных ```Uint64?``` для первых двух и ```String?``` для последних. Первичным ключом объявлен столбец ```SeriesId```.

YDB использует типы данных [YQL][](../../datatypes.md), в качестве типов столбцов могут использоваться [простые типы данных YQL](../../../yql/reference/types/primitive.md) . Все столбцы могут содержать специальное значение NULL, используемое для обозначения отсутствия значения.

Таблица YDB всегда имеет один или несколько столбцов, составляющих ключ ([primary key](https://en.wikipedia.org/wiki/Unique_key)). Строки таблицы уникальны по ключу, то есть для одного значения ключа может быть не больше одной строки. Таблица YDB всегда упорядочена по ключу. Это означает, что точечное чтение по ключу, а также диапазонные запросы по ключу или префиксу ключа выполняются эффективно (фактически используя индекс). В примере выше ключевые столбцы выделены серым цветом и отмечены специальным знаком. Допускаются таблицы, состоящие только из ключевых столбцов. Таблицы без первичного ключа создавать нельзя.

Часто при проектировании схемы таблицы имеется набор полей, естественным образом подходящий для использования в качестве первичного ключа. Тем не менее, к выбору ключа нужно подходить аккуратно, чтобы не создать хотспоты.
Например, если вы вставляете в таблицу данные с монотонно возрастающим ключом, то вы будете писать в конец таблицы. А так как YDB разбивает данные таблицы по диапазонам ключей, это означает, что ваши вставки будут обрабатываться одним конкретным сервером, и перестанут использоваться основные преимущества распределенной БД.
Для того, чтобы нагрузка равномерно распределялась по различным серверам и при обработке больших таблиц не было хотспотов, рекомендуется применять: хэширование естественного ключа и использование хеша в качестве первой компоненты первичного ключа, изменение порядка следования компонент первичного ключа.

### Партиционирование таблиц {#partitioning}
Таблица в БД может быть шардирована по диапазонам значений первичного ключа. Каждый шард таблицы отвечает за свой  диапазон первичных ключей. Диапазоны ключей, обслуживаемых разными шардами, не пересекаются. Различные шарды таблицы могут обслуживаться разными серверами распределенной БД (в том числе расположенными в разных локациях), а также могут независимо друг от друга перемещаться между серверами для перебалансировки или поддержания работоспособности шарда при отказах серверов или сетевого оборудования.

При малом объеме данных таблица может состоять из одного шарда. При росте объема данных, обслуживаемых шардом, YDB автоматически разбивает увеличившийся шард на два. Разбиение происходит по медианному значению первичного ключа и основано на объеме данных: при превышении заданного порога объемом данных шарда, происходит разделение шарда на два.

Порог разделения шарда и включение/выключение автоматического разделения могут быть настроены индивидуально для каждой таблицы базы данных.

Помимо автоматического разделения предоставляется возможность создать пустую таблицу с предопределённым количеством шардов. При этом можно вручную задать точные границы разделения ключей по шардам или указать равномерное разделение на предопределённое количество шардов. В этом случае границы создадутся по первой компоненте первичного ключа. Равномерное распределение можно указать для таблиц, у которых первая компонента первичного ключа — целое число.

В схеме данных определяются следующие параметры партиционирования таблицы:

| Имя параметра | Описание  | Тип | Допустимые значения | Возможность изменения | Возможность сброса |
| ------------- | --------- | --- | ------------------- | --------------------- | ------------------ |
| ```AUTO_PARTITIONING_BY_SIZE```              | Режим автоматического партиционирования по размеру партиции  | Enum   | ```ENABLED```, ```DISABLED```                                 | Да                    | Нет                |
| ```AUTO_PARTITIONING_PARTITION_SIZE_MB```    | Предпочитаемый размер каждой партиции, в мегабайтах          | Uint64 | Натуральные числа                                             | Да                    | Нет                |
| ```AUTO_PARTITIONING_MIN_PARTITIONS_COUNT``` | Минимальное количество партиций, при достижении которого перестаёт работать автоматическое слияние партиций | Uint64 | Натуральные числа | Да                 | Нет                |
| ```AUTO_PARTITIONING_MAX_PARTITIONS_COUNT``` | Максимальное количество партиций, при достижении которого перестаёт работать автоматическое разделение партиций | Uint64 | Натуральные числа | Да             | Нет                |
| ```UNIFORM_PARTITIONS```                     | Количество партиций для равномерного начального разделения таблицы. Первая колонка первичного ключа должна иметь тип Uint64 или Uint32 | Uint64 | Натуральные числа | Нет | Нет    |
| ```PARTITION_AT_KEYS```                      | Граничные значения ключей для начального разделения на партиции. Представляется списком граничных значений, разделенных запятыми и обрамленными в скобки. Каждое граничное значение может быть либо набором значений ключевых колонок (также разделенных запятыми и обрамленными в скобки), либо единичным значением, если указываются только значения первой ключевой колонки. Примеры: ```(100, 1000)```, ```((100, "abc"), (1000, "cde"))``` | Expression | | Нет | Нет |

### Чтение в геораспределённом кластере {#read_only_replicas}
При выполнении запросов в YDB фактическое выполнение осуществляется шардами, размещёнными на доступных ресурсах кластера. В геораспределённом кластере это могут быть ресурсы, выделенные в датацентре, отличном от датацентра, в котором был получен запрос пользователя. Это увеличит время выполнения запроса из-за передачи данных по сети между датацентрами.

Иногда эта задержка не позволяет пользовательскому сервису уложиться в требуемое время ответа.

Чтобы сократить время ответа для читающих запросов, при создании в настройках таблицы можно указать необходимость запуска реплик для чтения для каждого шарда таблицы. Обращения к репликам для чтения типично происходят не покидая сети датацентра, что позволяет обеспечить время ответа в единицы миллисекунд. Ограничениями при этом является возможность выполнения только самых простых запросов чтения. Реплики шардов не имеют собственного хранилища. Доставка обновлений на фолловеры выполняется асинхронно, что может приводит к наблюдаемым аномалиям чтения устаревших данных (как правило не более чем на десятки миллисекунд, но возможно до единиц секунд в случае проблем связности в кластере).

В схеме данных определяются следующие параметры таблицы, влияющие на количество и размещение читающих реплик:

| Имя параметра | Описание  | Тип | Допустимые значения | Возможность изменения | Возможность сброса |
| ------------- | --------- | --- | ------------------- | --------------------- | ------------------ |
| ```READ_REPLICAS_SETTINGS``` | ```PER_AZ``` означает использование указанного количества реплик в каждом AZ, ```ANY_AZ``` — во всех AZ суммарно. | String | ```"PER_AZ:<count>"```, ```"ANY_AZ:<count>"``` где ```<count>``` — число реплик | Да | Нет |

### Автоматическое удаление устаревших данных (TTL) {#ttl}

YDB поддерживат функцию автоматического фонового удаления устаревших данных. В схеме данных таблицы может быть определена колонка, содержащая значение типа Datetime или Timestamp, значение которой у всех строк будет сравниваться в фоновом режиме с текущим временем. Строки, для которых текущее время стало больше чем значение в колонке с учетом заданной задержки, будут удалены.

| Имя параметра | Тип | Допустимые значения | Возможность изменения | Возможность сброса |
| ------------- | --- | ------------------- | --------------------- | ------------------ |
| ```TTL``` | Expression | ```Interval("<literal>") ON <column>``` | Да | Да |

Подробнее об удалении устаревших данных читайте в разделе [Time to Live (TTL)](../../../concepts/ttl.md)

### Фильтр Блума {#bloom-filter}

Использование [фильтра Блума](https://en.wikipedia.org/wiki/Bloom_filter) позволяет эффективнее определять отсутствие ключей в таблице при множественных точечных запросах по первичному ключу, снижая количество необходимых операций ввода-вывода с диска, ценой увеличения объема потребляемой памяти.

| Имя параметра | Тип | Допустимые значения | Возможность изменения | Возможность сброса |
| ------------- | --- | ------------------- | --------------------- | ------------------ |
| ```KEY_BLOOM_FILTER``` | Enum | ```ENABLED```, ```DISABLED``` | Да | Нет |
