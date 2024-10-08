---
title: Функциональные характеристики {{ cloudml-full-name }}
description: Из статьи вы узнаете, из каких компонентов состоит сервис {{ cloudml-full-name }}.
---

# Функциональные характеристики {{ cloudml-full-name }}

Сервис {{ cloudml-full-name }} позволяет решать задачи применения больших языковых моделей, а также их дообучение, включая создание новых моделей.

Сервис {{ cloudml-name }} может быть развернут на физическом оборудовании клиента. {{ cloudml-name }} состоит из исполняемых программных компонентов, компонентов слоя хранения данных и моделей машинного обучения. Модели машинного обучения предназначены для решения различных прикладных задач. Программные компоненты обеспечивают работу этих моделей, позволяют проводить дообучение и создавать новые модели с нуля. Компоненты слоя хранения данных предназначены для хранения информации пользователей.

![architecture](../_assets/cloudai-hybrid/architecture.svg)

Исполняемые программные компоненты необходимы для различных задач:

1. Компоненты System обеспечивают функционирование сервиса (на рисунке — желтые и белые).
1. Компоненты Inference позволяют поддерживать инференс моделей (на рисунке — зеленые).
1. Компоненты Tuning необходимы для дообучения моделей (на рисунке — фиолетовые).


## Форма распространения {{ cloudml-name }} {#distribution}

{{ cloudml-name }} поставляется в виде набора программных компонентов и конфигурационных данных моделей. Компоненты слоя хранения данных не входят в поставку, их клиент должен обеспечить самостоятельно. 

Программные компоненты упакованы в Docker-образы, которые выгружаются в репозиторий образов в облаке клиента после заключения договора. Конфигурации моделей представляют собой файлы, позволяющие развернуть модели заданной конфигурации с помощью сервера инференсов. Файлы загружаются в объектное хранилище клиента в его облаке. Состав программных компонентов и конфигураций моделей в каждом случае определяется отдельно, поскольку зависит от оборудования клиента и решаемых задач.

