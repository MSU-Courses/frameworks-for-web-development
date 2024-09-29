## Rutarea în Laravel

În general, rutarea în Laravel funcționează similar cu alte framework-uri: URL-ul și metoda HTTP a unei cereri sunt asociate cu o anumită funcție sau metodă în aplicație.

## Directorul de rute `routes`

Rutele sunt definite în directorul `routes`. În acest director se găsesc mai multe fișiere, fiecare destinat unui anumit tip de rute:

- `web.php` — pentru rutele utilizate în aplicațiile web standard.
- `api.php` — pentru rutele destinate API-urilor.
- `console.php` — pentru rutele legate de comenzile din consolă.

În acest moment, ne vom concentra doar pe fișierul `web.php`, deoarece acesta este folosit pentru aplicațiile web obișnuite.

## Bazele rutării în Laravel [^1]

O rută simplă în Laravel primește un **URI** și o **funcție**, care se execută atunci când ruta este accesată. **De exemplu**:

```php
Route::get('/hello', function () {
    return 'Hello, World';
});
```

Când utilizatorul trimite o cerere de tip `GET` către URL-ul `/hello`, va primi ca răspuns textul `Hello, World`.

> [!NOTE]  
> Rutele sunt încărcate automat în timpul inițializării aplicației, deci nu trebuie să le încarci explicit.

### Metode de rutare disponibile

Routerul Laravel oferă mai multe metode care corespund fiecărei metode HTTP:

```php
Route::get($uri, $callback); // Rută pentru cererile GET
Route::post($uri, $callback); // Rută pentru cererile POST
Route::put($uri, $callback); // Rută pentru cererile PUT
Route::patch($uri, $callback); // Rută pentru cererile PATCH
Route::delete($uri, $callback); // Rută pentru cererile DELETE
Route::options($uri, $callback); // Rută pentru cererile OPTIONS
```

De exemplu, pentru a crea o rută care să gestioneze o cerere de tip `POST`, se poate folosi metoda `post`:

```php
Route::post('/hello', function () {
    return 'Hello, World was created ;)';
});
```

Dacă este necesar să înregistrezi o rută care să gestioneze mai multe metode HTTP, poți folosi metoda `match`:

```php
// Această rută va gestiona cererile GET și POST către URL-ul /hello
Route::match(['get', 'post'], '/hello', function () {
    return 'Hello, World';
});
```

De asemenea, poți utiliza metoda `any` pentru a înregistra o rută care răspunde la orice metodă HTTP:

```php
// Această rută va gestiona orice metodă HTTP către URL-ul /hello
Route::any('/hello', function () {
    return 'Hello, World';
});
```

> [!IMPORTANT]  
> Deși această flexibilitate este utilă, se recomandă specificarea explicită a metodei HTTP utilizând metodele `get`, `post`, `put`, `patch`, `delete` și `options` pentru un comportament mai previzibil al rutelor.

### Rutele pentru redirecționări

Pentru a redirecționa cererile de la un URL la altul, poți folosi următoarele metode:

- `redirect` — pentru redirecționări **temporare** (*cod HTTP 302*)
- `permanentRedirect` — pentru redirecționări **permanente** (*cod HTTP 301*)

```php
// Dacă utilizatorul trimite o cerere către URL-ul /user, va fi redirecționat la /profile
Route::redirect('/user', '/profile');
// sau
Route::permanentRedirect('/user', '/profile');
```

### Obținerea cererii

De multe ori, este necesar să accesezi obiectul cererii în handlerul rutei.

> [!NOTE]  
> **Obiectul cererii** este o instanță a clasei `Illuminate\Http\Request`, care conține toate informațiile despre cererea HTTP curentă, inclusiv datele formularului, anteturile, parametrii cererii etc.

Pentru a folosi obiectul cererii în handlerul rutei, trebuie să-l transmiți ca argument:

```php
use Illuminate\Http\Request;

// ...

// Obținerea obiectului cererii și afișarea antetului User-Agent
Route::get('/hello', function (Request $request) {
    return $request->header('User-Agent'); // Obține antetul User-Agent
});
```

> [!NOTE]  
> Vom discuta mai în detaliu lucrul cu obiectul cererii în secțiunile următoare.

### Rute dinamice

#### Parametri obligatorii

Crezi că rutele `/post/1` și `/post/2` sunt rute diferite sau aceeași rută?

Răspunsul simplu este că este aceeași rută, dar cu parametri diferiți. În Laravel, aceste rute sunt numite **rute dinamice**.

**Rutele dinamice** sunt rute care conțin parametri transmiși prin URL. Pentru a le crea, se folosesc acolade `{}`, care marchează un parametru dinamic.

Acest parametru este numit **parametru de rută** și este transmis în handlerul rutei ca argument.

**Exemplu**:

```php
Route::get('/post/{id}', function (int $id) {
    return 'Post ID: ' . $id;
});
```

Dacă utilizatorul trimite o cerere către `/post/1`, va primi ca răspuns textul `Post ID: 1`.

După ce ai primit parametrul în handlerul rutei, poți efectua diverse acțiuni, cum ar fi obținerea datelor din baza de date folosind `ID`-ul transmis.

De asemenea, poți utiliza mai mulți parametri într-o singură rută:

```php
Route::get('/post/{id}/{slug}', function (int $id, string $slug) {
    return 'Post ID: ' . $id . ', Slug: ' . $slug;
});
```

Dacă cererea va fi către `/post/1/hello-world`, răspunsul va fi `Post ID: 1, Slug: hello-world`.

> [!NOTE]  
> După cum ai observat, parametrii din funcție sunt **tipizați** (`int $id`). Acest lucru permite Laravel să convertească automat valorile transmise în tipul specificat și să prevină eventualele erori.

#### Folosirea `Request`

Pentru a obține **obiectul cererii** în handlerul rutei, trebuie să-l transmiți ca argument **înaintea parametrilor rutei**:

```php
Route::get('/post/{id}', function (Request $request, int $id) {
    return 'User-Agent: ' . $request->header('User-Agent') . ', Post ID: ' . $id;
});
```

#### Parametri opționali

Uneori, un parametru de rută **poate fi opțional**. În astfel de cazuri, poți specifica o valoare implicită pentru parametru și un semn `?` după parametru.

```php
Route::get('/post/{id?}', function (int $id = 1) {
    return 'Post ID: ' . $id;
});
```

#### Restricționarea parametrilor

Să luăm următoarea rută:

```php
Route::get('/post/{id}', function (int $id) {
    return 'Post ID: ' . $id;
});
```

Dacă utilizatorul trimite o cerere către `/post/abc`, va primi o eroare, deoarece parametrul `id` așteaptă o valoare numerică (`int`).

Laravel oferă un mecanism de **restricționare a parametrilor**, care permite specificarea unei expresii regulate pentru parametru, limitând valorile acceptabile.

Restricția unui parametru de rută se face prin metoda `where`:

`where($name, $expression)`, unde:
- `$name` — numele parametrului de rută
- `$expression` — expresia regulată care definește valorile acceptabile pentru parametru

**Exemplu**:

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

Acum, dacă utilizatorul trimite o cerere către `/post/abc`, va primi o eroare 404, deoarece parametrul `id` trebuie să conțină doar valori numerice.

> [!NOTE]  
> Dacă un parametru nu respectă restricția, Laravel va return

a automat o **eroare 404**.

Pentru restricții frecvent utilizate, Laravel oferă metode predefinite:

- `whereNumber($name)` — limitează parametrul la valori numerice
- `whereAlpha($name)` — limitează parametrul la valori alfabetice
- `whereAlphaNumeric($name)` — limitează parametrul la valori alfanumerice
- `whereUuid($name)` — limitează parametrul la valori de tip UUID
- și altele

**Exemplu**:

```php
Route::get('/post/{id}', function (int $id) {
    return 'Post ID: ' . $id;
})->whereNumber('id');

Route::get('/user/{id}/{name}', function (int $id, string $name) {
    return 'User ID: ' . $id . ', Name: ' . $name;
})->whereAlpha('name');
```

#### Restricții globale

Pentru a seta restricții globale pentru toți parametrii de rută, poți folosi metoda `pattern` în fișierul `RouteServiceProvider`, în metoda `boot`:

```php
public function boot(): void
{
    Route::pattern('id', '[0-9]+');
}
```

### Rute denumite

Imaginează-ți următoarea situație: ai creat o rută pentru pagina de profil a utilizatorului:

```php
Route::get('/profile/{id}', function (int $id) {
    return 'User ID: ' . $id;
});
```

Ai început să folosești această rută în diferite părți ale aplicației. Cu toate acestea, în timp, ai decis să schimbi URL-ul în `/user/{id}`.

Acum trebuie să actualizezi URL-ul în toate locurile în care a fost folosită această rută, lucru care este incomod și ineficient. Pentru a evita o astfel de situație, Laravel oferă **rute denumite**.

Fiecare rută poate primi un nume unic utilizând metoda `name`:

```php
Route::get('/profile/{id}', function (int $id) {
    return 'User ID: ' . $id;
})->name('profile');
```

> [!NOTE]  
> Numele rutei poate conține litere, cifre, precum și simbolurile `-`, `_` și `.`.

#### Utilizarea rutelor denumite

Pentru a genera un URL pe baza numelui rutei, se folosește funcția `route`:

```php
// Generarea URL-ului pe baza numelui rutei
$url = route('about');

// Generarea URL-ului pe baza numelui rutei cu parametri
// În acest caz, parametrul {id} va fi înlocuit cu valoarea 1
$url = route('profile', ['id' => 1]);
```

În general, utilizarea rutelor denumite simplifică considerabil gestionarea rutelor și previne erorile atunci când se modifică URL-urile.

#### Bune practici pentru denumirea rutelor

Nu există un standard fix pentru denumirea rutelor, dar există câteva recomandări:

1. **Folosește o structură ierarhică**.
   - Folosește simbolul `.` pentru a crea o structură ierarhică în denumirea rutelor. De exemplu, pentru `/post/{id}` poți folosi `post.show`, `post.edit`, `post.delete`.
   - [✅] `post.show`, `post.edit`, `post.delete`, `admin.post.show`, `admin.user.create`
   - [❌] `showPost`, `editPost`, `deletePost`
2. **Folosește nume descriptive**.
   - Numele rutelor ar trebui să fie descriptive și ușor de înțeles, pentru a ști clar unde duce o rută.
   - [✅] `post.show`, `post.edit`, `post.delete`, `post.create`
   - [❌] `post.sfd`, `post.see`, `post.del`, `post.cr`
3. **Fii consecvent în stilul de denumire**.
   - Alege un singur stil de denumire și respectă-l pe tot parcursul proiectului.
   - [✅] `post.show`, `post.edit`, `post.delete`, `post.create`
   - [❌] `postShow`, `post.edit`, `delete_post`, `postCreate`
4. **Folosește nume scurte**.
   - Folosește nume scurte pentru a reduce șansele de eroare la scriere.
   - [✅] `post.show`, `post.edit`, `post.delete`, `post.create`
   - [❌] `post.show.page`, `post.edit.page`, `post.delete.page`, `post.create.page`

### Gruparea rutelor

În Laravel, poți grupa rutele pentru a aplica setări comune, cum ar fi middleware-uri, prefixuri, spații de nume și alți parametri.

> [!NOTE]  
> Vom discuta despre middleware-uri și controllere în secțiunile următoare, dar pentru moment să vedem un exemplu de grupare a rutelor.

#### Gruparea prin prefix:

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

În acest exemplu, toate rutele din cadrul grupului vor avea prefixul `/admin`: `/admin/dashboard`, `/admin/users`.

#### Gruparea prin spațiu de nume:

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

În acest caz, toate rutele din cadrul grupului vor avea spațiul de nume `post`: `post.show`, `post.edit`.

### Lista rutelor

Pentru a vizualiza toate rutele înregistrate în aplicație, poți folosi comanda `route:list`:

```bash
php artisan route:list
```

De multe ori, în listă vor apărea nu doar rutele tale, ci și rutele definite de framework sau pachete externe. Pentru a afișa doar rutele proprii, poți folosi opțiunea `--except-vendor`:

```bash
php artisan route:list --except-vendor
```

[^1]: Laravel Routing, laravel.com [online]. URL: https://laravel.com/docs/10.x/routing