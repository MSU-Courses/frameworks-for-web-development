# Контроллеры в Laravel

Давайте разберем проблему, которая может возникнуть, если мы определяем все маршруты и логику обработки запросов прямо в
файле `routes/web.php`.

Представьте, что у нас есть несколько маршрутов, и вся их обработка находится в одном и том же файле.

**Пример**:

```php
use Illuminate\Http\Response;

// Список постов
Route::get('/post', function () {
    // ...
});

// Детали поста
Route::get('/post/{id}', function ($id) {
    // ...
});

// Создание поста
Route::post('/post', function () {
    // ...
});

// Обновление поста
Route::put('/post/{id}', function ($id) {
    // ...
});

// ...
```

Как видно, файл `routes/web.php` может быстро стать громоздким и трудным для поддержки, особенно если у нас много
маршрутов и сложной логики.

Для решения этой проблемы можно вынести логику обработки запросов в отдельные файлы. В Laravel для этого используются *
*контроллеры**, которые помогают организовать код и сделать его более структурированным.

## Что такое контроллеры в Laravel [^1]

> [!TIP]  
> Контроллер в MVC — это часть приложения, которая обрабатывает запросы и взаимодействует с моделью и представлением.
> Контроллер получает запрос, обрабатывает его и возвращает ответ.

**Контроллер** в Laravel — это **класс**, в котором определяются методы для обработки запросов.

Каждый метод контроллера выполняет конкретное действие при обращении к определенному маршруту. Вместо того чтобы писать
логику обработки запросов прямо в маршрутах, мы можем перенести ее в методы контроллера. Это помогает упростить
структуру кода.

Контроллеры позволяют группировать связанную логику в одном классе, что делает код более понятным и легко
поддерживаемым. Например, контроллер `PostController` будет отвечать за работу с постами, а `UserController` — за
пользователей.

> [!NOTE]  
> По умолчанию, контроллеры в Laravel находятся в папке `app/Http/Controllers`.

## Создание контроллера

Существуют два способа создать контроллер в Laravel:

1. **Ручное создание файла**:
    - Создайте файл контроллера в папке `app/Http/Controllers`.
    - Определите класс, который наследует класс `Controller`.
    - Определите методы для обработки запросов.

2. **Использование Artisan**:
    - Выполните команду `php artisan make:controller <ИмяКонтроллера>`, чтобы автоматически создать контроллер.

## Наименование класса контроллера

Класс контроллера должен иметь **суффикс** `Controller`. **Например**, если контроллер отвечает за работу с постами, его
имя должно быть `PostController`. Это помогает поддерживать единый стиль именования и улучшает читаемость кода.

### Группировка маршрутов по контроллерам

Одним из важных аспектов разработки является правильная группировка маршрутов по контроллерам. **Например**, если у вас
есть маршруты для работы с постами и категориями постов, возникает вопрос: выносить это в два разных контроллера —
`PostController` и `CategoryController`, или объединить их в один `PostController`, поскольку категории связаны с
постами.

Лучшей практикой считается разделение логики на **разные контроллеры**, такие как `PostController` для постов и
`CategoryController` для категорий. Это улучшает модульность и делает код более организованным и поддерживаемым. Каждый
контроллер отвечает за свою область ответственности (SRP — Single Responsibility Principle)[^2], что особенно важно в
больших проектах.

## Пример контроллера

Представим, что у нас определены маршруты в файле `routes/web.php`:

```php
use Illuminate\Http\Response;

Route::get('/post', function () {
    return 'List of Posts';
});

Route::get('/post/{id}', function ($id) {
    return 'Post Details: ' . $id;
});

Route::post('/post', function () {
    return 'Create Post';
});

Route::put('/post/{id}', function ($id) {
    return 'Update Post: ' . $id;
});

// ...
```

Мы можем вынести эту логику в контроллер `PostController`, чтобы сделать код более организованным.

Создадим контроллер `PostController` с помощью Artisan:

```bash
php artisan make:controller PostController
```

В файле `PostController.php` мы определим методы, которые будут обрабатывать запросы:

```php
<?php

namespace App\Http\Controllers;

class PostController extends Controller {
    public function index() {
        return 'List of Posts';
    }
    
    public function show($id) {
        return 'Post Details: ' . $id;
    }
    
    public function store() {
        return 'Create Post';
    }
    
    public function update($id) {
        return 'Update Post: ' . $id;
    }
    
    // ...
}
```

Теперь мы можем использовать этот контроллер для маршрутов. Общий синтаксис для определения маршрутов с использованием
контроллера выглядит так:  
`Route::method('/url', [Controller::class, 'controllerMethod']);`, где:

- `controllerMethod` — это метод контроллера, который обрабатывает запрос.
- `Controller::class` — имя класса контроллера.
- `/url` — URL-адрес маршрута.

Пример маршрутов с контроллером:

```php
use App\Http\Controllers\PostController;

Route::get('/post', [PostController::class, 'index']);
Route::get('/post/{id}', [PostController::class, 'show']);
Route::post('/post', [PostController::class, 'store']);
Route::put('/post/{id}', [PostController::class, 'update']);
// ...
```

Теперь при обращении к маршрутам `/post`, `/post/{id}`, `/post` (POST-запрос) и `/post/{id}` (PUT-запрос) будут
вызываться соответствующие методы контроллера `PostController`: `index`, `show`, `store` и `update`. Это упрощает
управление логикой обработки запросов и делает код более структурированным.

## Группировка маршрутов по контроллерам

Чтобы избежать повторения кода, как в предыдущем примере, где в каждом маршруте нужно было указывать контроллер и его
метод, Laravel предоставляет метод `Route::controller()`. Он позволяет сгруппировать маршруты, которые относятся к
одному контроллеру.

### Пример использования `Route::controller()`:

```php
// routes/web.php

use App\Http\Controllers\PostController;

Route::controller(PostController::class)->group(function () {
    Route::get('/post', 'index');
    Route::get('/post/{id}', 'show');
    Route::post('/post', 'store');
    Route::put('/post/{id}', 'update');
});
```

Как видно, мы упростили код, но всё ещё повторяется префикс `/post` для каждого маршрута. Для решения этой проблемы в
Laravel можно использовать метод `Route::prefix()`, который позволяет добавить префикс ко всем маршрутам в группе.

### Пример использования `Route::prefix()`:

```php
// routes/web.php

use App\Http\Controllers\PostController;

Route::controller(PostController::class)->prefix('post')->group(function () {
    Route::get('/', 'index');
    Route::get('/{id}', 'show');
    Route::post('/', 'store');
    Route::put('/{id}', 'update');
});
```

Теперь все маршруты с общим префиксом `/post` сгруппированы, и код стал чище. Следуя хорошим практикам, можно также
использовать именованные маршруты, что сделает код ещё более читаемым и удобным.

### Пример использования именованных маршрутов:

```php
// routes/web.php

use App\Http\Controllers\PostController;

Route::controller(PostController::class)->prefix('post')->group(function () {
    Route::get('/', 'index')->name('post.index');
    Route::get('/{id}', 'show')->name('post.show');
    Route::post('/', 'store')->name('post.store');
    Route::put('/{id}', 'update')->name('post.update');
});
```

Однако мы всё ещё повторяем часть имени маршрута (`post.`) в каждом методе `name()`. Чтобы избежать этого, можно
использовать метод `name()`, применённый ко всей группе маршрутов, что добавит префикс к именам маршрутов.

### Пример использования метода `name()` для группы:

```php
// routes/web.php

use App\Http\Controllers\PostController;

Route::controller(PostController::class)->prefix('post')->name('post.')->group(function () {
    Route::get('/', 'index')->name('index');
    Route::get('/{id}', 'show')->name('show');
    Route::post('/', 'store')->name('store');
    Route::put('/{id}', 'update')->name('update');
});
```

Теперь каждый маршрут получает префикс имени `post.` автоматически, и код стал ещё более оптимизированным.

### Пример генерации URL-адресов с использованием именованных маршрутов:

```php
route('post.index'); // GET /post
route('post.show', ['id' => 1]); // GET /post/1
route('post.store'); // POST /post
```

Использование группировки маршрутов по контроллерам и префиксам, а также именованных маршрутов, позволяет значительно
упростить и структурировать код, делая его более читаемым и легко поддерживаемым.

> [!TIP]  
> Порядок использования методов `Route::controller()`, `Route::prefix()`, и `Route::name()` не имеет значения. Например,
> такие комбинации, как `Route::controller()->prefix()->name()`, `Route::name()->prefix()->controller()`, или
`Route::prefix()->name()->controller()` будут работать одинаково. Однако для лучшей читаемости и логики рекомендуется
> придерживаться последовательности, которая наиболее удобна и понятна.

## Ресурсные контроллеры

При работе с ресурсами, такими как посты, категории или пользователи, часто требуется выполнять стандартный набор
операций: создание (**Create**), чтение (**Read**), обновление (**Update**) и удаление (**Delete**), что известно как
CRUD-операции.

Если не использовать ресурсные контроллеры, мы могли бы создать контроллер `PostController` и другие контроллеры для
каждого ресурса и определить методы `index`, `show`, `store`, `update` и `destroy`:

```php
use App\Http\Controllers\PostController;
use App\Http\Controllers\CategoryController;

Route::controller(PostController::class)->prefix('post')->group(function () {
    Route::get('/', 'index')->name('post.index');
    Route::get('/{id}', 'show')->name('post.show');
    Route::post('/', 'store')->name('post.store');
    Route::put('/{id}', 'update')->name('post.update');
    Route::delete('/{id}', 'destroy')->name('post.destroy');
});

Route::controller(CategoryController::class)->prefix('category')->group(function () {
    Route::get('/', 'index')->name('category.index');
    Route::get('/{id}', 'show')->name('category.show');
    Route::post('/', 'store')->name('category.store');
    Route::put('/{id}', 'update')->name('category.update');
    Route::delete('/{id}', 'destroy')->name('category.destroy');
});

// ...
```

Как видно, мы повторяем один и тот же код для каждого контроллера, что делает его громоздким и трудным в поддержке.
Чтобы упростить этот процесс, Laravel предоставляет **ресурсные контроллеры**.

**Ресурсный контроллер** — это контроллер, который содержит стандартный набор методов для работы с ресурсом. В Laravel
ресурсные контроллеры позволяют определить все необходимые методы в одном месте, а маршруты для этих методов могут быть
автоматически сгенерированы.

### Создание ресурсного контроллера

Чтобы создать ресурсный контроллер, используйте команду Artisan с опцией `--resource`:

```bash
php artisan make:controller PostController --resource
```

После выполнения команды в папке `app/Http/Controllers` будет создан файл `PostController.php` с предопределёнными
методами для работы с ресурсом.

```php
class PostController extends Controller {
    public function index() {
        // Отображение списка постов
    }

    public function create() {
        // Форма создания нового поста
    }

    public function store(Request $request) {
        // Сохранение нового поста
    }

    public function show($id) {
        // Отображение конкретного поста
    }

    public function edit($id) {
        // Форма редактирования поста
    }

    public function update(Request $request, $id) {
        // Обновление существующего поста
    }

    public function destroy($id) {
        // Удаление поста
    }
}
```

### Определение маршрутов для ресурсного контроллера

Для автоматического определения маршрутов используйте метод `Route::resource()`:

```php
use App\Http\Controllers\PostController;
use App\Http\Controllers\CategoryController;

Route::resource('post', PostController::class);
Route::resource('category', CategoryController::class);
```

### Стандартные действия ресурсного контроллера

Метод `Route::resource()` создаёт набор стандартных маршрутов для работы с ресурсом. В Laravel предусмотрено 7
стандартных действий:

| HTTP-метод | URI             | Метод контроллера | Имя маршрута | Описание                       |
|------------|-----------------|-------------------|--------------|--------------------------------|
| GET        | /post           | index             | post.index   | Отображение списка постов      |
| GET        | /post/create    | create            | post.create  | Форма создания нового поста    |
| POST       | /post           | store             | post.store   | Сохранение нового поста        |
| GET        | /post/{id}      | show              | post.show    | Отображение конкретного поста  |
| GET        | /post/{id}/edit | edit              | post.edit    | Форма редактирования поста     |
| PUT/PATCH  | /post/{id}      | update            | post.update  | Обновление существующего поста |
| DELETE     | /post/{id}      | destroy           | post.destroy | Удаление поста                 |

Используя ресурсные контроллеры, вы значительно упрощаете определение маршрутов и структурирование кода, что делает
приложение более понятным и лёгким в поддержке.

### Переименование маршрутов ресурсного контроллера

При использовании ресурсных контроллеров Laravel автоматически генерирует имена маршрутов для каждого действия. Однако
вы можете переопределить эти имена с помощью метода `names()`.

Хотя это не считается лучшей практикой, для примера рассмотрим, как это сделать:

```php
Route::resource('post', PostController::class)->names([
    // был 'post.index', стал 'post.build'
    'create' => 'post.build',
    // был 'post.store', стал 'post.save'
    'store' => 'post.save',
]);
```

В данном примере мы изменили имена маршрутов для действий `create` и `store` на `post.build` и `post.save`
соответственно.

## Singleton-контроллеры

В некоторых случаях может потребоваться использование **singleton-контроллеров**. Это актуально, когда ресурс может
иметь только один экземпляр для пользователя или всей системы. Примером может служить **профиль пользователя**: хотя
профилей может быть много, каждый пользователь имеет только один свой профиль.

Чтобы создать singleton-контроллер, используйте Artisan-команду `make:controller` с опцией `--singleton`:

```bash
php artisan make:controller ProfileController --singleton
```

Для определения маршрутов singleton-контроллера используйте метод `Route::singleton()`:

```php
use App\Http\Controllers\ProfileController;

Route::singleton('profile', ProfileController::class);
```

В этом случае будут созданы следующие маршруты:

| HTTP-метод | URI           | Метод контроллера | Имя маршрута   | Описание                     |
|------------|---------------|-------------------|----------------|------------------------------|
| GET        | /profile      | show              | profile.show   | Отображение профиля          |
| GET        | /profile/edit | edit              | profile.edit   | Форма редактирования профиля |
| PUT/PATCH  | /profile      | update            | profile.update | Обновление профиля           |

Singleton-контроллеры упрощают работу с ресурсами, которые существуют в единственном экземпляре, устраняя необходимость
передачи идентификатора ресурса в маршрутах. Это делает код более чистым и понятным.

Важно понимать, что вы можете сами вручную определять эти маршруты:

```php
use App\Http\Controllers\ProfileController;

Route::get('/profile', [ProfileController::class, 'show'])->name('profile.show');
Route::get('/profile/edit', [ProfileController::class, 'edit'])->name('profile.edit');
Route::put('/profile', [ProfileController::class, 'update'])->name('profile.update');
```

Однако использование таких методов, как `Route::resource()` или `Route::singleton()`, значительно упрощает код, особенно
когда работа с ресурсами подразумевает стандартные CRUD-операции. Вместо того чтобы повторно писать одни и те же
маршруты для каждого ресурса, вы можете автоматически генерировать их с помощью этих методов, что сокращает количество
кода и уменьшает вероятность ошибок.

[^1] Controllers, laravel.com [online].URL: https://laravel.com/docs/10.x/controllers

[^2]: The Single Responsibility Principle (SRP) of SOLID, medium.com [online].
URL: https://giovannamoeller.medium.com/the-single-responsibility-principle-srp-of-solid-eb2feed0c64b
