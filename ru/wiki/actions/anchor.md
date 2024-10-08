---
title: Как создать закладку на странице в {{ wiki-full-name }}
description: Следуя данной инструкции, вы сможете создать закладку на странице.
---

# Закладка на странице

{% include [actions-alert](../../_includes/wiki/actions-alert.md) %}

Чтобы перейти к заданной области на текущей или другой странице, используйте *якорь*:

1. Откройте страницу в режиме редактирования и добавьте в текст метку с помощью [блока `anchor`](#anchor-call). 

1. При формировании ссылки на заданное место страницы [вставьте cсылку на якорь](#add-anchor-to-link). 

При нажатии на ссылку будет выполнен переход в то место страницы, где установлена метка.

{% note info %}

Якорь нельзя использовать для перехода к строке в [динамической таблице](../create-grid.md).

{% endnote %}

## Добавить блок anchor {#anchor-call}

Чтобы добавить якорь на страницу, в нужную область текста вставьте один из вариантов кода:

```
{{anchor href="<имя_якоря>"}}
```
или 
```
{{a href="<имя_якоря>"}}
```

### Параметры блока {#anchor-params}

Параметр | Описание 
--- | --- 
`href` | Обязательный параметр. Содержит имя якоря. Не используйте в имени якоря пробел. 
`title` | Содержит заголовок якоря.
`text` | Содержит текст якоря. Отображается на странице.

{% include [note-for-headlines](../../_includes/wiki/note-for-headlines.md) %}

## Вставить ссылку на якорь {#add-anchor-to-link} 

Чтобы вставить ссылку на якорь, добавьте имя якоря справа от адреса страницы через символ `#`:

```
{{ wiki-pagename }}/#<имя_якоря>
```

Чтобы вставить ссылку на якорь на той же странице, полный адрес указывать необязательно:

```
#<имя_якоря>
```