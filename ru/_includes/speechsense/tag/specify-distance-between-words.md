(Опционально) В поле **{{ ui-key.yc-ui-talkanalytics.tags.words-between-key-value }}** укажите разрыв между ключевыми словами, который допустим при поиске ключевой фразы.

{% cut "Пример" %}

Тегу присвоена ключевая фраза «плохое обслуживание». Поиск этой фразы будет осуществляться по-разному в зависимости от значения поля **{{ ui-key.yc-ui-talkanalytics.tags.words-between-key-value }}**:

| Пример фразы в разговоре | Дистанция: `0` | Дистанция: `1` | Дистанция: `2` |
| ----------- | ----------- | ----------- | ----------- |
| «плохое обслуживание» | найдется | найдется | найдется |
| «плохое же обслуживание» | не найдется | найдется | найдется |
| «плохое у вас обслуживание» | не найдется | не найдется | найдется |

{% endcut %}
