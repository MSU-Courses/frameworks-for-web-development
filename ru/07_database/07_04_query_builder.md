# Построитель запросов в Laravel

В предыдущей главе мы рассмотрели работу с базой данных с помощью SQL-запросов. Однако в Laravel можно использовать специальный инструмент для создания запросов — **построитель запросов** (query builder) [^1].

Построитель запросов — это удобный конструктор, который позволяет пошагово создавать запросы к базе данных.

Начинается все с основного элемента — таблицы, к которой мы хотим обратиться. Далее, как строительные блоки, добавляются различные операции: выборка, фильтрация, сортировка, группировка, объединение таблиц и т.д. В результате мы получаем готовый запрос.

## Получение данных

Для извлечения данных из таблицы используется метод `table`:

```php
$users = DB::table('posts')->get();
// Сформированный запрос: select * from posts

// можно было бы использовать метод select('*')
DB::select('select * from posts');
```

- `table` — это основа, с которой начинается запрос, в данном случае мы выбираем таблицу `posts`.
- `get` возвращает все строки из таблицы в виде массива **объектов**.

Использование Query Builder делает код более читаемым, понятным, безопасным и гибким,— упрощает динамическое формирование запросов.

## Выборка данных

Для выборки определенных столбцов из таблицы используется метод `select`:

```php
$users = DB::table('posts')
            ->select('title', 'content as text')
            ->get();
// Сформированный запрос: select title, content as text from posts
```

- `select` позволяет указать столбцы, которые необходимо выбрать.

Для исключения дубликатов используется метод `distinct`:

```php
$users = DB::table('posts')
            ->select('title')
            ->distinct()
            ->get();
// Сформированный запрос: select distinct title from posts
```

### Добавление select

Для добавления дополнительных столбцов к уже существующим используется метод `addSelect`:

```php
// Сформированный запрос: select title, content from posts
$query = DB::table('posts')
            ->select('title');

// Если передан параметр getContent, добавляем столбец content
if ($request->has('getContent')) {
      $query->addSelect('content');
}

// Если передан параметр getContent, то запрос будет
//    select title, content from posts
// Если параметр не передан, то запрос будет
//    select title from posts
$posts = $query->get();
```

## Фильтрация данных

Для фильтрации данных используется метод `where`:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->get();
// В данном случае используется оператор равенства (=)
// Сформированный запрос: select * from posts where id = 1
```

- `where` позволяет указать условие для фильтрации данных.

Если необходимо добавить знак сравнения, например, `>=`, используется второй параметр:

```php
$users = DB::table('posts')
            ->where('id', '>=', 1)
            ->get();
// Сформированный запрос: select * from posts where id >= 1
```

Можно использовать любой оператор сравнения: `=`, `>`, `<`, `>=`, `<=`, `<>`, `LIKE`, `BETWEEN`, `IN`, `NOT IN`.

```php
$users = DB::table('posts')
            ->where('title', 'like', 'New%')
            ->get();
// Сформированный запрос: select * from posts where title like 'New%'
```

### Множественные условия

Для добавления нескольких условий используется метод `where` несколько раз:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->where('title', 'New Post')
            ->get();
// Сформированный запрос: select * from posts where id = 1 and title = 'New Post'
```

Или можно использовать метод `where` с массивом:

```php
$users = DB::table('posts')
            ->where([
                ['id', 1],
                ['title', 'New Post']
            ])
            ->get();
// Сформированный запрос: select * from posts where id = 1 and title = 'New Post'
```

> [!TIP]
> При использовании метода `where` несколько раз, условия объединяются оператором `AND`.

### Метод `orWhere`

Для использования оператора `OR` используется метод `orWhere`:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->orWhere('title', 'New Post')
            ->get();
// Сформированный запрос: select * from posts where id = 1 or title = 'New Post'
```

В функцию `orWhere` можно передать функцию для объединения условий:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->orWhere(function($query) {
                $query->where('title', 'New Post')
                      ->where('content', 'Content');
            })
            ->get();
// Сформированный запрос: select * from posts where id = 1 or (title = 'New Post' and content = 'Content')
```

### Методы `whereNot`, `whereAny`, `whereAll`

Для использования оператора `NOT` используется метод `whereNot`:

```php
$users = DB::table('posts')
            ->whereNot('id', 1)
            ->get();
// Сформированный запрос: select * from posts where id != 1
```

Для использования оператора `OR` с несколькими значениями используется метод `whereAny`:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->whereAny([
               'title',
               'description'
            ], 'like', 'New%')
            ->get();
```

**Сформированный запрос**:

```sql
select *
from posts
where id = 1 and (
   title like 'New%' or
   description like 'New%'
);
```

Для использования оператора `AND` с несколькими значениями используется метод `whereAll`:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->whereAll([
               'title',
               'description'
            ], 'like', 'New%')
            ->get();
```

**Сформированный запрос**:

```sql
select *
from posts
where id = 1 and (
   title like 'New%' and
   description like 'New%'
);
```

### Метод `whereBetween`

Для фильтрации данных в диапазоне используется метод `whereBetween`:

```php
$users = DB::table('posts')
            ->whereBetween('id', [1, 10])
            ->get();
// Сформированный запрос: select * from posts where id between 1 and 10
```

### Метод `whereIn`

Для фильтрации данных по списку значений используется метод `whereIn`:

```php
$users = DB::table('posts')
            ->whereIn('id', [1, 2, 3])
            ->get();
// Сформированный запрос: select * from posts where id in (1, 2, 3)
```

Метод `whereIn` можно использовать для вложенных запросов:

```php
$query = DB::table('users')->select('id')
               ->from('posts')
               ->where('id', 1);

$posts = DB::table('posts')
            ->whereIn('user_id', $query)
            ->get();
```

**Сформированный запрос**:

```sql
select *
from posts
where user_id in (
   select id
   from users
   from posts
   where id = 1
);
```

## Сортировка данных

### Метод `orderBy`

Для сортировки данных используется метод `orderBy`:

```php
$users = DB::table('posts')
            ->orderBy('title', 'desc')
            ->get();
// Сформированный запрос: select * from posts order by title desc
```

- `orderBy` позволяет указать столбец и направление сортировки.

### Метод `latest`, `oldest`

Для сортировки по дате создания используются методы `latest` и `oldest`:

```php
$users = DB::table('posts')
            ->latest()
            ->get();
// Сформированный запрос: select * from posts order by created_at desc
```

```php
$users = DB::table('posts')
            ->oldest()
            ->get();
// Сформированный запрос: select * from posts order by created_at asc
```

Для выборки первой или последней строки используются методы `first` и `last`:

```php
$user = DB::table('posts')
            ->first();
// Сформированный запрос: select * from posts limit 1
```

```php
$user = DB::table('posts')
            ->last();
// Сформированный запрос: select * from posts order by id desc limit 1
```

### Метод `inRandomOrder`

Для случайной сортировки используется метод `inRandomOrder`:

```php
$users = DB::table('posts')
            ->inRandomOrder()
            ->get();
// Сформированный запрос: select * from posts order by rand()
```

## Группировка данных

Для группировки данных используется метод `groupBy`:

```php
$users = DB::table('posts')
            ->select('user_id, count(*) as total')
            ->groupBy('user_id')
            ->get();
// Сформированный запрос: select user_id, count(*) as total from posts group by user_id
```

## Ограничение и смещение данных

Для ограничения количества возвращаемых строк используется метод `limit`:

```php
$users = DB::table('posts')
            ->limit(10)
            ->get();
// Сформированный запрос: select * from posts limit 10
```

Для смещения начала выборки используется метод `offset`:

```php
$users = DB::table('posts')
            ->offset(10)
            ->get();
// Сформированный запрос: select * from posts offset 10
```

Для выбора первой и последней строки используются методы `first` и `last`:

```php
$user = DB::table('posts')
            ->first();
// Сформированный запрос: select * from posts limit 1
```

```php
$user = DB::table('posts')
            ->last();
// Сформированный запрос: select * from posts order by id desc limit 1
```

## Вставка данных

Для вставки данных в таблицу используется метод `insert`:

```php
DB::table('posts')
    ->insert([
        'title' => 'New Post',
        'content' => 'Content'
    ]);
```

- `insert` принимает массив данных для вставки.

### Получение идентификатора вставленной строки

Если мы хотим получить идентификатор вставленной строки, используется метод `insertGetId`:

```php
$id = DB::table('posts')
        ->insertGetId([
            'title' => 'New Post',
            'content' => 'Content'
        ]);
```

## Обновление данных

Для обновления данных в таблице используется метод `update`:

```php
DB::table('posts')
    ->where('id', 1)
    ->update([
        'title' => 'Updated Post',
        'content' => 'Updated Content'
    ]);
```

- `update` принимает массив данных для обновления.

## Удаление данных

Для удаления данных из таблицы используется метод `delete`:

```php
DB::table('posts')
    ->where('id', 1)
    ->delete();
```

- `delete` удаляет строки, удовлетворяющие условию.

## Обновление или вставка данных

Бывают случаи, когда необходимо обновить данные, если они существуют, или вставить новые, если их нет. Для этого используется метод `updateOrInsert`:

```php
DB::table('posts')
    ->updateOrInsert(
        ['title' => 'New Post'],
        ['content' => 'Content']
    );
```

## Инкремент и декремент

Иногда необходимо увеличить или уменьшить значение столбца на определенное число. Для этого используются методы `increment` и `decrement`:

```php
DB::table('posts')
    ->where('id', 1)
    ->increment('views');
```

```php
DB::table('posts')
    ->where('id', 1)
    ->decrement('views');
```

## Отладка

Для отладки запросов можно использовать встроенную панель отладки Laravel. Для этого необходимо включить режим отладки в файле `.env`:

```ini
APP_DEBUG=true
```

Или использовать следующие методы:

```php
// Получить сформированный SQL-запрос
$sql = DB::table('posts')->dd();

// Получить сформированный SQL-запрос со вставленными параметрами
$sql = DB::table('posts')->where('id', 5)->orderBy('title')->ddRawSql();
```

> [!NOTE]
> В данной главе мы рассмотрели основные методы построителя запросов в Laravel. Для более сложных запросов, таких как объединение таблиц, использование подзапросов и т.д., рекомендуется изучить документацию Laravel.

## Заключение

Построитель запросов в Laravel является мощным и удобным инструментом для работы с базой данных, позволяющим разработчикам создавать гибкие и безопасные SQL-запросы с минимальными усилиями.

Использование query builder не только делает код более читаемым и понятным, но и позволяет динамически строить сложные запросы, избегая риска SQL-инъекций.

С его помощью можно легко выполнять выборку, фильтрацию, сортировку, группировку, вставку, обновление и удаление данных, не прибегая к явному написанию SQL.

Это значительно упрощает разработку приложений, обеспечивая при этом высокую производительность и безопасность работы с базами данных.

[^1]: Database: Query Builder, laravel.com [online]. URL: https://laravel.com/docs/queries