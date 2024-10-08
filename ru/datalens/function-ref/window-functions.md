---
title: Оконные функции
description: Оконные функции в {{ datalens-short-name }} вычисляются как агрегатные функции от показателей.
editable: false
sourcePath: ru/_api-ref/datalens/function-ref/window-functions.md
---

# Оконные функции
Оконные функции в {{ datalens-short-name }} вычисляются как агрегатные функции от показателей. Группировка по измерениям при этом отличается от группировки, заданной в чарте. Параметры группировки указываются при вызове функции в виде списка измерений, которые включаются в группировку (`WITHIN ...`) или исключаются из нее (`AMONG ...`).

Так как показатели — это измерения, к которым применяется агрегация, то оконные функции в {{ datalens-short-name }} можно рассматривать как агрегации агрегаций. Следует учитывать, что не для всех агрегатных функций результат агрегации частных результатов идентичен общей агрегации значений.

Например, если посчитать сумму для каждой группы, а затем сложить эти значения, то получится общая сумма во всех группах:
```
SUM(SUM(a) UNION SUM(b)) = SUM(a UNION b)
```
Если посчитать количество значений для каждой группы, а затем попробуете посчитать количество от суммы этих значений, то получится количество количеств (слагаемых в сумме), а не общее количество значений во всех группах:
```
COUNT(COUNT(a) UNION COUNT(b)) = 2
```

**Пример**

| `Category`   |   `Sales` |   `OrderCount` |   `Sales_Furniture+Technology` |   `OrderCount_Furniture+Technology` |
|:-------------|----------:|---------------:|-------------------------------:|------------------------------------:|
| Furniture    |    100000 |            350 |                         300000 |                                   2 |
| Technology   |    200000 |            650 |                         300000 |                                   2 |

Где:
* **Sales** — показатель суммы продаж в категориях;
* **Order Count** — показатель количества заказов в категориях;
* **Sales_Furniture+Technology** — общая сумма продаж во всех категориях:
  ```
  SUM_IF([Sales], [Category] = 'Furniture' TOTAL) + SUM_IF([Sales], [Category] = 'Technology' TOTAL)
  ```
* **OrderCount_Furniture+Technology** — количество значений показателя заказов:
  ```
  COUNT(COUNT_IF([Order Count], [Category] = 'Furniture' TOTAL) +  COUNT_IF([Order Count], [Category] = 'Technology' TOTAL) TOTAL)
  ```

## Ограничения использования {#usage-restrictions}

1. Оконные функции могут иметь в качестве первого аргумента только [показатели](../dataset/data-model.md#field).

   Для оконных функций `AVG_IF`, `COUNT_IF`, `SUM_IF` первым аргументом (`expression` в описании функции) всегда должен быть показатель.

   **Пример:**

   `AVG_IF([Profit], [Profit] > 5)`

   Для остальных оконных функций первым (или единственным) аргументом (`value` в описании функции) также всегда должен быть показатель.

   **Примеры:**

   * Корректная формула: `SUM(SUM([Profit]) TOTAL)`.
   * Некорректная формула: `RANK([Profit] TOTAL)`, где `[Profit]` — неагрегированное выражение.

1. В группировке оконных функций могут использоваться только [измерения](../dataset/data-model.md#field), участвующие в построении чарта. Только измерения, участвующие в построении чарта, задают [группировку при вычислении показателя](../concepts/aggregation-tutorial.md#aggregation-in-charts). Эти измерения определяют разбиение на группы и поэтому имеют фиксированные значения в каждой группе.

   Если указать измерение, не участвующее в построении чарта, то оно не будет иметь фиксированного значения — в каждой строке группы оно может быть разным. Невозможно будет определить, какое конкретно значение этого измерения должно быть выбрано при вычислении показателя. Ограничение справедливо для типов группировки `WITHIN` и `AMONG`.

   **Примеры:**

   * Корректная формула: `RANK(SUM([Profit]) WITHIN [Category])` в чарте с группировкой по измерениям `[Order Date]` и `[Category]`. Измерение `[Category]` участвует в построении чарта.
   * Допустимая формула: `RANK(SUM([Profit]) WITHIN [Category])` в чарте с группировкой по измерениям `[Order Date]` и `[City]`. Измерение `[Category]` не участвует в группировке, поэтому не будет использовано при вычислении. Результат будет равносилен применению формулы `RANK(SUM([Profit]) TOTAL)`.
   * Некорректная формула: `RANK(SUM([Profit]) AMONG [City])` в чарте с группировкой по измерениям `[Order Date]` и `[Category]`. Выполнение функции вызовет ошибку [Unknown dimension for window](../troubleshooting/errors/ERR-DS_API-FORMULA-UNKNOWN_WINDOW_DIMENSION.md).

1. В оконных функциях не поддерживается управление [уровнем детализации](./aggregation-functions.md#syntax-lod). Это доступно только для агрегатных функций.
1. Для функций, зависящих от порядка сортировки, перечисленные в `ORDER BY` поля должны участвовать в построении чарта.
1. В настройках чарта типа **Таблица** не рекомендуется включать отображение **Итого**, если при построении таблицы используется оконная функция. Это может вызвать ошибку.

## Синтаксис {#syntax}

Общий синтаксис оконных функций выглядит так:
```
<WINDOW_FUNCTION_NAME>(
    arg1, arg2, ...

    [ TOTAL
    | WITHIN dim1, dim2, ...
    | AMONG dim1, dim2, ... ]

    [ ORDER BY field1, field2, ... ]

    [ BEFORE FILTER BY filtered_field1, ... ]
)
```

Как и у обычных функций, вызов начинается с названия функции и аргументов (в данном случае — `arg1, arg2, ...`). 

### Группировка {#syntax-grouping}

За аргументами следует указание группировки окна, которое может быть одним из трех типов:
* `TOTAL` (равносильно `WITHIN` без измерений): все записи запроса попадают в одно единственное окно.
* `WITHIN dim1, dim2, ...` : записи группируются по измерениям `dim1, dim2, ...``dim1, dim2, ...`
* `AMONG dim1, dim2, ...`  : записи группируются по всем измерениям из запроса, кроме перечисленных. Например, если использовать формулу `RSUM(SUM([Sales]) AMONG dim1, dim2)` с измерениями `dim1`, `dim2`, `dim3`, `dim4` в запросе данных, то записи будут группироваться по `dim3` и `dim4`, так что эта формула будет эквивалентна `RSUM([Sales] WITHIN dim3, dim4)`.

Группировка опциональна. По умолчанию используется тип группировки `TOTAL`.

### Сортировка {#syntax-order-by}

За группировкой следует сортировка (`ORDER BY`). Она поддерживается только для функций, зависящих от порядка сортировки:

| `M*`                | `R*`                | Позиционные функции   |
|:--------------------|:--------------------|:----------------------|
| [MAVG](MAVG.md)     | [RAVG](RAVG.md)     | [LAG](LAG.md)         |
| [MCOUNT](MCOUNT.md) | [RCOUNT](RCOUNT.md) | [FIRST](FIRST.md)     |
| [MMAX](MMAX.md)     | [RMAX](RMAX.md)     | [LAST](LAST.md)       |
| [MMIN](MMIN.md)     | [RMIN](RMIN.md)     |                       |
| [MSUM](MSUM.md)     | [RSUM](RSUM.md)     |                       |

Сортировка для этих функций опциональна.

Результат функции зависит от полей и направления сортировки. Чтобы узнать, как сортировка влияет на расчеты, перейдите к описанию функции.
В сортировке допустимо использовать как измерения, так и показатели. Также поддерживается стандартный синтаксис `ASC`/`DESC` для указания направления сортировки: по нарастанию или убыванию (по умолчанию используется `ASC`):
`... ORDER BY [Date] ASC, SUM([Sales]) DESC, [Category] ...`

Перечисленные в `ORDER BY` поля дополняются списком полей из сортировки чарта.
Пример:
* Функция — `... ORDER BY [Date] DESC, [City]`.
* Чарт — сортировка по `Date` и `Category`.
* Результат сортировки — `Date` (по убыванию), `City`, `Category`.

### BEFORE FILTER BY {#syntax-before-filter-by}

Если какие-либо поля перечислены в `BEFORE FILTER BY`, то эта оконная функция будет рассчитана до фильтрации данных по этим полям.

`BEFORE FILTER BY` применяется также и ко всем вложенным оконным функциям.

Пример:
* Формула — `MAVG(RSUM([Sales]), 10 BEFORE FILTER BY [Date])`.
* Эквивалент — `MAVG(RSUM([Sales] BEFORE FILTER BY [Date]), 10 BEFORE FILTER BY [Date])`.

Не используйте конфликтующие `BEFORE FILTER BY` в запросе:
* Корректная формула: `MAVG(RSUM([Sales] BEFORE FILTER BY [Date], [Category]), 10 BEFORE FILTER BY [Date])` — функции вложены друг в друга, и (`[Date]`) является подмножеством (`[Date], [Category]`).
* Корректная формула: `MAVG(RSUM([Sales] BEFORE FILTER BY [Category]), 10 BEFORE FILTER BY [Date])` — функции вложены друг в друга, поэтому списки полей комбинируются во второй из функций.
* Корректная формула: `RSUM([Sales] BEFORE FILTER BY [Date], [Category]) - RSUM([Sales] BEFORE FILTER BY [Date])` — (`[Date]`) является подмножеством (`[Date], [Category]`).
* Некорректная формула: `RSUM([Sales] BEFORE FILTER BY [Category]) - RSUM([Sales] BEFORE FILTER BY [Date])` — функции не вложены, и ни одно из (`[Category]`) и (`[Date]`) не является подмножеством другого.

## Агрегатные функции как оконные {#aggregate-functions-as-window-functions}

Следующие агрегатные функции могут быть использованы как оконные:

| Агрегации                | Условные агрегации             |
|:-------------------------|:-------------------------------|
| [SUM](SUM_WINDOW.md)     | [SUM_IF](SUM_IF_WINDOW.md)     |
| [COUNT](COUNT_WINDOW.md) | [COUNT_IF](COUNT_IF_WINDOW.md) |
| [AVG](AVG_WINDOW.md)     | [AVG_IF](AVG_IF_WINDOW.md)     |
| [MAX](MAX_WINDOW.md)     |                                |
| [MIN](MIN_WINDOW.md)     |                                |

Для использования их оконных вариантов необходимо явно указывать группировку (в отличие от остальных оконных функций, где она опциональна).

Например, выражение `SUM([Sales]) / SUM(SUM([Sales]) TOTAL)` может быть использовано для расчета доли суммы `[Sales]` по группе к итоговой сумме `[Sales]` среди всех записей.





## [AVG](AVG_WINDOW.md)

**Синтаксис:**<br/>`AVG( value
     TOTAL | WITHIN ... | AMONG ...
   )`<br/>или<br/>`AVG( value
     TOTAL | WITHIN ... | AMONG ...
     [ BEFORE FILTER BY ... ]
   )`

Возвращает среднее арифметическое значений выражения. Работает только с числовыми типами данных.



## [AVG_IF](AVG_IF_WINDOW.md)

**Синтаксис:**<br/>`AVG_IF( expression, condition
        TOTAL | WITHIN ... | AMONG ...
      )`<br/>или<br/>`AVG_IF( expression, condition
        TOTAL | WITHIN ... | AMONG ...
        [ BEFORE FILTER BY ... ]
      )`

Возвращает среднее для всех значений, которые удовлетворяют условию `condition`. Если значения отсутствуют, то возвращается `NULL`. Работает только с числовыми типами данных.



## [COUNT](COUNT_WINDOW.md)

**Синтаксис:**<br/>`COUNT(  [ value ]
       TOTAL | WITHIN ... | AMONG ...
     )`<br/>или<br/>`COUNT(  [ value ]
       TOTAL | WITHIN ... | AMONG ...
       [ BEFORE FILTER BY ... ]
     )`

Возвращает количество элементов в заданном окне.



## [COUNT_IF](COUNT_IF_WINDOW.md)

**Синтаксис:**<br/>`COUNT_IF( expression, condition
          TOTAL | WITHIN ... | AMONG ...
        )`<br/>или<br/>`COUNT_IF( expression, condition
          TOTAL | WITHIN ... | AMONG ...
          [ BEFORE FILTER BY ... ]
        )`

Возвращает количество элементов в заданном окне, которые удовлетворяют условию `expression`.



## [FIRST](FIRST.md)

**Синтаксис:**<br/>`FIRST( value )`<br/>или<br/>`FIRST( value
       [ TOTAL | WITHIN ... | AMONG ... ]
       [ ORDER BY ... ]
       [ BEFORE FILTER BY ... ]
     )`

Возвращает значение `value` из первой строки заданного окна. См. также [LAST](LAST.md).



## [LAG](LAG.md)

**Синтаксис:**<br/>`LAG( value [ , offset [ , default ] ] )`<br/>или<br/>`LAG( value [ , offset [ , default ] ]
     [ TOTAL | WITHIN ... | AMONG ... ]
     [ ORDER BY ... ]
     [ BEFORE FILTER BY ... ]
   )`

Возвращает значение `value` из строки со смещением `offset` относительно текущей в рамках заданного окна:
- положительное значение `offset` делает смещение назад;
- отрицательное значение `offset` делает смещение вперед.

По умолчанию `offset` равно `1`.

Если значение отсутствует (`offset` ссылается на строку до первой или после последней), то возвращается значение `default` в качестве результата. Если `default` не задано, то используется `NULL`.

См. также [AGO](AGO.md) в качестве неоконной альтернативы.



## [LAST](LAST.md)

**Синтаксис:**<br/>`LAST( value )`<br/>или<br/>`LAST( value
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ ORDER BY ... ]
      [ BEFORE FILTER BY ... ]
    )`

Возвращает значение `value` из последней строки заданного окна. См. также [FIRST](FIRST.md).



## [MAVG](MAVG.md)

**Синтаксис:**<br/>`MAVG( value, rows_1 [ , rows_2 ] )`<br/>или<br/>`MAVG( value, rows_1 [ , rows_2 ]
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ ORDER BY ... ]
      [ BEFORE FILTER BY ... ]
    )`

Возвращает скользящее среднее значений по окну записей. Значение определяется порядком сортировки и аргументами:

| `rows_1`      | `rows_2`   | Окно                                                             |
|:--------------|:-----------|:-----------------------------------------------------------------|
| положительное | -          | Текущая запись и `rows_1` предшествующих.                        |
| отрицательное | -          | Текущая запись и -`rows_1` последующих.                          |
| любой знак    | любой знак | `rows_1` предшествующих записей, текущая и `rows_2` последующих. |



Аналогичное поведение у оконных функций [MSUM](MSUM.md), [MCOUNT](MCOUNT.md), [MMIN](MMIN.md), [MMAX](MMAX.md).

См. также [AVG](AVG.md), [RAVG](RAVG.md).



## [MAX](MAX_WINDOW.md)

**Синтаксис:**<br/>`MAX( value
     TOTAL | WITHIN ... | AMONG ...
   )`<br/>или<br/>`MAX( value
     TOTAL | WITHIN ... | AMONG ...
     [ BEFORE FILTER BY ... ]
   )`

Возвращает максимальное значение.

Если `value`:
- число — возвращает наибольшее число;
- дата — возвращает самую позднюю дату;
- строка — возвращает последнее значение в алфавитном порядке.




## [MCOUNT](MCOUNT.md)

**Синтаксис:**<br/>`MCOUNT( value, rows_1 [ , rows_2 ] )`<br/>или<br/>`MCOUNT( value, rows_1 [ , rows_2 ]
        [ TOTAL | WITHIN ... | AMONG ... ]
        [ ORDER BY ... ]
        [ BEFORE FILTER BY ... ]
      )`

Возвращает количество значений (не равных `NULL`) по окну записей, которое определяется порядком сортировки и аргументами:

| `rows_1`      | `rows_2`   | Окно                                                             |
|:--------------|:-----------|:-----------------------------------------------------------------|
| положительное | -          | Текущая запись и `rows_1` предшествующих.                        |
| отрицательное | -          | Текущая запись и -`rows_1` последующих.                          |
| любой знак    | любой знак | `rows_1` предшествующих записей, текущая и `rows_2` последующих. |



Аналогичное поведение у оконных функций [MSUM](MSUM.md), [MMIN](MMIN.md), [MMAX](MMAX.md), [MAVG](MAVG.md).

См. также [COUNT](COUNT.md), [RCOUNT](RCOUNT.md).



## [MIN](MIN_WINDOW.md)

**Синтаксис:**<br/>`MIN( value
     TOTAL | WITHIN ... | AMONG ...
   )`<br/>или<br/>`MIN( value
     TOTAL | WITHIN ... | AMONG ...
     [ BEFORE FILTER BY ... ]
   )`

Возвращает минимальное значение.

Если `value`:
- число — возвращает наименьшее число;
- дата — возвращает самую раннюю дату;
- строка — возвращает первое значение в алфавитном порядке.




## [MMAX](MMAX.md)

**Синтаксис:**<br/>`MMAX( value, rows_1 [ , rows_2 ] )`<br/>или<br/>`MMAX( value, rows_1 [ , rows_2 ]
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ ORDER BY ... ]
      [ BEFORE FILTER BY ... ]
    )`

Возвращает скользящий максимум значений по окну записей. Значение определяется порядком сортировки и аргументами:

| `rows_1`      | `rows_2`   | Окно                                                             |
|:--------------|:-----------|:-----------------------------------------------------------------|
| положительное | -          | Текущая запись и `rows_1` предшествующих.                        |
| отрицательное | -          | Текущая запись и -`rows_1` последующих.                          |
| любой знак    | любой знак | `rows_1` предшествующих записей, текущая и `rows_2` последующих. |



Аналогичное поведение у оконных функций [MSUM](MSUM.md), [MCOUNT](MCOUNT.md), [MMIN](MMIN.md), [MAVG](MAVG.md).

См. также [MAX](MAX.md), [RMAX](RMAX.md).



## [MMIN](MMIN.md)

**Синтаксис:**<br/>`MMIN( value, rows_1 [ , rows_2 ] )`<br/>или<br/>`MMIN( value, rows_1 [ , rows_2 ]
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ ORDER BY ... ]
      [ BEFORE FILTER BY ... ]
    )`

Возвращает скользящий минимум значений по окну записей, определяемому порядком сортировки и аргументами:

| `rows_1`      | `rows_2`   | Окно                                                             |
|:--------------|:-----------|:-----------------------------------------------------------------|
| положительное | -          | Текущая запись и `rows_1` предшествующих.                        |
| отрицательное | -          | Текущая запись и -`rows_1` последующих.                          |
| любой знак    | любой знак | `rows_1` предшествующих записей, текущая и `rows_2` последующих. |



Аналогичное поведение у оконных функций [MSUM](MSUM.md), [MCOUNT](MCOUNT.md), [MMAX](MMAX.md), [MAVG](MAVG.md).

См. также [MIN](MIN.md), [RMIN](RMIN.md).



## [MSUM](MSUM.md)

**Синтаксис:**<br/>`MSUM( value, rows_1 [ , rows_2 ] )`<br/>или<br/>`MSUM( value, rows_1 [ , rows_2 ]
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ ORDER BY ... ]
      [ BEFORE FILTER BY ... ]
    )`

Возвращает скользящую сумму значений по окну записей, которое определяется порядком сортировки и аргументами:

| `rows_1`      | `rows_2`   | Окно                                                             |
|:--------------|:-----------|:-----------------------------------------------------------------|
| положительное | -          | Текущая запись и `rows_1` предшествующих.                        |
| отрицательное | -          | Текущая запись и -`rows_1` последующих.                          |
| любой знак    | любой знак | `rows_1` предшествующих записей, текущая и `rows_2` последующих. |



Аналогичное поведение у оконных функций [MCOUNT](MCOUNT.md), [MMIN](MMIN.md), [MMAX](MMAX.md), [MAVG](MAVG.md).

См. также [SUM](SUM.md), [RSUM](RSUM.md).



## [RANK](RANK.md)

**Синтаксис:**<br/>`RANK( value [ , direction ] )`<br/>или<br/>`RANK( value [ , direction ]
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ BEFORE FILTER BY ... ]
    )`

Выполняет ранжирование значений с пропусками: возвращает порядковый номер строки при сортировке по `value`. Строки, которые соответствуют одному и тому же значению `value`, имеют одно и то же значение ранга. Если первые две строки получают ранг `1`, то ранг следующей строки (если значение `value` не совпадает) будет равен `3`. Значение `2` в этом случае пропускается.

Если `direction` равно `"desc"` или не указано, то ранжирование происходит от большего к меньшему, если `"asc"`, то от меньшего к большему. По умолчанию используется `"desc"`.

См. также [RANK_DENSE](RANK_DENSE.md), [RANK_UNIQUE](RANK_UNIQUE.md), [RANK_PERCENTILE](RANK_PERCENTILE.md).



## [RANK_DENSE](RANK_DENSE.md)

**Синтаксис:**<br/>`RANK_DENSE( value [ , direction ] )`<br/>или<br/>`RANK_DENSE( value [ , direction ]
            [ TOTAL | WITHIN ... | AMONG ... ]
            [ BEFORE FILTER BY ... ]
          )`

Выполняет ранжирование значений без пропусков: возвращает порядковый номер строки при сортировке по `value`. Строки, которые соответствуют одному и тому же значению `value`, имеют одно и то же значение ранга. Если первые две строки получают ранг `1`, то ранг следующей строки (если значение `value` не совпадает) будет равен `2`. Значения ранга не пропускаются.

Если `direction` равно `"desc"` или не указано, то ранжирование происходит от большего к меньшему, если `"asc"`, то от меньшего к большему. По умолчанию используется `"desc"`.

См. также [RANK](RANK.md), [RANK_DENSE](RANK_DENSE.md), [RANK_PERCENTILE](RANK_PERCENTILE.md).



## [RANK_PERCENTILE](RANK_PERCENTILE.md)

**Синтаксис:**<br/>`RANK_PERCENTILE( value [ , direction ] )`<br/>или<br/>`RANK_PERCENTILE( value [ , direction ]
                 [ TOTAL | WITHIN ... | AMONG ... ]
                 [ BEFORE FILTER BY ... ]
               )`

Выполняет относительное ранжирование. Возвращает дробный ранг (от `0` до `1`). Расчитывается как `(RANK(...) - 1) / (количество строк)`.

Если `direction` равно `"desc"` или не указано, то ранжирование происходит от большего к меньшему, если `"asc"`, то от меньшего к большему. По умолчанию используется `"desc"`.

См. также [RANK](RANK.md), [RANK_DENSE](RANK_DENSE.md), [RANK_UNIQUE](RANK_UNIQUE.md).



## [RANK_UNIQUE](RANK_UNIQUE.md)

**Синтаксис:**<br/>`RANK_UNIQUE( value [ , direction ] )`<br/>или<br/>`RANK_UNIQUE( value [ , direction ]
             [ TOTAL | WITHIN ... | AMONG ... ]
             [ BEFORE FILTER BY ... ]
           )`

Выполняет уникальное ранжирование. Возвращает порядковый номер строки при сортировке по `value`. Строки, которые соответствуют одному и тому же значению `value`, имеют разные значения ранга. Ни для каких двух строк значения не совпадают. Принимает все значения от `1` до значения, которое равно количеству строк.

Если `direction` равно `"desc"` или не указано, то ранжирование происходит от большего к меньшему, если `"asc"`, то от меньшего к большему. По умолчанию используется `"desc"`.

См. также [RANK](RANK.md), [RANK_DENSE](RANK_DENSE.md), [RANK_PERCENTILE](RANK_PERCENTILE.md).



## [RAVG](RAVG.md)

**Синтаксис:**<br/>`RAVG( value [ , direction ] )`<br/>или<br/>`RAVG( value [ , direction ]
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ ORDER BY ... ]
      [ BEFORE FILTER BY ... ]
    )`

Возвращает среднее арифметическое значений в рамках окна записей, определяемого аргументом `direction`:

| `direction`   | Окно                            |
|:--------------|:--------------------------------|
| `"asc"`       | От первой записи до текущей.    |
| `"desc"`      | От текущей записи до последней. |

По умолчанию используется значение `"asc"`.


Аналогичное поведение у оконных функций [RSUM](RSUM.md), [RCOUNT](RCOUNT.md), [RMIN](RMIN.md), [RMAX](RMAX.md).

См. также [AVG](AVG.md), [MAVG](MAVG.md).



## [RCOUNT](RCOUNT.md)

**Синтаксис:**<br/>`RCOUNT( value [ , direction ] )`<br/>или<br/>`RCOUNT( value [ , direction ]
        [ TOTAL | WITHIN ... | AMONG ... ]
        [ ORDER BY ... ]
        [ BEFORE FILTER BY ... ]
      )`

Возвращает количество значений в рамках окна записей, определяемого порядком сортировки и значением аргумента `direction`:

| `direction`   | Окно                            |
|:--------------|:--------------------------------|
| `"asc"`       | От первой записи до текущей.    |
| `"desc"`      | От текущей записи до последней. |

По умолчанию используется значение `"asc"`.


Аналогичное поведение у оконных функций [RSUM](RSUM.md), [RMIN](RMIN.md), [RMAX](RMAX.md), [RAVG](RAVG.md).

См. также [COUNT](COUNT.md), [MCOUNT](MCOUNT.md).



## [RMAX](RMAX.md)

**Синтаксис:**<br/>`RMAX( value [ , direction ] )`<br/>или<br/>`RMAX( value [ , direction ]
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ ORDER BY ... ]
      [ BEFORE FILTER BY ... ]
    )`

Возвращает максимальное из значений в рамках окна записей, определяемого порядком сортировки и значением аргумента `direction`:

| `direction`   | Окно                            |
|:--------------|:--------------------------------|
| `"asc"`       | От первой записи до текущей.    |
| `"desc"`      | От текущей записи до последней. |

По умолчанию используется значение `"asc"`.


Аналогичное поведение у оконных функций [RSUM](RSUM.md), [RCOUNT](RCOUNT.md), [RMIN](RMIN.md), [RAVG](RAVG.md).

См. также [MAX](MAX.md), [MMAX](MMAX.md).



## [RMIN](RMIN.md)

**Синтаксис:**<br/>`RMIN( value [ , direction ] )`<br/>или<br/>`RMIN( value [ , direction ]
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ ORDER BY ... ]
      [ BEFORE FILTER BY ... ]
    )`

Возвращает минимальное из значений в рамках окна записей, определяемого порядком сортировки и значением аргумента `direction`:

| `direction`   | Окно                            |
|:--------------|:--------------------------------|
| `"asc"`       | От первой записи до текущей.    |
| `"desc"`      | От текущей записи до последней. |

По умолчанию используется значение `"asc"`.


Аналогичное поведение у оконных функций [RSUM](RSUM.md), [RCOUNT](RCOUNT.md), [RMAX](RMAX.md), [RAVG](RAVG.md).

См. также [MIN](MIN.md), [MMIN](MMIN.md).



## [RSUM](RSUM.md)

**Синтаксис:**<br/>`RSUM( value [ , direction ] )`<br/>или<br/>`RSUM( value [ , direction ]
      [ TOTAL | WITHIN ... | AMONG ... ]
      [ ORDER BY ... ]
      [ BEFORE FILTER BY ... ]
    )`

Возвращает сумму значений в рамках окна записей, определяемого порядком сортировки и значением аргумента `direction`:

| `direction`   | Окно                            |
|:--------------|:--------------------------------|
| `"asc"`       | От первой записи до текущей.    |
| `"desc"`      | От текущей записи до последней. |

По умолчанию используется значение `"asc"`.


Аналогичное поведение у оконных функций [RCOUNT](RCOUNT.md), [RMIN](RMIN.md), [RMAX](RMAX.md), [RAVG](RAVG.md).

См. также [SUM](SUM.md), [MSUM](MSUM.md).



## [SUM](SUM_WINDOW.md)

**Синтаксис:**<br/>`SUM( value
     TOTAL | WITHIN ... | AMONG ...
   )`<br/>или<br/>`SUM( value
     TOTAL | WITHIN ... | AMONG ...
     [ BEFORE FILTER BY ... ]
   )`

Возвращает сумму всех значений выражения. Работает только с числовыми типами данных.



## [SUM_IF](SUM_IF_WINDOW.md)

**Синтаксис:**<br/>`SUM_IF( expression, condition
        TOTAL | WITHIN ... | AMONG ...
      )`<br/>или<br/>`SUM_IF( expression, condition
        TOTAL | WITHIN ... | AMONG ...
        [ BEFORE FILTER BY ... ]
      )`

Возвращает сумму всех значений выражения, которые удовлетворяют условию `condition`. Работает только с числовыми типами данных.


