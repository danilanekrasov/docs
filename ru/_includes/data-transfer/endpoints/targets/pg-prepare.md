{% list tabs %}

- {{ mpg-name }}
    
    1. Убедитесь, что мажорная версия {{ PG }} на приемнике не ниже версии на источнике.
     
    1. При трансфере из {{ PG }} [включите те же расширения](../../../../managed-postgresql/operations/extensions/cluster-extensions.md) в базе приемника, что и в базе источника.

        Если в базе источника расширения установлены в пользовательскую схему, и эти расширения используются в DDL переносимых объектов, то вручную создайте в приемнике DDL. В этих DDL обращение к функции должно быть без указания схемы. В политике очистки эндпоинта-приемника установите значение `{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.CleanupPolicy.TRUNCATE.title }}`, чтобы трансфер не удалил эти объекты.

    1. Выберите политику очистки таблиц трансфера `{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.CleanupPolicy.DROP.title }}`.

        Если вы вручную создали в приемнике DDL, используйте политику `{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.CleanupPolicy.TRUNCATE.title }}`. При политике `{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.CleanupPolicy.TRUNCATE.title }}` эти DDL не будут удалены.

    1. [Создайте пользователя](../../../../managed-postgresql/operations/cluster-users.md#adduser) с доступом к базе приемника.
    
    1. Выдайте созданному пользователю все привилегии на базу данных, схемы и переносимые таблицы:
    
        ```sql
        GRANT ALL PRIVILEGES ON DATABASE <имя_базы> TO <имя_пользователя>;
        ```
    
       Если база не пустая, то пользователь должен быть ее владельцем (owner):
    
        ```sql
        ALTER DATABASE <имя_базы> OWNER TO <имя_пользователя>;
        ```
    
       После старта трансфер подключится к приемнику от имени этого пользователя.
    1. Если в приемнике включена опция [сохранение границ транзакций](../../../../data-transfer/operations/endpoint/target/postgresql.md#additional-settings), выдайте созданному пользователю все привилегии на создание служебной таблицы `__data_transfer_lsn` в [текущей схеме](https://www.postgresql.org/docs/current/ddl-schemas.html#DDL-SCHEMAS-PATH) (обычно `public`) на приемнике:
    
       ```sql
       GRANT ALL PRIVILEGES ON SCHEMA <имя_схемы> TO <имя_пользователя>;
       ```

    1. Настройте [количество подключений пользователя](../../../../data-transfer/concepts/work-with-endpoints.md#postgresql-connection-limit) к базе данных.

- {{ PG }}
    
    1. {% include notitle [White IP list](../../configure-white-ip.md) %}
    
    1. Убедитесь, что мажорная версия {{ PG }} на приемнике не ниже версии на источнике.
    
    1. Включите те же расширения в базе приемника, что и в базе источника.
    
    1. Убедитесь, что на приемнике выбрана политика очистки `DROP таблиц трансфера`.
    
    1. Создайте пользователя:
    
        ```sql
        CREATE ROLE <имя_пользователя> LOGIN ENCRYPTED PASSWORD '<пароль>';
        ```
    
    1. Выдайте созданному пользователю все привилегии на базу данных, схемы и переносимые таблицы:
    
        ```sql
        GRANT ALL PRIVILEGES ON DATABASE <имя_базы> TO <имя_пользователя>;
        ```
    
       Если база не пустая, то пользователь должен быть ее владельцем (owner):
    
        ```sql
        ALTER DATABASE <имя_базы> OWNER TO <имя_пользователя>;
        ```
    
       После старта трансфер подключится к приемнику от имени этого пользователя.
    
    1. Если в приемнике включена опция [сохранение границ транзакций](../../../../data-transfer/operations/endpoint/target/postgresql.md#additional-settings), выдайте созданному пользователю все привилегии на создание служебной таблицы `__data_transfer_lsn` в [текущей схеме](https://www.postgresql.org/docs/current/ddl-schemas.html#DDL-SCHEMAS-PATH) (обычно `public`) на приемнике:
    
        ```sql
        GRANT ALL PRIVILEGES ON SCHEMA <имя_схемы> TO <имя_пользователя>;
        ```

    1. Настройте [количество подключений пользователя](../../../../data-transfer/concepts/work-with-endpoints.md#postgresql-connection-limit) к базе данных.

{% endlist %}

{% include [matview limits](../../pg-gp-matview.md) %}

Если определение переносимого `VIEW` содержит вызов `VOLATILE` [функции]({{ pg.docs.org }}/current/xfunc-volatility.html), то трансфер читает данные из такого `VIEW` с уровнем изоляции `READ UNCOMMITTED`. Консистентность между данными в этом `VIEW` и данными других переносимых объектов не гарантируется. Чтение из `MATERIALIZED VIEW` в определении `VIEW` равносильно вызову `VOLATILE` функции.