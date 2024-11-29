## Utilizarea serviciilor ca helper, fațadă și prin Dependency Injection

Laravel oferă mai multe modalități flexibile de a lucra cu servicii:  
- utilizând **helperi**,  
- prin **fațade**,  
- sau prin **Dependency Injection (DI)**.  

Fiecare abordare are propriile caracteristici și este folosită în funcție de context. Înțelegerea acestor metode îți permite să scrii cod mai flexibil și ușor de întreținut.

De exemplu, serviciul integrat de autentificare din Laravel (`Auth`) poate fi accesat prin helperul `auth()`, fațada `Auth` sau injectat ca dependență. Aceste metode pot fi aplicate și propriilor servicii.

---

### Serviciul ca helper

**Helperii** sunt funcții globale care oferă acces rapid la diverse funcționalități. Laravel are mai mulți helperi încorporați, cum ar fi `auth()`, `route()`, `config()` și alții. Helperii sunt utili pentru apeluri rapide și concise.

#### Exemplu: `auth()` ca helper

Helperul `auth()` permite obținerea utilizatorului curent:

```php
$user = auth()->user();
```

Acest helper apelează intern serviciul de autentificare `AuthManager`, care este înregistrat în containerul de servicii.

#### Cum să creezi un helper personalizat?

Poți crea un helper pentru a apela metodele propriului serviciu. De exemplu, pentru `CurrencyConverter`:

1. Creează un fișier `app/Helpers/custom_helpers.php` (dacă nu există deja).

2. Adaugă următorul cod:

```php
use App\Services\CurrencyConverter;

if (!function_exists('convert_currency')) {
    function convert_currency($amount, $from, $to)
    {
        return app(CurrencyConverter::class)->convert($amount, $from, $to);
    }
}
```

3. Adaugă fișierul în `composer.json`:

```json
"autoload": {
    "files": [
        "app/Helpers/custom_helpers.php"
    ]
}
```

4. Rulează comanda:

```bash
composer dump-autoload
```

Acum poți folosi helperul `convert_currency()`:

```php
$converted = convert_currency(100, 'USD', 'EUR');
```

---

### Serviciul ca fațadă

**Fațadele** oferă o interfață statică pentru accesarea serviciilor prin containerul de servicii. Ele simplifică apelarea metodelor, păstrând codul curat și compact. Laravel folosește fațade pentru componente precum `Auth`, `Cache`, `DB` și altele.

#### Exemplu: `Auth` ca fațadă

Fațada `Auth` permite gestionarea autentificării:

```php
$user = Auth::user();
```

Intern, fațada apelează același serviciu `AuthManager` pe care îl folosește și helperul `auth()`.

#### Cum să creezi o fațadă pentru un serviciu personalizat?

1. Creează fișierul pentru fațadă:

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

2. Înregistrează serviciul în container (dacă nu este deja înregistrat):

```php
$this->app->singleton(\App\Services\CurrencyConverter::class, function ($app) {
    return new \App\Services\CurrencyConverter();
});
```

3. Folosește fațada:

```php
use App\Facades\Currency;

$converted = Currency::convert(100, 'USD', 'EUR');
```

Fațadele sunt utile pentru servicii utilizate frecvent, dar pot complica testarea, deoarece nu permit ușor înlocuirea dependențelor cu mock-uri.

---

### Serviciul prin Dependency Injection

**Dependency Injection (DI)** este metoda principală de a lucra cu servicii în Laravel, mai ales cu cele proprii. Injectarea dependențelor face codul mai ușor de testat și modular. DI permite specificarea explicită a dependențelor clasei prin constructor sau metode.

#### Exemplu: Utilizarea `CurrencyConverter` prin DI

1. Injectează serviciul în controller prin constructor:

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

2. Serviciul este acum gestionat de container, ceea ce simplifică testarea și întreținerea.

---

### Concluzie

Alegerea între helper, fațadă și Dependency Injection depinde de context:

- Folosește **DI** pentru serviciile tale, deoarece face codul mai modular și mai ușor de testat.
- Utilizează **helperii** pentru acces rapid la funcționalități simple.
- Folosește **fațadele** pentru acces convenabil la componentele Laravel predefinite.

**Dependency Injection** este cea mai bună alegere pentru serviciile personalizate, deoarece oferă control, testabilitate și flexibilitate. Helperii și fațadele sunt ideale pentru creșterea ușurinței de utilizare a serviciilor utilitare sau integrate.