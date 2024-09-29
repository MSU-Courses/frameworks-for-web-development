# Controlerele în Laravel

Să discutăm o problemă care poate apărea atunci când definim toate rutele și logica de manipulare a cererilor direct în fișierul `routes/web.php`.

Imaginați-vă că aveți mai multe rute, iar toată logica lor este în același fișier.

**Exemplu**:

```php
// Lista postărilor
Route::get('/post', function () {
    // ...
});

// Detalii despre o postare
Route::get('/post/{id}', function ($id) {
    // ...
});

// Crearea unei postări
Route::post('/post', function () {
    // ...
});

// Actualizarea unei postări
Route::put('/post/{id}', function ($id) {
    // ...
});
```

După cum se poate observa, fișierul `routes/web.php` poate deveni rapid voluminos și greu de gestionat, mai ales când avem multe rute și logica devine complexă.

Pentru a rezolva această problemă, putem muta logica de manipulare a cererilor în fișiere separate. În Laravel, aceste fișiere se numesc **controlere**, care ajută la organizarea codului și la crearea unei structuri mai clare.

## Ce sunt controlerele în Laravel [^1]

> [!TIP]  
> Un controller în arhitectura MVC este acea parte a aplicației care gestionează cererile și interacționează cu modelul și vederea.  
> Controllerul primește o cerere, o procesează și returnează un răspuns.

Un **controller** în Laravel este o **clasă** care conține metode pentru manipularea cererilor.

Fiecare metodă din controller efectuează o acțiune specifică atunci când este apelată o anumită rută. În loc să scriem logica de manipulare a cererilor direct în rute, o putem muta în metodele controllerului. Aceasta ajută la simplificarea structurii codului.

Controlerele permit gruparea logicii conexe într-o singură clasă, făcând codul mai ușor de înțeles și întreținut. De exemplu, un controller `PostController` va gestiona postările, iar `UserController` va gestiona utilizatorii.

> [!NOTE]  
> În mod implicit, controlerele în Laravel se află în directorul `app/Http/Controllers`.

## Crearea unui controller

Există două moduri de a crea un controller în Laravel:

1. **Creare manuală a fișierului**:
   - Creați un fișier controller în folderul `app/Http/Controllers`.
   - Definiți o clasă care extinde clasa `Controller`.
   - Definiți metodele pentru manipularea cererilor.

2. **Utilizarea Artisan**:
   - Rulați comanda `php artisan make:controller <NumeController>` pentru a crea automat controllerul.

## Denumirea clasei controller

Clasa controller ar trebui să aibă **sufixul** `Controller`. **De exemplu**, dacă controllerul gestionează postările, numele său ar trebui să fie `PostController`. Aceasta ajută la menținerea unui stil de denumire consistent și la îmbunătățirea lizibilității codului.

### Gruparea rutelor după controlere

Un aspect important al dezvoltării este gruparea corectă a rutelor pe controlere. **De exemplu**, dacă aveți rute pentru gestionarea postărilor și categoriilor de postări, apare întrebarea: să le plasăm în două controlere separate — `PostController` și `CategoryController`, sau să le combinăm într-un singur `PostController`, deoarece categoriile sunt legate de postări?

O bună practică este să separați logica în **controlere diferite**, cum ar fi `PostController` pentru postări și `CategoryController` pentru categorii. Aceasta îmbunătățește modularitatea și face codul mai ușor de gestionat și întreținut. Fiecare controller ar trebui să aibă o singură responsabilitate (SRP — Single Responsibility Principle)[^2], ceea ce este esențial în proiectele mari.

## Exemplu de controller

Să presupunem că avem definite următoarele rute în fișierul `routes/web.php`:

```php
Route::get('/post', function () {
    return 'Lista postărilor';
});

Route::get('/post/{id}', function ($id) {
    return 'Detalii postare: ' . $id;
});

Route::post('/post', function () {
    return 'Creează postare';
});

Route::put('/post/{id}', function ($id) {
    return 'Actualizează postarea: ' . $id;
});
```

Putem muta această logică într-un controller `PostController` pentru a face codul mai organizat.

Creăm controllerul `PostController` folosind Artisan:

```bash
php artisan make:controller PostController
```

În fișierul `PostController.php`, vom defini metodele care vor manipula cererile:

```php
<?php

namespace App\Http\Controllers;

class PostController extends Controller {
    public function index() {
        return 'Lista postărilor';
    }
    
    public function show($id) {
        return 'Detalii postare: ' . $id;
    }
    
    public function store() {
        return 'Creează postare';
    }
    
    public function update($id) {
        return 'Actualizează postarea: ' . $id;
    }
    
    // ...
}
```

Acum putem utiliza acest controller în rute. Sintaxa generală pentru definirea rutelor folosind un controller este următoarea:  
`Route::method('/url', [Controller::class, 'controllerMethod']);`, unde:

- `controllerMethod` — este metoda controllerului care gestionează cererea.
- `Controller::class` — este numele clasei controllerului.
- `/url` — este adresa URL a rutei.

Exemplu de rute cu controller:

```php
use App\Http\Controllers\PostController;

Route::get('/post', [PostController::class, 'index']);
Route::get('/post/{id}', [PostController::class, 'show']);
Route::post('/post', [PostController::class, 'store']);
Route::put('/post/{id}', [PostController::class, 'update']);
```

Acum, atunci când se accesează rutele `/post`, `/post/{id}`, `/post` (cerere POST) și `/post/{id}` (cerere PUT), se vor apela metodele corespunzătoare din controllerul `PostController`: `index`, `show`, `store` și `update`. Aceasta simplifică gestionarea logicii de manipulare a cererilor și face codul mai ușor de întreținut.

## Gruparea rutelor după controlere

Pentru a evita repetarea codului, ca în exemplul anterior, unde în fiecare rută trebuie să specificăm controllerul și metoda acestuia, Laravel oferă metoda `Route::controller()`. Aceasta permite gruparea rutelor care aparțin aceluiași controller.

### Exemplu de utilizare a `Route::controller()`:

```php
use App\Http\Controllers\PostController;

Route::controller(PostController::class)->group(function () {
    Route::get('/post', 'index');
    Route::get('/post/{id}', 'show');
    Route::post('/post', 'store');
    Route::put('/post/{id}', 'update');
});
```

După cum se poate observa, codul este simplificat, însă prefixul `/post` încă se repetă la fiecare rută. Pentru a rezolva această problemă, Laravel oferă metoda `Route::prefix()`, care permite adăugarea unui prefix la toate rutele din grup.

### Exemplu de utilizare a `Route::prefix()`:

```php
use App\Http\Controllers\PostController;

Route::controller(PostController::class)->prefix('post')->group(function () {
    Route::get('/', 'index');
    Route::get('/{id}', 'show');
    Route::post('/', 'store');
    Route::put('/{id}', 'update');
});
```

Acum, toate rutele cu prefixul comun `/post` sunt grupate, iar codul este mai curat. Urmând bunele practici, putem folosi și **rute denumite** pentru a face codul și mai lizibil.

### Exemplu de utilizare a rutelor denumite:

```php
use App\Http\Controllers\PostController;

Route::controller(PostController::class)->prefix('post')->group(function () {
    Route::get('/', 'index')->name('post.index');
    Route::get('/{id}', 'show')->name('post.show');
    Route::post('/', 'store')->name('post.store');
    Route::put('/{id}', 'update')->name('post.update');
});
```

Totuși, repetăm o parte din numele rutei (`post.`) în fiecare metodă `name()`. Pentru a evita acest lucru, putem folosi metoda `name()` aplicată întregului grup de rute, care va adăuga automat un prefix la numele rutelor.

### Exemplu de utilizare a metodei `name()` pentru

un grup:

```php
use App\Http\Controllers\PostController;

Route::controller(PostController::class)->prefix('post')->name('post.')->group(function () {
    Route::get('/', 'index')->name('index');
    Route::get('/{id}', 'show')->name('show');
    Route::post('/', 'store')->name('store');
    Route::put('/{id}', 'update')->name('update');
});
```

Acum fiecare rută primește automat prefixul `post.` în numele său, iar codul devine mai optimizat.

### Exemplu de generare a URL-urilor folosind rute denumite:

```php
route('post.index'); // GET /post
route('post.show', ['id' => 1]); // GET /post/1
route('post.store'); // POST /post
```

Folosind gruparea rutelor după controlere și prefixuri, precum și rute denumite, putem simplifica și structura codul, făcându-l mai lizibil și ușor de întreținut.

> [!TIP]  
> Ordinea de utilizare a metodelor `Route::controller()`, `Route::prefix()`, și `Route::name()` nu contează. De exemplu, combinațiile `Route::controller()->prefix()->name()`, `Route::name()->prefix()->controller()`, sau `Route::prefix()->name()->controller()` vor funcționa la fel. Cu toate acestea, pentru o lizibilitate mai bună, se recomandă utilizarea unei ordini logice și intuitive.

## Controlerele de tip resource

Atunci când lucrăm cu resurse precum postări, categorii sau utilizatori, este frecvent necesar să realizăm operații standard: creare (**Create**), citire (**Read**), actualizare (**Update**) și ștergere (**Delete**), cunoscute sub numele de operații CRUD.

Dacă nu folosim controlere de tip resource, am putea crea un controller `PostController` și altele pentru fiecare resursă și să definim metodele `index`, `show`, `store`, `update` și `destroy`:

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
```

După cum se poate observa, repetăm aceleași metode pentru fiecare controller, ceea ce face codul voluminos și dificil de întreținut. Pentru a simplifica acest proces, Laravel oferă **controlere de tip resource**.

Un **controller de tip resource** conține un set standard de metode pentru gestionarea unei resurse. Laravel permite definirea tuturor acestor metode într-un singur loc, iar rutele corespunzătoare pot fi generate automat.

### Crearea unui controller de tip resource

Pentru a crea un controller de tip resource, utilizați comanda Artisan cu opțiunea `--resource`:

```bash
php artisan make:controller PostController --resource
```

După rularea comenzii, în directorul `app/Http/Controllers` va fi creat fișierul `PostController.php` cu metodele predefinite pentru gestionarea resursei.

```php
class PostController extends Controller {
    public function index() {
        // Afișarea listei de postări
    }

    public function create() {
        // Formular pentru crearea unei noi postări
    }

    public function store(Request $request) {
        // Salvarea unei noi postări
    }

    public function show($id) {
        // Afișarea unei postări
    }

    public function edit($id) {
        // Formular pentru editarea unei postări
    }

    public function update(Request $request, $id) {
        // Actualizarea unei postări existente
    }

    public function destroy($id) {
        // Ștergerea unei postări
    }
}
```

### Definirea rutelor pentru un controller de tip resource

Pentru a defini automat rutele pentru aceste metode, folosește metoda `Route::resource()`:

```php
use App\Http\Controllers\PostController;
use App\Http\Controllers\CategoryController;

Route::resource('post', PostController::class);
Route::resource('category', CategoryController::class);
```

### Acțiunile standard ale unui controller de tip resource

Metoda `Route::resource()` creează un set de rute standard pentru manipularea unei resurse. În Laravel sunt definite 7 acțiuni standard:

| Metodă HTTP | URI             | Metoda controllerului | Numele rutei  | Descriere                           |
|-------------|-----------------|-----------------------|---------------|-------------------------------------|
| GET         | /post           | index                 | post.index    | Afișarea listei de postări          |
| GET         | /post/create    | create                | post.create   | Formular pentru crearea unei postări|
| POST        | /post           | store                 | post.store    | Salvarea unei noi postări           |
| GET         | /post/{id}      | show                  | post.show     | Afișarea unei postări specifice     |
| GET         | /post/{id}/edit | edit                  | post.edit     | Formular pentru editarea unei postări|
| PUT/PATCH   | /post/{id}      | update                | post.update   | Actualizarea unei postări existente |
| DELETE      | /post/{id}      | destroy               | post.destroy  | Ștergerea unei postări              |

Folosind controlere de tip resource, simplifici definirea rutelor și organizarea codului, ceea ce face aplicația mai ușor de înțeles și întreținut.

### Redenumirea rutelor unui controller de tip resource

Deși nu este o practică recomandată, poți redenumi rutele generate automat cu ajutorul metodei `names()`.

```php
Route::resource('post', PostController::class)->names([
    'create' => 'post.build',  // Noul nume pentru 'post.create'
    'store'  => 'post.save',   // Noul nume pentru 'post.store'
]);
```

## Controlere singleton

În unele cazuri, poate fi necesar să folosești **controlere singleton**, atunci când o resursă poate avea un singur exemplar pentru utilizator sau pentru întregul sistem. Un exemplu ar putea fi **profilul unui utilizator**: deși pot exista multe profiluri, fiecare utilizator are doar unul singur.

Pentru a crea un controller singleton, folosește comanda Artisan `make:controller` cu opțiunea `--singleton`:

```bash
php artisan make:controller ProfileController --singleton
```

Pentru a defini rutele pentru un controller singleton, folosește metoda `Route::singleton()`:

```php
use App\Http\Controllers\ProfileController;

Route::singleton('profile', ProfileController::class);
```

Aceasta va crea următoarele rute:

| Metodă HTTP | URI           | Metoda controllerului | Numele rutei   | Descriere                           |
|-------------|---------------|-----------------------|----------------|-------------------------------------|
| GET         | /profile      | show                  | profile.show   | Afișarea profilului                 |
| GET         | /profile/edit | edit                  | profile.edit   | Formular pentru editarea profilului |
| PUT/PATCH   | /profile      | update                | profile.update | Actualizarea profilului             |

Controlerele singleton simplifică gestionarea resurselor care există într-un singur exemplar, eliminând necesitatea de a transmite identificatorul resursei în rute. Acest lucru face codul mai clar și mai concis.

Desigur, poți defini manual aceste rute:

```php
use App\Http\Controllers\ProfileController;

Route::get('/profile', [ProfileController::class, 'show'])->name('profile.show');
Route::get('/profile/edit', [ProfileController::class, 'edit'])->name('profile.edit');
Route::put('/profile', [ProfileController::class, 'update'])->name('profile.update');
```

Cu toate acestea, utilizarea metodelor precum `Route::resource()` sau `Route::singleton()` simplifică semnificativ codul, mai ales atunci când se lucrează cu resurse care necesită operații standard CRUD. În loc să scrii aceleași rute pentru fiecare resursă, poți genera automat aceste rute, economisind timp și reducând posibilitatea erorilor.

[^1]: Controllers, laravel.com [online]. URL: https://laravel.com/docs/10.x/controllers  
[^2]: The Single Responsibility Principle (SRP) of SOLID, medium.com [online]. URL: https://giovannamoeller.medium.com/the-single-responsibility-principle-srp-of-solid-eb2feed0c64b