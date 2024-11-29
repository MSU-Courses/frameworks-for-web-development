# Практическая часть: Сервис для преобразования валют

В этой части мы создадим сервис — **CurrencyConverter**, который будет использоваться для преобразования валют.

Контроллер будет выполнять минимальную роль: обрабатывать запросы и делегировать логику сервису.

## Шаг 1: Создание сервиса

Создайте директорию `app/Services`, если её ещё нет. В этой директории создайте файл `CurrencyConverter.php`.

### Код: `app/Services/CurrencyConverter.php`

```php
<?php

namespace App\Services;

class CurrencyConverter
{
    protected array $rates;

    public function __construct()
    {
        // Пример фиксированных курсов валют
        // Данные можно получать из стороннего API или базы данных
        $this->rates = [
            'USD' => 1.0,
            'EUR' => 0.85,
            'GBP' => 0.75,
        ];
    }

    /**
     * Конвертирует сумму из одной валюты в другую.
     *
     * @param float $amount Сумма для конвертации
     * @param string $from Исходная валюта
     * @param string $to Целевая валюта
     * @return float Конвертированная сумма
     * @throws \Exception Если валюта не поддерживается
     */
    public function convert(float $amount, string $from, string $to): float
    {
        if (!isset($this->rates[$from]) || !isset($this->rates[$to])) {
            throw new \Exception("Currency not supported: {$from} or {$to}");
        }

        $amountInBase = $amount / $this->rates[$from];
        return $amountInBase * $this->rates[$to];
    }
}
```

### Шаг 2: Регистрация сервиса в `Service Container`

Зарегистрируйте сервис в методе `register` класса `AppServiceProvider`.

### Код: `app/Providers/AppServiceProvider.php`

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Services\CurrencyConverter;

class AppServiceProvider extends ServiceProvider
{
    public function register()
    {
        // Регистрация CurrencyConverter как singleton
        // потому, что он не хранит состояние и не зависит от других сервисов.
        // Это позволяет использовать один экземпляр сервиса во всем приложении.
        $this->app->singleton(CurrencyConverter::class, function ($app) {
            return new CurrencyConverter();
        });
    }

    public function boot()
    {
        //
    }
}
```

## Шаг 3: Использование сервиса в контроллере

Создайте контроллер, который будет использовать `CurrencyConverter`.

### Создание контроллера

Создайте контроллер командой Artisan:

```bash
php artisan make:controller CurrencyController
```

### Код: `app/Http/Controllers/CurrencyController.php`

```php
<?php

namespace App\Http\Controllers;

use App\Services\CurrencyConverter;
use Illuminate\Http\Request;

class CurrencyController extends Controller
{
    protected CurrencyConverter $currencyConverter;

    public function __construct(CurrencyConverter $currencyConverter)
    {
        $this->currencyConverter = $currencyConverter;
    }

    /**
     * Обрабатывает запрос на конвертацию валют.
     */
    public function convert(CurrencyRequest $request)
    {
        $validated = $request->validated();

        try {
            $convertedAmount = $this->currencyConverter->convert(
                $validated['amount'],
                $validated['from'],
                $validated['to']
            );

            return response()->json([
                'success' => true,
                'converted_amount' => $convertedAmount,
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'success' => false,
                'error' => $e->getMessage(),
            ], 400);
        }
    }
}
```

## Шаг 4: Настройка маршрута

Добавьте маршрут для метода `convert` контроллера.

### Код: `routes/web.php`

```php
use App\Http\Controllers\CurrencyController;

Route::post('/convert', [CurrencyController::class, 'convert']);
```