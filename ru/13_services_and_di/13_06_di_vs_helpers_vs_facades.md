# Использование сервиса как хелпера, фасада и через Dependency Injection

Laravel предоставляет гибкие способы работы с сервисами: 
- через хелперы
- через фасады
- Dependency Injection (DI). 

Каждый из подходов имеет свои особенности и применяется в зависимости от задачи. Понимание того, как использовать сервисы различными способами, позволяет писать более гибкий и удобный в сопровождении код.

Например, встроенный в Laravel сервис аутентификации (`Auth`) можно вызывать через хелпер `auth()`, фасад `Auth` или внедрить как зависимость. Эти подходы можно применить и к вашим собственным сервисам.

## Сервис как хелпер

**Хелперы** — это глобальные функции, предоставляющие доступ к функциональности. Laravel имеет встроенные хелперы, такие как `auth()`, `route()`, `config()` и многие другие. Хелперы полезны, когда нужно быстро и компактно вызывать методы сервиса.

### Пример: `auth()` как хелпер

Хелпер `auth()` позволяет получить текущего пользователя:

```php
$user = auth()->user();
```

За этим хелпером скрывается вызов сервиса аутентификации `AuthManager`, зарегистрированного в контейнере. 

### Как сделать свой хелпер?

Вы можете создать собственный хелпер для вызова методов вашего сервиса. Например, для `CurrencyConverter`:

1. Создайте файл `app/Helpers/custom_helpers.php` (если его ещё нет).

2. Добавьте следующий код:

```php
use App\Services\CurrencyConverter;

if (!function_exists('convert_currency')) {
    function convert_currency($amount, $from, $to)
    {
        return app(CurrencyConverter::class)->convert($amount, $from, $to);
    }
}
```

3. Подключите файл в `composer.json`:

```json
"autoload": {
    "files": [
        "app/Helpers/custom_helpers.php"
    ]
}
```

4. Выполните команду:

```bash
composer dump-autoload
```

Теперь вы можете использовать хелпер `convert_currency()`:

```php
$converted = convert_currency(100, 'USD', 'EUR');
```

## Сервис как фасад

**Фасады** предоставляют статический интерфейс для доступа к сервисам через сервис-контейнер. Они упрощают вызов методов, сохраняя читаемость и компактность кода. Laravel использует фасады для доступа к таким компонентам, как `Auth`, `Cache`, `DB` и другим.

### Пример: `Auth` как фасад

Фасад `Auth` позволяет работать с аутентификацией:

```php
$user = Auth::user();
```

Под капотом фасад обращается к тому же сервису `AuthManager`, что и хелпер `auth()`.

### Как создать фасад для собственного сервиса?

1. Создайте файл фасада:

```php
namespace App\Facades;

use Illuminate\Support\Facades\Facade;

class Currency extends Facade
{
    protected static function getFacadeAccessor()
    {
        return 'App\Services\CurrencyConverter';
    }
}
```

2. Зарегистрируйте сервис в контейнере (если он ещё не зарегистрирован):

```php
$this->app->singleton(\App\Services\CurrencyConverter::class, function ($app) {
    return new \App\Services\CurrencyConverter();
});
```

3. Используйте фасад:

```php
use App\Facades\Currency;

$converted = Currency::convert(100, 'USD', 'EUR');
```

Фасады особенно удобны для часто используемых сервисов, но их избыточное использование может усложнить тестирование, так как они не позволяют легко заменять зависимости mock-объектами.

## Сервис через Dependency Injection

**Dependency Injection (DI)** — это основной способ работы с сервисами в Laravel, особенно с вашими собственными. Внедрение зависимостей делает код тестируемым и модульным. С помощью DI вы можете явно указать зависимости класса через его конструктор или методы.

### Пример: Использование `CurrencyConverter` через DI

1. Внедрите сервис в контроллер через конструктор:

```php
namespace App\Http\Controllers;

use App\Services\CurrencyConverter;

class CurrencyController extends Controller
{
    protected CurrencyConverter $converter;

    public function __construct(CurrencyConverter $converter)
    {
        $this->converter = $converter;
    }

    public function convert($amount, $from, $to)
    {
        return $this->converter->convert($amount, $from, $to);
    }
}
```

2. Теперь сервис создаётся и управляется контейнером, что упрощает тестирование и поддержку.


## Заключение

Выбор между хелпером, фасадом и Dependency Injection зависит от контекста:

- Используйте **DI** для своих сервисов, чтобы код был модульным и тестируемым.
- Применяйте **хелперы** для быстрого доступа к простым функциям.
- Используйте **фасады** для удобного вызова встроенных компонентов Laravel.

**Dependency Injection** — это лучший выбор для работы с вашими собственными сервисами, так как он обеспечивает контроль, тестируемость и гибкость. Хелперы и фасады же хороши для повышения удобства использования встроенных или утилитарных сервисов.