# Метод CreateDomainIdentity

Создает [адрес](../../concepts/glossary.md#adress) в каталоге. Нужный каталог определяется по сервисному аккаунту, от имени которого выполняется запрос.

## Запрос {#request}

```http
POST /v2/email/identities HTTP/2
```

### Заголовки запроса {#request-headers}

{% include [api-request-headers](../../../_includes/postbox/api-request-headers.md) %}

### Тело запроса {#request-body}

```json
{
  "EmailIdentity": "<адрес>",
  "Tags": [
    {
      "Key": "<ключ_метки>",
      "Value": "<значение_метки>"
    }
  ],
  "DkimSigningAttributes": {
    "DomainSigningSelector": "<название_селектора>",
    "DomainSigningPrivateKey": "<закрытый_ключ>"
  },
  "ConfigurationSetName": "<название_конфигурации>"
}
```

#|
|| **Параметр** | **Описание** ||
|| `EmailIdentity` | **Тип**: string.

Адрес. ||
|| `Tags` | **Тип**: array.

Массив меток для адреса.

Максимальное количество — 50 меток. ||
|| `Key` | **Тип**: string.

Ключ метки.

Минимальная длина ключевого слова — 1 символ, максимальная — 63 символа. ||
|| `Value` | **Тип**: string.

Значение метки.

Минимальная длина значения метки — 0 символов (значение отсутствует), максимальная — 256 символов. ||
|| `DkimSigningAttributes` | **Тип**: object.

Объект, который содержит параметры для создания DKIM-подписи. ||
|| `DomainSigningSelector` | **Тип**: string.

Название селектора, который используется для идентификации открытого ключа в конфигурации DNS для домена.

Минимальная длина названия селектора —— 1 символ, максимальная — 63 символа. ||
|| `DomainSigningPrivateKey` | **Тип**: string.

Закрытый ключ, который используется для генерации DKIM-подписи. Закрытый ключ должен использовать 1024- или 2048-битное шифрование RSA и должен быть закодирован с использованием кодировки base64.

Минимальная длина ключа —— 1 символ, максимальная — 20480 символов. ||
|| `ConfigurationSetName` | **Тип**: string.

Название конфигурации, которую вы хотите привязать к адресу. Конфигурация должна находиться в том же каталоге, что и адрес. ||
|#

## Ответы {#responses}

### 200 OK {#200}

```json
{
  "IdentityType": "<тип_адреса>",
  "VerifiedForSendingStatus": <статус_верификации_для_отправки_писем>,
  "DkimAttributes": {
    "SigningEnabled": <использование_DKIM>,
    "Status": "<статус_поиска_DKIM>",
    "Tokens": [
      "<селектор_для_открытого_ключа>"
    ],
    "SigningAttributesOrigin": "<способ_конфигурации_DKIM>",
    "NextSigningKeyLength": "<длина_следующего_ключа>",
    "CurrentSigningKeyLength": "<длина_текущего_ключа>"
  }
}
```

#|
|| **Параметр** | **Описание** ||
|| `IdentityType` | **Тип**: string.

Тип адреса. Возможное значение — `DOMAIN`. ||
|| `VerifiedForSendingStatus` | **Тип**: boolean.

Указывает, подтверждено ли владение адресом. Возможные значения: `true` или `false`.

Вы можете отправлять электронные письма только с проверенных адресов. ||
|| `DkimAttributes` | **Тип**: object.

Объект, который содержит параметры DKIM-подписи. ||
|| `SigningEnabled` | **Тип**: boolean.

Указывает, используется ли DKIM-подпись при отправке электронной почты с этого адреса. Возможные значения: `true` или `false`. ||
|| `Status` | **Тип**: string.

Указывает, удалось ли {{ postbox-name }} обнаружить DKIM-подпись в DNS-конфигурации адреса. Возможные значения:

  * `PENDING` — поиск запущен, но сервис пока не обнаружил DKIM-подпись в DNS-конфигурации адреса.
  * `SUCCESS` — DKIM-подпись обнаружена.
  * `FAILED` — не удалось обнаружить DKIM-подпись.
  * `TEMPORARY_FAILURE` — из-за временной проблемы {{ postbox-name }} не может обнаружить DKIM-подпись.
  * `NOT_STARTED` — поиск не был запущен. ||
|| `Tokens` | **Тип**: array.

Список селекторов для идентификации открытого ключа. Тип для селектора: string. ||
|| `SigningAttributesOrigin` | **Тип**: string.

Указывает, как была настроена DKIM-подпись. Значение `EXTERNAL` говорит о том, что  DKIM-подпись настроена с помощью BYODKIM. ||
|| `NextSigningKeyLengths` | **Тип**: string.

Используется только для совместимости с AWS. ||
|| `CurrentSigningKeyLength` | **Тип**: string.

Используется только для совместимости с AWS. ||
|#

Ответ `200 OK` может содержать дополнительные параметры. Он не возвращается строго в формате, указанном выше.

### Ошибки {#errors}

{% include [api-errors](../../../_includes/postbox/api-errors.md) %}

Возможные ошибки:

#|
|| **Код ошибки** | **Описание** ||
|| `400 BadRequestException` | В запросе переданы неправильные заголовки или параметры. ||
|| `404 NotFoundException` | Не найден запрашиваемый ресурс. ||
|| `409 AlreadyExistsException` | Конфигурация с таким названием уже существует, выберите другие имя. ||
|| `409 ConcurrentModificationException` | Конфликт операций. Дождитесь завершения предыдущей операции и повторите запрос. ||
|| `429 TooManyRequestsException` | При вызове запроса превышена [квота](../../concepts/limits.md#postbox-quotas). ||
|| `429 LimitExceededException` | При вызове запроса превышен [лимит](../../concepts/limits.md). ||
|#
