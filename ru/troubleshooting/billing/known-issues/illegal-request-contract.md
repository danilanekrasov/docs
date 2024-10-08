# Устранение ошибки `ILLEGAL_REQUEST_CONTRACT` при формировании счета на оплату


## Описание проблемы {#issue-description}

При попытке сформировать счет на оплату на странице со свойствами платежного аккаунта отображается ошибка `ILLEGAL_REQUEST_CONTRACT`.

## Решение {#issue-resolution}

Для решения этой проблемы понадобится [создать запрос в техническую поддержку]({{ link-console-support }}). При создании запроса укажите следующую информацию:

1. Идентификатор платежного аккаунта.  Он имеет вид `d2nXXXXXXXXXXXXXXXXX`.  Его можно найти [на странице с данными по платежному аккаунту]({{ link-console-billing }}).
1. Полный текст сообщения об ошибке.
1. [HAR-файл](../../../support/create-har.md) с сохраненными результатами взаимодействия браузера с серверами {{ yandex-cloud }} при попытке формирования счета.
