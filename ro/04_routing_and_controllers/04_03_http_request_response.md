# Cererea HTTP și răspunsul HTTP în Laravel

## Cererea HTTP [^1]

În capitolul anterior, am discutat pe scurt despre obiectul `Request`, care reprezintă o cerere HTTP. În acest capitol, vom aprofunda modul de lucru cu cererile și răspunsurile HTTP în Laravel.

> [!NOTE]  
> Acest obiect va fi detaliat în lucrul cu formularele.

Reamintim că obiectul `Request` reprezintă o cerere HTTP și conține toate informațiile despre cererea trimisă către server. În Laravel, acest obiect este accesibil în controlere și alte părți ale aplicației unde este utilizat containerul de dependențe.

Pe scurt, obiectul `Request` oferă o interfață orientată pe obiecte pentru a lucra cu datele cererii. Să vedem un exemplu de utilizare:

```php
use Illuminate\Http\Request;

// Returnăm calea cererii
// De exemplu, dacă accesezi /request, metoda va returna șirul "/request"
Route::get('/request', function (Request $request) {
    return $request->path();
});
```

## Metodele cererii HTTP

Obiectul `Request` oferă o gamă largă de metode pentru a lucra cu datele cererii, care pot fi folosite în diverse părți ale aplicației. Să vedem una dintre aceste metode:

### `path()`

Metoda `path()` returnează calea cererii curente, fără informații despre gazdă sau parametrii cererii. **De exemplu**, la accesarea URL-ului `/request`, această metodă va returna șirul `/request`.

### `is()`

Metoda `is()` verifică dacă calea curentă a cererii corespunde unui anumit șablon. **De exemplu**, dacă calea cererii curente este `/request`, apelul metodei `is('request')` va returna `true`.

```php
use Illuminate\Http\Request;

Route::get('/request', function (Request $request) {
    return $request->is('admin/*'); // Returnează true dacă calea cererii începe cu "admin/"
});
```

### `url()`

Metoda `url()` returnează URL-ul complet al cererii curente. **De exemplu**, la accesarea URL-ului `http://example.com/request`, metoda va returna șirul `http://example.com/request`.

### Obținerea anteturilor cererii

Pentru a obține valoarea anteturilor cererii, poți folosi metoda `header()`. **De exemplu**, pentru a obține antetul `User-Agent`, apelează `$request->header('User-Agent')`.

Pentru a verifica existența unui antet, se folosește metoda `hasHeader()`. **De exemplu**, pentru a verifica dacă antetul `User-Agent` există, folosește `$request->hasHeader('User-Agent')`.

### `ip()`

Metoda `ip()` returnează adresa IP a clientului care a trimis cererea curentă. **De exemplu**, la accesarea URL-ului `http://example.com/request`, această metodă va returna adresa IP a clientului.

### Cookie-uri

Pentru a lucra cu cookie-urile din cerere, poți folosi metodele `cookie()` și `hasCookie()`. **De exemplu**, pentru a obține valoarea cookie-ului `name`, apelează `$request->cookie('name')`.

### Obținerea datelor din formulare

Să analizăm următorul formular HTML:

```html
<form method="POST" action="/form">
    <input type="text" name="name" />
    <input type="number" name="age" />
    <select name="country">
        <option value="md">Moldova</option>
        <option value="us">USA</option>
    </select>
    <input type="submit" value="Trimite">
</form>
```

#### Obținerea tuturor datelor din formular

Pentru a obține toate datele trimise prin formular, poți folosi metoda `all()`. **De exemplu**, apelul `$request->all()` va returna un tablou cu datele formularului:

```php
[
    'name' => 'John Doe',
    'age' => 30,
    'country' => 'md',
]
```

#### Obținerea datelor unui câmp specific

Pentru a obține valoarea unui câmp specific din formular, se folosește metoda `input()`. **De exemplu**, pentru a obține valoarea câmpului `name`, trebuie să apelezi `$request->input('name')`.

```php
$name = $request->input('name'); // Returnează 'John Doe' dacă câmpul name are această valoare
```

Dacă câmpul nu există, metoda va returna o valoare implicită, pe care o poți specifica ca al doilea argument:

```php
$name = $request->input('name', 'Guest'); // Returnează 'Guest' dacă câmpul nu este completat
```

> [!NOTE]  
> Vom discuta mai detaliat despre metodele obiectului `Request` în capitolul dedicat formularelor.

## Răspunsul HTTP [^2]

După ce cererea a fost procesată, este necesar să trimitem un răspuns utilizatorului, altfel el nu va vedea rezultatele aplicației 😔.

În Laravel, pentru acest lucru se folosește obiectul `Illuminate\Http\Response`. Acest obiect reprezintă un răspuns HTTP și oferă o gamă largă de metode pentru a gestiona conținutul și parametrii răspunsului.

Pentru a trimite un răspuns, poți folosi acest obiect.

```php
use Illuminate\Http\Response;

Route::get('/response', function () {
    return new Response('Salut, Lume!', 200);
});
```

Va fi trimis următorul răspuns HTTP:

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8

Salut, Lume!
```

În acest exemplu, se creează un obiect `Response` cu textul "Salut, Lume!" și statusul `200`. Acest obiect este returnat din rută și trimis clientului. Rezultatul va fi afișarea textului "Salut, Lume!" pe pagină.

Cu toate acestea, nu este obligatoriu să folosești obiectul `Response`. Poți returna o simplă valoare sau un tablou, iar Laravel va converti automat aceste date într-un obiect `Response`.

```php
// Deși obiectul Response nu este utilizat direct,
// Laravel va încadra automat șirul într-un răspuns Response,
// iar pe pagină va apărea textul "Salut, Lume!"
Route::get('/', function () {
    return 'Salut, Lume!';
});

// Pe pagină va fi afișat tabloul JSON [1, 2, 3]
Route::get('/', function () {
    return [1, 2, 3];
});
```

Astfel, Laravel gestionează flexibil datele, convertindu-le automat în răspunsuri HTTP corecte, astfel încât utilizatorul să primească întotdeauna rezultatul așteptat.

## Funcția auxiliară `response()`

Înainte de a discuta funcția `response()`, să vedem ce sunt funcțiile auxiliare (helpers).

**Funcțiile auxiliare** sunt funcții globale speciale în Laravel care fac codul mai simplu și mai convenabil. Ele permit realizarea rapidă a anumitor sarcini fără a fi nevoie să creezi manual obiecte sau să folosești clase direct (sunt învelișuri în jurul obiectelor). Ne vom întâlni frecvent cu ele pe parcursul învățării Laravel.

Funcția auxiliară `response()` simplifică crearea unui obiect `Response`. Este mai convenabil decât să scrii tot codul manual.

**Exemplu**:

```php
use Illuminate\Http\Response;

Route::get('/response', function () {
    return response('Salut, Lume!', 200);
});

// Cod echivalent fără funcția auxiliară
Route::get('/response', function () {
    return new Response('Salut, Lume!', 200);
});
```

Ambele exemple funcționează identic, dar funcția `response()` face codul mai scurt și mai ușor de citit.

### Setarea anteturilor răspunsului

Pentru a seta anteturi în răspuns, se folosește metoda `header()`. **De exemplu**, dacă vrei să setezi antetul `Content-Type` ca `application/json`, poți apela `$response->header('Content-Type', 'application/json')`.

Exemplu:

```php
use Illuminate\Http\Response;

Route::get('/response', function () {
    return response('Salut, Lume!', 200)
        ->header('Content-Type', 'text/plain');
});

// sau
Route::get('/response', function () {
    return response('Salut, Lume!', 200)
        ->withHeaders([
            'Content-Type' => 'text/plain',
            'X-Custom-Header' => 'Custom Value',
        ]);
});
```



### Redirecționarea

Un alt tip de răspuns în Laravel este redirecționarea. Pentru aceasta, se folosește metoda `redirect()`. **De exemplu**, pentru a trimite utilizatorul către pagina `/home`, este suficient să apelezi `redirect('/home')`.

Exemplu:

```php
use Illuminate\Http\Response;

Route::get('/redirect', function () {
    return redirect('/home');
});
```

Dacă se folosește numele unei rute (`route name`), acesta este considerat un mod preferat:

```php
use Illuminate\Http\Response;

// Definim o rută cu numele "home"
Route::get('/home', function () {
    return 'Pagina de acasă';
})->name('home');

// Redirecționăm utilizatorul către ruta "home"
Route::get('/', function () {
    return redirect()->route('home');
});
```

```php
// Definim o rută dinamică cu parametrul "id"
Route::get('/post/{id}', function ($id) {
    // ...
})->name('post');

// Redirecționăm utilizatorul către ruta "post" cu parametrul "id" setat la 4
Route::get('/', function () {
    // return redirect('/post/4');
    // sau
    return redirect()->route('post', ['id' => 4]);
});
```

Uneori, este necesar să redirecționezi utilizatorul înapoi la **pagina anterioară**. Pentru aceasta, se folosește metoda `back()`.

Exemplu:

```php
use Illuminate\Http\Response;

Route::get('/', function () {
    return back();
});
```

Această metodă este utilă atunci când vrei să returnezi utilizatorul la pagina de unde a venit, de exemplu, după trimiterea unui formular sau după o autentificare reușită.

# Concluzie

În acest capitol, am explorat bazele lucrului cu cererile și răspunsurile HTTP în Laravel. Am învățat cum să obținem date din cerere, să trimitem un răspuns și să efectuăm redirecționări.

Pe parcursul utilizării Laravel, vei putea experimenta aceste funcționalități în practică.

[^1]: HTTP Requests, laravel.com [online]. URL: https://laravel.com/docs/10.x/requests  
[^2]: HTTP Responses, laravel.com [online]. URL: https://laravel.com/docs/10.x/responses