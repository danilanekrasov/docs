Для некоторых команд требуется указание уникального идентификатора облака и каталога. Вы можете указать информацию о них в профиле или запускать такие команды с соответствующим флагом.

1. Укажите облако в профиле:

    ```bash
    yc config set cloud-id <идентификатор_облака>
    ```

    Или [запускайте команды](../cli/concepts/index.md#manage-properties) с параметром `--cloud-id`.
1. Укажите каталог в профиле:

    ```bash
    yc config set folder-id <идентификатор_каталога>
    ```

    Или [запускайте команды](../cli/concepts/index.md#manage-properties) с параметром `--folder-id`.

Все операции в этом профиле будут выполняться от имени привязанного сервисного аккаунта. Вы можете [изменить параметры профиля](../cli/operations/profile/manage-properties.md) или [сменить его](../cli/operations/profile/profile-activate.md).
