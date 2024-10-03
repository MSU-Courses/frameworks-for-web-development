# Șablonizatorul în Laravel

În prezent, există numeroase șablonizatoare, iar fiecare framework preferă să utilizeze unul specific. Totuși, poți instala orice șablonizator după preferință.

În Laravel, se folosește șablonizatorul **Blade**, care permite inserarea codului PHP direct în șabloane.

## De ce folosim un șablonizator?

În tema anterioară, am discutat despre rutare și controlere și cum acestea ajută la separarea logicii aplicației de partea de prezentare.

Putem returna HTML direct dintr-un controller:

```php
Route::get('/profile', function () {
    $user = // Obținerea datelor utilizatorului

    return "
        <!DOCTYPE html>
        <html>
        <head>
            <title>Profil utilizator</title>
        </head>
        <body>
            <h1>Profil utilizator</h1>
            <p>Nume: {$user->name}</p>
            <p>Prenume: {$user->surname}</p>
            <p>Vârstă: {$user->age}</p>
        </body>
        </html>
    ";
});
```

Totuși, această abordare încalcă principiul separării logicii de prezentare, făcând codul greu de citit și întreținut.

De aceea, folosirea șablonizatoarelor precum Blade ajută la o separare mai clară între logică și prezentare, structurant codul și făcându-l mai ușor de gestionat.

## Bazele Blade

**Blade** este un șablonizator care permite utilizarea codului PHP direct în șabloane și oferă instrumente eficiente pentru gestionarea datelor.

Toate șabloanele Blade sunt compilate în cod PHP obișnuit și stocate în cache, ceea ce îmbunătățește performanța. Dacă șablonul este modificat, acesta este recompilat automat.

De exemplu, pentru a afișa o variabilă, folosește acolade duble:

```blade
{{ $name }}
```

Codul PHP compilat va arăta astfel:

```php
<?php echo e($name); ?>
```

## Crearea șabloanelor

Șabloanele se creează în directorul `resources/views`. Toate șabloanele trebuie să aibă extensia `.blade.php`.

Exemplu de șablon `profile.blade.php`:

```blade
<!DOCTYPE html>
<html>
<head>
    <title>Profil utilizator</title>
</head>
<body>
    <h1>Profil utilizator</h1>
    <p>Nume: {{ $name }}</p>
    <p>Prenume: {{ $surname }}</p>
    <p>Vârstă: {{ $age }}</p>
</body>
</html>
```

## Randarea șabloanelor

Pentru a randa un șablon în Laravel, se folosește metoda `view()`, care primește ca parametri numele șablonului și un array de date ce vor fi transmise șablonului.

Exemplu de randare pentru șablonul `profile.blade.php`:

```php
Route::get('/profile', function () {
    return view('profile', [
        'name' => 'Ivan',
        'surname' => 'Ivanov',
        'age' => 25
    ]);
});
```

Atunci când este accesat URL-ul `/profile`, șablonul `profile.blade.php` va fi afișat cu datele transmise.

Observi că nu este nevoie să specifici extensia `.blade.php` în metoda `view()`. Laravel o adaugă automat.

### Funcția `compact()`

În loc să transmiți un array de date în metoda `view()`, poți folosi funcția `compact()`, care creează automat un array pe baza variabilelor transmise.

Exemplu de utilizare a funcției `compact()`:

```php
Route::get('/profile', function () {
    $name = 'Ivan';
    $surname = 'Ivanov';
    $age = 25;

    return view('profile', compact('name', 'surname', 'age'));
});
```

Acest cod este echivalent cu următorul:

```php
return view('profile', [
    'name' => $name,
    'surname' => $surname,
    'age' => $age
]);
```

Utilizarea funcției `compact()` ajută la evitarea duplicării numelor variabilelor, făcând astfel codul mai concis și mai ușor de citit și întreținut.

## Denumirea șabloanelor

Șabloanele sunt rulate și încărcate frecvent în Laravel, de aceea este important să le denumești clar pentru a fi ușor de identificat de ceilalți dezvoltatori.

### Recomandări pentru denumirea șabloanelor

În Laravel, este recomandat să urmezi aceste reguli:

- **Reflectarea conținutului:** Numele șablonului ar trebui să descrie clar conținutul său, cum ar fi `profile.blade.php` pentru un șablon de profil utilizator.
- **Folosește cratime:** Pentru separarea cuvintelor, este recomandat să folosești cratime, de exemplu, `user-profile.blade.php`.
- **Organizarea în directoare:** Pentru o structură clară a proiectului, organizează șabloanele în directoare, de exemplu, `users/profile.blade.php` pentru profilul utilizatorului.
- **Nume clare:** Numele șabloanelor trebuie să fie clare și ușor de înțeles pentru toți dezvoltatorii proiectului.

### Exemple de nume bune și rele pentru șabloane

| Nume bune de șabloane      | Nume rele de șabloane    |
| -------------------------- | ------------------------ |
| `profile.blade.php`         | `user.blade.php`         |
| `user-profile.blade.php`    | `userprofile.blade.php`  |
| `users/profile.blade.php`   | `userProfile.blade.php`  |
| `posts/index.blade.php`     | `post.blade.php`         |
| `posts/show.blade.php`      | `POST.blade.php`         |

### Organizarea șabloanelor

Șabloanele Laravel sunt de obicei stocate în directorul `resources/views`. Pentru claritate, este recomandat să grupezi șabloanele în directoare:

- `resources/views/users` — pentru șabloane legate de utilizatori;
- `resources/views/posts` — pentru șabloane legate de postări;
- `resources/views/layouts` — pentru layout-uri;
- și altele.

Aceasta menține o structură clară și facilitează căutarea fișierelor.

### Denumirea șabloanelor la conectare și randare

Atunci când conectezi șabloane în Laravel, folosești calea relativă din directorul `resources/views`. De exemplu, pentru a randa `resources/views/index.blade.php`, folosești `view('index')`.

Dacă șablonul este într-un subdirector, se folosește notația cu puncte pentru a separa directoarele. De exemplu:

- Șablonul `resources/views/users/profile.blade.php` se randează ca `view('users.profile')`.
- Șablonul `resources/views/posts/details/show.blade.php` se randează ca `view('posts.details.show')`.

Acest sistem simplifică gestionarea șabloanelor și structura proiectului.


## Afișarea variabilelor

Pentru a afișa variabile într-un șablon Blade, se folosesc acolade duble:

```blade
Hello, {{ $name }}
```

Reamintim că datele sunt transmise șablonului sub forma unui array:

```php
return view('profile', [
    'name' => 'Ivan',
    'surname' => 'Ivanov',
    'age' => 25
]);
```

> [!NOTE]
> Expresiile de ieșire `{{ }}` din Blade sunt trecute automat prin funcția `htmlspecialchars` din PHP pentru a preveni atacurile XSS.

## Directive Blade

**Directivele Blade** sunt instrucțiuni speciale care permit introducerea de cod PHP în șabloane, făcându-le mai dinamice. Directivele sunt aliasuri simple pentru cod PHP, facilitând utilizarea acestuia în șabloane.

### Directivele `@if`, `@elseif`, `@else` și `@endif`

Directiva `@if` permite rularea condițiilor în șablon.

Sintaxa directivei `@if`:

```blade
@if (conditie)
    // Codul care va fi rulat dacă condiția este adevărată
@else
    // Codul care va fi rulat dacă condiția este falsă
@endif
```

Exemplu de utilizare:

```blade
@if ($name == 'Ivan')
    <p>Bună, Ivan!</p>
@else
    <p>Bună, vizitatorule!</p>
@endif
```

Exemplu cu `@elseif`:

```blade
@if ($fruit == 'apple')
    <p>Aceasta este un măr</p>
@elseif ($fruit == 'banana')
    <p>Aceasta este o banană</p>
@else
    <p>Aceasta nu este nici măr, nici banană</p>
@endif
```

### Directiva `@unless`

Directiva `@unless` rulează codul dacă o condiție este falsă.

Sintaxa directivei `@unless`:

```blade
@unless (conditie)
    // Codul care va fi rulat dacă condiția este falsă
@endunless
```

Exemplu de utilizare pentru verificarea autentificării:

```blade
@unless (Auth::check())
    <p>Te rog să te autentifici</p>
@endunless
```

### Directivele `@empty` și `@isset`

Directiva `@empty` verifică dacă o variabilă este goală, iar `@isset` verifică dacă o variabilă este definită.

Exemplu de utilizare a directivei `@empty`:

```blade
@empty ($name)
    <p>Numele nu este specificat</p>
@endempty
```

Sintaxa directivei `@isset`:

```blade
@isset ($name)
    <p>Nume: {{ $name }}</p>
@endisset
```

### Directivele de autorizare `@auth` și `@guest`

Directiva `@auth` verifică dacă un utilizator este autentificat, iar directiva `@guest` verifică dacă utilizatorul nu este autentificat.

Exemplu de utilizare:

```blade
@auth
    <p>Bun venit, {{ Auth::user()->name }}</p>
@endauth

@guest
    <p>Te rog să te autentifici</p>
@endguest
```

### Directivele `@switch`, `@case`, `@default` și `@break`

Directiva `@switch` este folosită pentru a gestiona multiple condiții.

Exemplu de utilizare:

```blade
@switch($var)
    @case('value1')
        <!-- Cod rulat dacă $var este 'value1' -->
        @break

    @case('value2')
        <!-- Cod rulat dacă $var este 'value2' -->
        @break

    @default
        <!-- Cod rulat dacă niciuna dintre condiții nu este adevărată -->
@endswitch
```

### Directiva `@class`

Una dintre directivele utile în Blade este `@class`, care permite adăugarea de clase elementelor HTML în funcție de condiții.

Exemplu de utilizare:

```blade
<div @class(['class1', 'class2' => $condition])>
    <!-- Conținut -->
</div>
```

Exemplu specific:

```blade
<div @class(['bg-red' => true, 'text-white' => $isError])>
    <!-- Conținut -->
</div>

// Dacă $isError este adevărat, se adaugă clasa 'text-white'
// <div class="bg-red text-white"></div>
```

### Directiva `@for`

Directiva `@for` este folosită pentru a crea bucle.

Exemplu de utilizare:

```blade
@for ($i = 0; $i < 10; $i++)
    <p>Valoare: {{ $i }}</p>
@endfor
```

### Directiva `@foreach`

Directiva `@foreach` este utilizată pentru a itera peste un array.

Exemplu de utilizare:

```blade
@foreach ($posts as $post)
    <p>{{ $post->title }}</p>
    <p>{{ $post->content }}</p>
@endforeach
```

### Directiva `@forelse` și `@empty`

Directiva `@forelse` permite iterația prin array-uri și afișarea unui mesaj dacă array-ul este gol.

Exemplu de utilizare:

```blade
@forelse ($posts as $post)
    <h2>{{ $post->title }}</h2>
    <p>{{ $post->content }}</p>
@empty
    <p>Niciun post găsit</p>
@endforelse
```

### Directiva `@while`

Directiva `@while` este folosită pentru a crea bucle `while`.

Exemplu de utilizare:

```blade
@while (true)
    <p>Bucle infinite</p>
@endwhile
```

### Variabila `loop` în directivele de buclă

În directivele de buclă Blade, este disponibilă variabila `loop`, care oferă informații despre iterația curentă.

Exemplu de utilizare:

```blade
@foreach ($users as $user)
    @if ($loop->first)
        Aceasta este prima iterație.
    @endif

    @if ($loop->last)
        Aceasta este ultima iterație.
    @endif

    <p>Utilizatorul {{ $user->id }}</p>
@endforeach
```

Variabila `loop` oferă multe proprietăți utile:

- `loop->index` — indexul iterației curente (începând de la 0).
- `loop->iteration` — numărul iterației curente (începând de la 1).
- `loop->count` — numărul total de iterații.
- `loop->remaining` — numărul de iterații rămase.
- `loop->depth` — adâncimea de imbricare a buclei.
- `loop->parent` — referință la bucla părinte în bucle imbricate.
- `loop->first` — indicator al primei iterații.
- `loop->last` — indicator al ultimei iterații.

Aceste proprietăți permit un control mai flexibil asupra logicii buclelor în șabloane.

### Încorporarea codului PHP

În cazuri rare, când este necesar să introduci cod PHP în șablon, poți folosi directiva `@php`. Totuși, acest lucru nu este recomandat, deoarece de obicei sugerează că logica ar trebui să fie mutată într-un alt strat al aplicației.

Exemplu de utilizare:

```blade
@php
    $name = 'Ivan';
    echo $name;
@endphp
```

### Comentarii

Blade suportă comentarii care nu vor fi afișate în codul HTML final.

Comentariu pe o singură linie:

```blade
{{-- Acesta este un comentariu --}}
```

## Rularea claselor PHP

Pentru a rula clase PHP în șabloanele Blade, se folosește sintaxa săgeată `->`.

Exemplu de utilizare:

```php
// ...
$user = new User();
$user->name = 'Ivan';

return view('profile', ['user' => $user]);
```

Șablon:

```blade
{{ $user->name }}
```

## Exemplu de utilizare a Blade

Să luăm ca exemplu utilizarea Blade pentru:

1. o pagină care afișează o listă de postări,
2. o pagină individuală pentru fiecare postare.

### Lista de postări

Controllerul `PostController.php`:

```php
namespace App\Http\Controllers;

class PostController extends Controller
{
    public function index()
    {
        $posts = [
            ['title' => 'Postare 1', 'content' => 'Conținutul postării 1'],
            ['title' => 'Postare 2', 'content' => 'Conținutul postării 2'],
            ['title' => 'Postare 3', 'content' => 'Conținutul postării 3']
        ];

        return view('posts', ['posts' => $posts]);
    }
}
```

Șabl

onul `posts.blade.php`:

```blade
<!DOCTYPE html>
<html>
<head>
    <title>Postări</title>
</head>
<body>
    <h1>Postări</h1>
    @forelse ($posts as $post)
        <h2>{{ $post['title'] }}</h2>
        <p>{{ $post['content'] }}</p>
    @empty
        <p>Nicio postare găsită</p>
    @endforelse
</body>
</html>
```

### Postare individuală

Controllerul `PostController.php`:

```php
namespace App\Http\Controllers;

class PostController extends Controller
{
    public function show($id)
    {
        // Obținerea postării după ID
        // De exemplu, din baza de date
        $post = [
            'title' => 'Postare ' . $id,
            'content' => 'Conținutul postării ' . $id
        ];

        return view('post', ['post' => $post]);
    }
}
```

Șablonul `post.blade.php`:

```blade
<!DOCTYPE html>
<html>
<head>
    <title>{{ $post['title'] }}</title>
</head>
<body>
    <h1>{{ $post['title'] }}</h1>
    <p>{{ $post['content'] }}</p>
</body>
</html>
```