# Использование базы данных в Laravel

После создания миграций можно приступить к работе с базой данных.

В Laravel мы можем напрямую работать с базой данных, используя встроенные инструменты фреймворка.

## Выполнение SQL-запросов

Для выполнения SQL-запросов в Laravel используется класс `DB`. С его помощью можно выполнять запросы таких типов, как: `select`, `insert`, `update`, `delete` и `statement` [^1].

### Запрос `select`

Пример использования:

```php
// $posts - массив объектов stdClass
$posts = DB::select('select * from posts where published = ?', [true]);

// использование нескольких условий
$posts = DB::select('select * from posts where published = ? and author_id = ?', [true, 1]);
```

Как видно, вторым параметром передаётся массив значений, которые будут подставлены вместо `?` в запросе. Это помогает защититься от SQL-инъекций. Эти значения можно получить, например, из формы или других внешних источников.

Пример использования в контроллере:

```php
<?php

class PostController extends Controller
{
    public function index(int $isPublished)
    {
        $posts = DB::select('select * from posts where published = ?', [$isPublished]);

        return view('posts.index', ['posts' => $posts]);
    }
}
```

Таким образом, вы можете безопасно и эффективно работать с базой данных через SQL-запросы в Laravel.

#### Использование именованных параметров

Вместо использования `?` в запросах можно использовать именованные параметры:

```php
$posts = DB::select('select * from posts where id = :id', ['id' => 1]);
```

Это делает код более читаемым и понятным, особенно при использовании нескольких параметров.

### Запрос `insert`

Для добавления записей в базу данных используется метод `insert`:

```php
DB::insert('insert into posts (title, content) values (?, ?)', ['New Post', 'Content']);

// или с использованием именованных параметров
DB::insert('insert into posts (title, content) values (:title, :content)', ['title' => 'New Post', 'content' => 'Content']);
```

Вторым параметром передаётся массив значений, которые подставляются в запрос. Этот подход аналогичен методу `select`, что позволяет избежать SQL-инъекций.

### Запрос `update`

Для обновления записей в базе данных используется метод `update`:

```php
DB::update('update posts set title = ? where id = ?', ['Updated Post', 1]);

// или с использованием именованных параметров
DB::update('update posts set title = :title where id = :id', ['title' => 'Updated Post', 'id' => 1]);
```

### Запрос `delete`

Для удаления записей из базы данных используется метод `delete`:

```php
DB::delete('delete from posts where id = ?', [1]);

// или с использованием именованных параметров
DB::delete('delete from posts where id = :id', ['id' => 1]);
```

### Запрос `statement`

Метод `statement` позволяет выполнять произвольные SQL-запросы, например, удаление таблиц или изменение схемы базы данных:

```php
DB::statement('drop table posts');
```

> [!NOTE]
> Этот метод полезен для выполнения запросов, которые напрямую изменяют структуру базы данных, такие как создание или удаление таблиц, индексов и т.д. 

## Использование транзакций

Laravel поддерживает транзакции, которые позволяют группировать несколько запросов в одну логическую операцию. Если один из запросов не удастся, все изменения откатятся.

Пример использования транзакции:

```php
DB::transaction(function () {
    DB::update('update posts set title = "New Title" where id = 1');
    DB::delete('delete from comments where post_id = 1');
});
```

Транзакции помогают сохранить целостность данных в случае возникновения ошибок.

## Заключение

Работа с базой данных в Laravel становится значительно проще благодаря мощным инструментам, встроенным в фреймворк.

С помощью методов класса `DB` можно легко выполнять различные SQL-запросы, такие как `select`, `insert`, `update`, и `delete`, а также безопасно управлять данными с использованием именованных параметров, что снижает риск SQL-инъекций.

Возможность выполнения произвольных SQL-запросов через метод `statement` позволяет гибко управлять структурой базы данных.

Кроме того, поддержка транзакций помогает гарантировать целостность данных при выполнении нескольких запросов. Все эти возможности делают работу с базой данных в Laravel не только удобной, но и безопасной.

[^1]: Database: Getting Started, laravel.com [online]. URL: https://laravel.com/docs/database