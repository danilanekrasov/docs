# Резервные копии в {{ mos-name }}

{{ mos-short-name }} обеспечивает автоматическое и ручное резервное копирование [индексов](./indexing.md).

## Создание резервной копии {#size}

Резервные копии создаются только на работающих кластерах.

Автоматическое резервное копирование выполняется каждый час и отключить его нельзя. Все автоматические резервные копии, а также все резервные копии, [созданные вручную](../operations/cluster-backups.md), — инкрементные, хранится только разница с предыдущей резервной копией, что позволяет экономить место в хранилище.

После создания резервная копия сжимается для дальнейшего хранения. Отображение точного размера резервной копии недоступно.

О том, как вручную создать резервную копию, читайте в разделе [Управление резервными копиями](../operations/cluster-backups.md).

## Хранение резервной копии {#storage}

Особенности хранения резервных копий в {{ mos-name }}:

* Резервные копии хранятся в объектном хранилище в виде бинарных файлов.

* Автоматические резервные копии хранятся две недели и изменить этот срок нельзя. Созданные вручную резервные копии хранятся бессрочно, до момента их удаления пользователем.

* {% include [no-quotes-no-limits](../../_includes/mdb/backups/no-quotes-no-limits.md) %}

* {% include [using-storage](../../_includes/mdb/backups/storage.md) %}


    Подробнее см. в разделе [Правила тарификации для {{ mos-name }}](../pricing.md#rules-storage).


## Проверка восстановления из резервной копии {#capabilities}

Чтобы проверить возможности резервного копирования, вы можете [восстановить кластер из любой резервной копии](../operations/cluster-backups.md#restore) и проверить целостность данных.
