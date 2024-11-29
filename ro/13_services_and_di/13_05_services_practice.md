# Partea practică: Serviciu pentru conversia valutelor

În această parte, vom crea un serviciu — **CurrencyConverter**, care va fi utilizat pentru conversia valutelor. 

Controller-ul va avea un rol minim: gestionarea cererilor și delegarea logicii serviciului.

---

## Pasul 1: Crearea serviciului

Creează un director `app/Services` dacă acesta nu există deja. În acest director, creează fișierul `CurrencyConverter.php`.

### Cod: `app/Services/CurrencyConverter.php`

```php
<?php

namespace App\Services;

class CurrencyConverter
{
    protected array $rates;

    public function __construct()
    {
        // Exemple de cursuri valutare fixe
        // Datele pot fi obținute dintr-un API extern sau o bază de date
        $this->rates = [
            'USD' => 1.0,
            'EUR' => 0.85,
            'GBP' => 0.75,
        ];
    }

    /**
     * Convertește o sumă dintr-o valută în alta.
     *
     * @param float $amount Suma de convertit
     * @param string $from Valuta de origine
     * @param string $to Valuta țintă
     * @return float Suma convertită
     * @throws \Exception Dacă valuta nu este suportată
     */
    public function convert(float $amount, string $from, string $to): float
    {
        if (!isset($this->rates[$from]) || !isset($this->rates[$to])) {
            throw new \Exception("Valuta nu este suportată: {$from} sau {$to}");
        }

        $amountInBase = $amount / $this->rates[$from];
        return $amountInBase * $this->rates[$to];
    }
}
```


## Pasul 2: Înregistrarea serviciului în `Service Container`

Înregistrează serviciul în metoda `register` a clasei `AppServiceProvider`.

### Cod: `app/Providers/AppServiceProvider.php`

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Services\CurrencyConverter;

class AppServiceProvider extends ServiceProvider
{
    public function register()
    {
        // Înregistrarea CurrencyConverter ca singleton
        // deoarece nu păstrează stare și nu depinde de alte servicii.
        // Aceasta permite utilizarea unui singur exemplu al serviciului în întreaga aplicație.
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

## Pasul 3: Utilizarea serviciului în controller

Creează un controller care va utiliza `CurrencyConverter`.

### Crearea controller-ului

Creează un controller folosind comanda Artisan:

```bash
php artisan make:controller CurrencyController
```

### Cod: `app/Http/Controllers/CurrencyController.php`

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
     * Gestionează cererea de conversie valutară.
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

## Pasul 4: Configurarea rutei

Adaugă o rută pentru metoda `convert` din controller.

### Cod: `routes/web.php`

```php
use App\Http\Controllers\CurrencyController;

Route::post('/convert', [CurrencyController::class, 'convert']);
```