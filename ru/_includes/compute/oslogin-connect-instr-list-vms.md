Получите список всех ВМ в каталоге по умолчанию:

```bash
yc compute instance list
```

Результат:

```text
+----------------------+-----------------+---------------+---------+---------------+--------------+
|          ID          |       NAME      |    ZONE ID    | STATUS  |  EXTERNAL IP  | INTERNAL IP  |
+----------------------+-----------------+---------------+---------+---------------+--------------+
| fhm0b28lgf********** | first-instance  | {{ region-id }}-a | RUNNING | 158.160.**.** | 192.168.0.8  |
| fhm9gk85nj********** | second-instance | {{ region-id }}-a | RUNNING | 51.250.**.*** | 192.168.0.12 |
+----------------------+-----------------+---------------+---------+---------------+--------------+
```