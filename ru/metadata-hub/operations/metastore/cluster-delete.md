# Удаление кластера {{ metastore-full-name }}

Вы можете удалить кластер {{ metastore-name }} вместе со всеми хранимыми данными.

## Перед удалением кластера {#before-you-delete}

Отключите защиту от удаления для кластера, если она включена.

## Удалите кластер {#delete}

{% list tabs group=instructions %}

- Консоль управления {#console}

    1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_metadata-hub }}**.
    1. На левой панели выберите страницу ![image](../../../_assets/console-icons/database.svg) **{{ ui-key.yacloud.metastore.label_metastore }}**.
    1. Нажмите на значок ![image](../../../_assets/console-icons/ellipsis.svg) для нужного кластера и выберите пункт ![image](../../../_assets/console-icons/trash-bin.svg) **{{ ui-key.yacloud.mdb.cluster.overview.button_action-delete }}**.
    1. Подтвердите удаление кластера.

{% endlist %}
