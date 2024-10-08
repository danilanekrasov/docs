# Как настроить статический IP-адрес


## Описание сценария {#case-description}

Необходимо настроить статический IP-адрес для функции или {{ serverless-containers-name }}.

## Решение {#case-resolution}

Возможность привязать к облачной функции статический IP-адрес пока не предусмотрена. Функция каждый раз может выполняться на ресурсах с разными адресами, и закрепить за ней конкретный не получится.

В качестве альтернативного решения вы можете воспользоваться сервисом {{ compute-full-name }}. В нем потребуется создать виртуальную машину со статическим IP-адресом, установить среду выполнения для используемого вами языка программирования и запускать выполнение кода функции на ней. Подробнее пишем [здесь](../../../compute/operations/vm-create/create-linux-vm.md) и [здесь](../../../vpc/operations/get-static-ip.md).

{% note info %}

Вы также можете использовать API-шлюз для вызова функции через собственный домен. 

Подробнее в руководствах из нашей документации: 

* [Привязка домена к шлюзу](../../../api-gateway/operations/api-gw-domains.md);
* [Вызов функции через шлюз](../../../api-gateway/concepts/extensions/cloud-functions.md);
* [Вызов контейнера через шлюз](../../../api-gateway/concepts/extensions/containers.md).

{% endnote %}