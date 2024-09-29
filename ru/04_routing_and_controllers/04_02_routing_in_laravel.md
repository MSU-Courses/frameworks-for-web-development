# Маршрутизация в Laravel

В общем, маршрутизация в Laravel работает аналогично другим фреймворкам: URL-адрес и HTTP-метод запроса сопоставляются с определённой функцией или методом в приложении.

## Директория маршрутов `routes`

Маршруты определяются в директории `routes`. В этой директории находятся различные файлы, каждый из которых предназначен для определённого типа маршрутов:

- `web.php` — для маршрутов, используемых в стандартных веб-приложениях.
- `api.php` — для маршрутов, предназначенных для API.
- `console.php` — для маршрутов, связанных с консольными командами.

На данный момент мы будем рассматривать только файл `web.php`, поскольку он применяется для работы с обычными веб-приложениями.

## Основы маршрутизации в Laravel [^1]

Простейший маршрут в Laravel принимает **URI** и **функцию**, которое выполняется при сопоставлении маршрута. **Например**:

```php
Route::get('/hello', function () {
    return 'Hello, World';
});
```

Когда пользователь отправляет запрос `GET` на URL-адрес `/hello`, в ответ он получает строку `Hello, World`.

> [!NOTE]
> Маршруты автоматически загружаются во время загрузки приложения, поэтому вам не нужно явно загружать их.

### Доступные методы маршрутизации

Маршрутизатор Laravel предоставляет несколько методов, которые соответствуют каждому HTTP-методу:

```php
Route::get($uri, $callback); // Маршрут для GET-запроса
Route::post($uri, $callback); // Маршрут для POST-запроса
Route::put($uri, $callback); // Маршрут для PUT-запроса
Route::patch($uri, $callback); // Маршрут для PATCH-запроса
Route::delete($uri, $callback); // Маршрут для DELETE-запроса
Route::options($uri, $callback); // Маршрут для OPTIONS-запроса
```

Например, если нужно создать маршрут, который будет обрабатывать `POST`-запрос, вы можете воспользоваться методом `post`:

```php
Route::post('/hello', function () {
    return 'Hello, World was created ;)';
});
```

Если необходимо зарегистрировать маршрут, который будет обрабатывать несколько HTTP-методов, можно использовать метод `match`:

```php
// Данный маршрут будет обработывать GET и POST запросы на URL-адрес /hello
Route::match(['get', 'post'], '/hello', function () {
    return 'Hello, World';
});
```

Или воспользоваться методом `any`, чтобы зарегистрировать маршрут, отвечающий на любой HTTP-запрос:

```php
// Данный маршрут будет обрабатывать любой HTTP-метод на URL-адрес /hello
Route::any('/hello', function () {
    return 'Hello, World';
});
```

> [!IMPORTANT]
> Несмотря на такую гибкость, рекомендуется явно указывать HTTP-метод с помощью методов `get`, `post`, `put`, `patch`, `delete` и `options` для более предсказуемого поведения маршрутов.


### Маршруты для перенаправления

Для перенаправления запросов с одного URL-адреса на другой можно использовать следующие методы:

- `redirect` — для **временного** перенаправления (*HTTP-код 302*)
- `permanentRedirect` — для **постоянного** перенаправления (*HTTP-код 301*)

```php
// Если пользователь отправит запрос на URL-адрес /user, он будет перенаправлен на /profile
Route::redirect('/user', '/profile');
// или
Route::permanentRedirect('/user', '/profile');
```

### Получение запроса

Часто возникает необходимость получить доступ к объекту запроса внутри обработчика маршрута.

> [!NOTE]
> **Объект запроса** — это экземпляр класса `Illuminate\Http\Request`, который содержит всю информацию о текущем HTTP-запросе, включая данные формы, заголовки, параметры запроса и т. д.

Чтобы использовать объект запроса в обработчике маршрута, его необходимо передать в качестве аргумента:

```php
use Illuminate\Http\Request;

// ...

// Получение объекта запроса и вывод заголовка User-Agent
Route::get('/hello', function (Request $request) {
    return $request->header('User-Agent'); // Получение заголовка User-Agent
});
```

> [!NOTE]
> В дальнейшем мы более подробно рассмотрим работу с объектом запроса.

### Динамические маршруты

#### Обязательные параметры

Как вы думаете, маршруты `/post/1` и `/post/2` — это разные маршруты или один и тот же? 

Ответ простой, это один и тот же маршрут, но с разными параметрами. В Laravel такие маршруты называются **динамическими**.

**Динамические маршруты** — это маршруты, содержащие параметры, передаваемые через URL. Для их создания используются фигурные скобки `{}`, которые обозначают динамический параметр.

Такой параметр называется **параметром маршрута** и передается в обработчик маршрута в виде аргумента.

**Пример**:

```php
Route::get('/post/{id}', function (int $id) {
    return 'Post ID: ' . $id;
});
```

Если пользователь отправит запрос на `/post/1`, в ответ он получит строку `Post ID: 1`.

Получив параметр в обработчике маршрута, можно выполнять различные действия, например, получать данные из базы по переданному `ID`.

Также можно использовать несколько параметров в одном маршруте:

```php
Route::get('/post/{id}/{slug}', function (int $id, string $slug) {
    return 'Post ID: ' . $id . ', Slug: ' . $slug;
});
```

Если запрос будет на `/post/1/hello-world`, в ответ придет `Post ID: 1, Slug: hello-world`.

> [!NOTE]
> Как вы могли заметить, параметры в функции **типизированы** (`int $id`). Это позволяет Laravel автоматически преобразовывать переданные значения в указанный тип и предотвращать возможные ошибки.

#### Использование `Request` с динамическими маршрутами

Для получения **объекта запроса** в обработчике маршрута необходимо передать его в качестве аргумента **перед параметрами маршрута**:

```php
Route::get('/post/{id}', function (Request $request, int $id) {
    return 'User-Agent: ' . $request->header('User-Agent') . ', Post ID: ' . $id;
});
```

#### Необязательные параметры

Иногда параметр маршрута **может быть необязательным**. В таких случаях можно указать значение по умолчанию для параметра и знак `?` после параметра маршрута.

```php
Route::get('/post/{id?}', function (int $id = 1) {
    return 'Post ID: ' . $id;
});
```

### Ограничения параметров

Рассмотрим следующий маршрут:

```php
Route::get('/post/{id}', function (int $id) {
    return 'Post ID: ' . $id;
});
```

Если пользователь отправит запрос на `/post/abc`, он получит ошибку, поскольку параметр `id` ожидает целочисленное значение (`int`).

В Laravel предусмотрен механизм **ограничения параметров**, который позволяет задать регулярное выражение для параметра маршрута, ограничивая его допустимыми значениями.

Ограничение параметра маршрута задается с помощью метода `where`:

`where($name, $expression)`, где:
- `$name` — имя параметра маршрута
- `$expression` — регулярное выражение, которому должно соответствовать значение параметра

**Пример**:

```php
Route::get('/post/{id}', function (int $id) {
    return 'Post ID: ' . $id;
})->where('id', '[0-9]+');
```

```php
Route::get('/user/{id}/{name}', function (int $id, string $name) {
    return 'User ID: ' . $id . ', Name: ' . $name;
})->where(['id' => '[0-9]+', 'name' => '[a-zA-Z]+']);
```

Теперь, если пользователь отправит запрос на `/post/abc`, он получит ошибку 404, так как параметр `id` должен содержать только цифровые значения.

> [!NOTE]
> Если параметр не соответствует ограничению, Laravel автоматически вернет **ошибку 404**.

Для часто используемых ограничений Laravel предоставляет готовые методы:

- `whereNumber($name)` — ограничивает параметр только числовыми значениями
- `whereAlpha($name)` — ограничивает параметр только буквенными значениями
- `whereAlphaNumeric($name)` — ограничивает параметр только буквенно-цифровыми значениями
- `whereUuid($name)` — ограничивает параметр только UUID-значениями
- и другие

**Пример**:

```php
Route::get('/post/{id}', function (int $id) {
    return 'Post ID: ' . $id;
})->whereNumber('id');

Route::get('/user/{id}/{name}', function (int $id, string $name) {
    return 'User ID: ' . $id . ', Name: ' . $name;
})->whereAlpha('name');
```

#### Глобальные ограничения

Для установки ограничений для всех параметров маршрутов можно использовать метод `pattern` в файле `RouteServiceProvider`, в методе `boot`:

```php
public function boot(): void
{
    Route::pattern('id', '[0-9]+');
}
```

### Именнованные маршруты

Представьте ситуацию: вы создали маршрут для страницы профиля пользователя:

```php
Route::get('/profile/{id}', function (int $id) {
    return 'User ID: ' . $id;
});
```

Вы начали использовать этот маршрут в различных частях приложения. Однако со временем вы решили изменить URL на `/user/{id}`.

Теперь вам придется обновить URL во всех местах, где этот маршрут используется, что неудобно и неэффективно. Чтобы избежать такой ситуации, в Laravel предусмотрены **именованные маршруты**.

Каждому маршруту можно присвоить уникальное имя с помощью метода `name`:

```php
Route::get('/profile/{id}', function (int $id) {
    return 'User ID: ' . $id;
})->name('profile');
```

> [!NOTE]
> В качестве имени маршрута можно использовать буквы, цифры, а также символы `-`, `_` и `.`.

#### Использование именованных маршрутов

Для генерации URL по имени маршрута используется функция `route`:

```php
// Генерация URL по имени маршрута
$url = route('about');

// Генерация URL по имени маршрута с параметрами
// В данном случае, параметр {id} будет заменен на значение 1
$url = route('profile', ['id' => 1]);
```

В целом, использование именованных маршрутов позволяет значительно упростить работу с маршрутами и избежать ошибок при изменении URL.

#### Хорошие практики наименования маршрутов

Нет определенного стандарта для именования маршрутов, но есть несколько рекомендаций:

1. **Используйте иерархическую структуру**.
   - Используйте символ `.` для создания иерархической структуру для именования маршрутов, например, для `/post/{id}` можно использовать `post.show`, `post.edit`, `post.delete`.
   - [✅] `post.show`, `post.edit`, `post.delete`, `admin.post.show`, `admin.user.create`
   - [❌] `showPost`, `editPost`, `deletePost`
2. **Используйте описательные имена**.
   - Имена маршрутов должны быть описательными и понятными, чтобы было легко понять, куда ведет маршрут.
   - [✅] `post.show`, `post.edit`, `post.delete`, `post.create`
   - [❌] `post.sfd`, `post.see`, `post.del`, `post.cr`
3. **Используйте единный стиль именования**.
   - Выберите один стиль именования и придерживайтесь его во всем проекте.
   - [✅] `post.show`, `post.edit`, `post.delete`, `post.create`
   - [❌] `postShow`, `post.edit`, `delete_post`, `postCreate`
4. **Используйте короткие имена**.
   - Используйте короткие имена, чтобы уменьшить вероятность ошибок при написании.
   - [✅] `post.show`, `post.edit`, `post.delete`, `post.create`
   - [❌] `post.show.page`, `post.edit.page`, `post.delete.page`, `post.create.page`


### Группировка маршрутов

В Laravel можно группировать маршруты для применения к ним общих настроек, таких как промежуточное ПО, префиксы, пространства имен и другие параметры.

> [!NOTE]
> Промежуточное ПО и контроллеры мы рассмотрим в следующих разделах, а пока рассмотрим пример группировки маршрутов.

#### Группировка по префиксу:

```php
Route::prefix('admin')->group(function () {
    Route::get('/dashboard', function () {
        return 'Admin Dashboard';
    });
    Route::get('/users', function () {
        return 'Admin Users';
    });
});
```

В этом примере все маршруты внутри группы будут иметь префикс `/admin`: `/admin/dashboard`, `/admin/users`.

#### Группировка по пространству имен:

```php
Route::name('post.')->group(function () {
    Route::get('/post/{id}', function (int $id) {
        return 'Post ID: ' . $id;
    })->name('show');
    Route::get('/post/{id}/edit', function (int $id) {
        return 'Edit Post ID: ' . $id;
    })->name('edit');
});
```

В этом случае все маршруты внутри группы будут иметь пространство имен `post`: `post.show`, `post.edit`.

### Список маршрутов

Для просмотра всех зарегистрированных маршрутов в приложении можно использовать команду `route:list`:

```bash
php artisan route:list
```

Часто в списке маршрутов присутствуют не только ваши собственные маршруты, но и маршруты, определённые фреймворком или сторонними пакетами. Чтобы отобразить только ваши маршруты, можно использовать флаг `--except-vendor`:

```bash
php artisan route:list --except-vendor
```
 

[^1]: Laravel Routing, laravel.com [online]. URL: https://laravel.com/docs/10.x/routing