# Divizarea în componente

Așa cum am menționat anterior, unul dintre principalele avantaje ale șablonizatoarelor este capacitatea de a separa codul și de a-l reutiliza.

În această secțiune, vom explora cum să împărțim un șablon în componente și diverse modalități de a le folosi.

## Ce este o componentă?

**Componenta** este o parte a unui șablon care poate fi reutilizată în diferite locuri din proiectul tău[^3].

Componentele pot fi simple sau complexe.

- **Exemple de componente simple**: buton, iconiță, titlu.
- **Exemple de componente complexe**: card de produs, formular de contact, meniu.

> [!NOTE]  
> O componentă poate conține alte componente, ceea ce permite crearea de interfețe complexe din părți simple. De exemplu, un card de produs poate include un buton "Adaugă în coș".

Atunci când există mult cod HTML și logică pe o pagină, împărțirea în componente ajută la simplificarea codului și îmbunătățirea considerabilă a lizibilității acestuia.

## Când ar trebui să împarți un șablon în componente?

Una dintre marile întrebări _filosofice_ care apare în dezvoltarea șabloanelor este _când ar trebui să împarți un șablon în componente_, adică în părți mai mici.

Nu există un răspuns unic la această întrebare, dar există câteva recomandări care te pot ajuta să iei decizia:

- **Reutilizabilitate**: dacă o parte din șablon poate fi folosită în locuri diferite din proiect, acesta este un indiciu clar că ar trebui transformată într-o componentă.
- **Complexitate și dimensiune**: dacă o parte din șablon conține mult cod HTML și logică complexă, împărțirea în componente va simplifica codul și va îmbunătăți lizibilitatea acestuia.

Să vedem un exemplu de împărțire a șablonului în componente.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Lista postărilor</title>
  </head>
  <body>
    @foreach ($posts as $post)
    <div class="post">
      <h2>{{ $post->title }}</h2>
      <p>{{ $post->content }}</p>
      <span>{{ $post->author }}</span>
    </div>
    @endforeach
  </body>
</html>
```

În acest exemplu, fiecare postare conține un titlu, conținut și autor. Aceste elemente pot fi extrase într-o componentă pentru a simplifica codul și a îmbunătăți lizibilitatea acestuia.

```html
<!-- Componenta "Postare" -->
<div class="post">
  <h2>{{ $post->title }}</h2>
  <p>{{ $post->content }}</p>
  <span>{{ $post->author }}</span>
</div>
```

De ce crezi că în acest caz împărțirea în componente îmbunătățește codul?

## Împărțirea în componente în Laravel [^1]

În Laravel Blade, există multe modalități de a împărți un șablon în componente: de la soluții simple la unele mai complexe.

### Moștenirea șabloanelor

Unul dintre cele mai puternice instrumente ale șablonizatoarelor, cum ar fi Laravel Blade, este **moștenirea șabloanelor**.

Esența acestui mecanism este simplă: creezi un șablon de bază (`layout`) care include elemente comune pentru toate paginile site-ului tău, cum ar fi structura, meniul de navigare, subsolul, etc. Apoi creezi șabloane derivate care moștenesc șablonul de bază și adaugă elemente unice.

Poți crea mai multe layout-uri pentru diferite părți ale site-ului, de exemplu, unul pentru partea de utilizator și altul pentru panoul de administrare.

De obicei, layout-urile sunt stocate în directorul `resources/views/layouts`.

#### Exemplu de șablon de bază și șablon derivat

Să vedem un exemplu de creare a unui șablon de bază `app.blade.php` și un șablon derivat `home.blade.php`.

```blade
<!-- resources/views/layouts/app.blade.php -->
<!-- Layout-ul de bază al aplicației cu elemente comune -->
<!DOCTYPE html>
<html>
  <head>
    <title>@yield('title')</title>
    <!-- Conectarea stilurilor și scripturilor -->
    <link rel="stylesheet" href="styles.css">
    <script src="script.js"></script>
  </head>
  <body>
   <header>...</header>
    <main>
      <h1>@yield('title')</h1>
      @yield('content')
    </main>
    <footer>...</footer>
  </body>
</html>
```

Directiva `@yield` specifică locurile în care conținutul șabloanelor derivate va fi inserat. Aceste zone pot fi suprascrise în șabloanele derivate.

```blade
<!-- resources/views/home.blade.php -->
<!-- Șablon derivat care moștenește layout-ul -->
@extends('layouts.app')

@section('title', 'Pagina principală')

@section('content')
  <p>Bine ai venit pe pagina principală!</p>
  <p>Numele tău este: {{ $username }}</p>
@endsection
```

În șablonul `home.blade.php`, directiva `@extends` se folosește pentru a moșteni layout-ul `app.blade.php`. Directivele `@section` permit definirea conținutului care va fi inserat în zonele corespunzătoare din layout-ul de bază.

Directiva `@section` poate fi folosită atât pentru blocuri scurte, cât și pentru blocuri lungi de cod.

#### Cum funcționează?

Datorită moștenirii șablonului `app.blade.php`, conținutul șablonului derivat `home.blade.php` va fi inserat automat în zonele specificate ale șablonului de bază:

```blade
<!DOCTYPE html>
<html>
  <head>
    <title>Pagina principală</title>
    <link rel="stylesheet" href="styles.css">
    <script src="script.js"></script>
  </head>
  <body>
   <header>...</header>
    <main>
      <h1>Pagina principală</h1>
      <p>Bine ai venit pe pagina principală!</p>
      <p>Numele tău este: {{ $username }}</p>
    </main>
    <footer>...</footer>
  </body>
</html>
```

Astfel, **nu va trebui să duplici același cod pe fiecare pagină**, ceea ce simplifică considerabil întreținerea și dezvoltarea.

De exemplu, poți conecta fișierele JS sau CSS în șablonul de bază și acestea vor fi disponibile automat pe toate paginile care moștenesc acel șablon (_așa cum este demonstrat mai sus_).

### Directiva `@include`

Una dintre cele mai simple modalități de a împărți un șablon în componente este folosirea directivei `@include`.

> [!NOTE]  
> Utilizarea directivei `@include` este o metodă simplă și eficientă de a împărți șablonul în componente.

Directiva `@include` îți permite să inserezi conținutul unui șablon în altul, făcând codul mai modular și ușor de reutilizat.

Sintaxa generală a directivei `@include`:

```blade
@include('component-name', ['data1' => $data1, 'data2' => $data2, ...])
```

Să vedem un exemplu de utilizare a directivei `@include` pentru a insera componenta „Postare” în șablonul „Lista postărilor”:

```blade
<!-- resources/views/posts.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>Lista postărilor</title>
</head>
<body>
    @foreach ($posts as $post)
        @include('components.post', ['post' => $post])
    @endforeach
</body>
</html>
```

```blade
<!-- resources/views/components/post.blade.php -->
<div class="post">
    <h2>{{ $post->title }}</h2>
    <p>{{ $post->content }}</p>
    <span>{{ $post->author }}</span>
</div>
```

Astfel, directiva `@include` inserează conținutul componentei specificate în locul în care este apelată, făcând codul mai structurat și reutilizabil.

Cu ajutorul directivei `@includeWhen`, poți include o componentă în șablon doar dacă o anumită condiție este îndeplinită:

```blade
@includeWhen($condition, 'component-name', ['data1' => $data1, 'data2'

 => $data2, ...])
```

Dacă condiția `$condition` este adevărată, componenta va fi adăugată în șablon.

### Componentele Blade

După cum am menționat anterior, directiva `@include` este o metodă simplă de a insera conținutul unui șablon în altul. Totuși, în aplicațiile moderne, această metodă este utilizată mai rar.

În Laravel Blade, există un instrument mai puternic și mai flexibil pentru a lucra cu șabloanele — **componentele Blade**.

> [!NOTE]  
> În crearea acestei funcționalități, dezvoltatorii s-au inspirat din componentele din Vue.

Există două metode principale pentru a crea componente Blade:

- **Componente bazate pe clase**;
- **Componente anonime**.

Să explorăm fiecare dintre ele.

### Componente bazate pe clase

Componentele bazate pe clase în Laravel pot fi create manual sau folosind comanda Artisan. Să vedem cum putem crea o componentă folosind comanda Artisan.

#### Crearea unei componente bazate pe clase

Pentru a crea o componentă bazată pe clase, folosește comanda Artisan `make:component`:

```bash
php artisan make:component Post
```

Această comandă va crea componenta `Post` în directorul `app/View/Components` și șablonul corespunzător în `resources/views/components`.

#### Utilizarea componentei bazate pe clase

După crearea componentei, aceasta poate fi utilizată în șabloane folosind directiva `x-component`, unde numele componentei este scris în format _kebab-case_:

```blade
<x-post />
```

```blade
<!-- resources/views/components/post.blade.php -->
<div class="post">
   <!-- Conținut -->
</div>
```

Dacă componenta ta se află în subdirectoare, specifică calea folosind notația cu punct:

```blade
// resources/views/components/blog/post.blade.php
<x-blog.post />
```

#### Transmiterea datelor către componentă

La crearea componentei, se generează o clasă care poate accepta date prin proprietăți transmise în constructor. Este important de menționat că, dacă în constructor sunt folosite proprietăți în format _camelCase_, în șablon acestea se transmit prin atribute în format _kebab-case_.

Exemplu:

```php
// app/View/Components/Post.php

class Post extends Component
{
   public string $title;
   public string $description;
   public string $authorName;

    public function __construct(string $title, string $description, string $authorName)
    {
        $this->title = $title;
        $this->description = $description;
        $this->authorName = $authorName;
    }

    public function render()
    {
        return view('components.post');
    }
}
```

În acest exemplu, componenta acceptă trei proprietăți: `title`, `description` și `authorName`, care sunt transmise prin constructor.

Pentru a transmite date în componentă, folosește atribute, unde formatul _camelCase_ din constructor se transformă în _kebab-case_ în șablon:

```blade
<x-post title="Titlul postării" description="Descrierea postării" author-name="Nick" />
```

Dacă valorile sunt stocate în variabile, poți folosi sintaxa Blade:

```blade
<x-post
   :title="$post->title"
   :description="$post->description"
   :author-name="$post->author"
/>
```

Componenta în sine va arăta astfel:

```blade
<!-- resources/views/components/post.blade.php -->
<div class="post">
    <h2>{{ $title }}</h2>
    <p>{{ $description }}</p>
    <span>{{ $authorName }}</span>
</div>
```

#### Atributele componentei

Adesea este necesar să adaugi componentei atribute standard, cum ar fi clasa sau id-ul. Pentru aceasta, poți folosi variabila `$attributes` în șablonul componentei:

```blade
<!-- resources/views/components/post.blade.php -->
<div {{ $attributes }}>
      <!-- Conținut -->
</div>
```

Acum poți transmite atribute componentei astfel:

```blade
// În șablon, $attributes va include class="post" și id="post-1"

<x-post class="post" id="post-1" />
```

Astfel, toate atributele suplimentare transmise componentei vor fi adăugate automat în marcajul HTML al acesteia.

### Componente anonime [^2]

**Componentele anonime** sunt o altă modalitate de a crea componente în Laravel Blade. Ele permit crearea componentelor direct în șablon, fără a fi necesar să creezi o clasă separată.

Această metodă este utilă atunci când componenta are o logică simplă și nu necesită procesări complexe ale datelor. Componentele anonime se creează doar cu ajutorul șabloanelor și se află în directorul `resources/views/components`.

De asemenea, poți folosi comanda `artisan` pentru a crea o componentă anonimă cu ajutorul opțiunii `--view`:

```bash
php artisan make:component post --view
```

#### Utilizarea unei componente anonime

Componentele anonime sunt create folosind directiva `x-component`:

```blade
<!-- resources/views/components/post.blade.php -->
<div>
   <h2>{{ $title }}</h2>
   <p>{{ $description }}</p>
   <span>{{ $authorName }}</span>
</div>
```

```blade
<!-- resources/views/posts.blade.php -->
<x-post title="Titlul postării" description="Descrierea postării" author-name="Nick" />
```

#### Prop-urile unei componente anonime

Uneori, este necesar să setezi valori implicite pentru proprietățile (prop-urile) componentei sau să faci ca IDE-ul să sugereze parametrii disponibili pentru a fi transmiși în componentă.

> [!IMPORTANT]
> Definirea clară a parametrilor folosiți îmbunătățește și lizibilitatea codului.

Pentru aceasta, în componentele anonime se folosesc **prop-urile** (de la „proprietăți”).

**Prop-urile** sunt datele transmise unei componente. În componentele anonime, acestea sunt transmise prin atribute.

Exemplu de utilizare a prop-urilor cu valori implicite:

```blade
@props([
   'title' => 'Titlu implicit',
   'description' => 'Descriere implicită',
   'authorName' => 'Autor implicit'
])

<div>
   <h2>{{ $title }}</h2>
   <p>{{ $description }}</p>
   <span>{{ $authorName }}</span>
</div>
```

```blade
<x-post title="Titlul postării" />
// Rezultatul:
// <h2>Titlul postării</h2>
// <p>Descriere implicită</p>
// <span>Autor implicit</span>
```

Dacă un prop nu este transmis la apelarea componentei, va fi folosită valoarea sa implicită. Acest lucru face componenta mai flexibilă și simplifică utilizarea acesteia.

Definirea valorilor implicite permite să nu transmiți toate prop-urile atunci când nu este necesar.

De asemenea, poți defini prop-uri fără valori implicite:

```blade
@props(['title', 'description', 'authorName'])

// ...
```

În acest caz, aceste prop-uri vor fi obligatorii pentru a fi transmise la apelarea componentei.

```blade
// Eroare: prop-urile obligatorii nu sunt transmise
<x-post title="Titlul postării" />
```

### Unirea atributelor

În componente, este adesea necesar să adaugi atribute, indiferent dacă acestea au fost sau nu transmise la apelarea componentei.

Pentru aceasta, poți folosi metoda `merge`, care unește atributele transmise cu cele suplimentare:

```blade
<!-- resources/views/components/post.blade.php -->
<div {{ $attributes->merge(['class' => 'bg-gray-100']) }}>
   <!-- Conținut -->
</div>
```

Acum, la apelarea componentei, poți transmite atribute suplimentare:

```blade
<x-post class="text-center" />

// Rezultatul:
// <div class="bg-gray-100 text-center">
```

Astfel, clasa `bg-gray-100` va fi adăugată întotdeauna, iar clasele transmise, precum `text-center`, se vor uni cu aceasta.

### Sloturile componentelor

**Sloturile** sunt o funcționalitate puternică și utilă a componentelor, care permit transmiterea nu doar a datelor, ci a unor întregi fragmente de conținut care pot fi inserate în locul dorit din interiorul componentei.

#### Cum funcționează sloturile

Slotul este locul din componentă unde va fi inserat conținutul transmis. Acest lucru face componenta mai flexibilă și universală. Pentru a folosi slotul într-un șablon de componentă, se folosește variabila `$slot`.

Exemplu:

```blade
<!-- resources/views/components/alert.blade.php -->
<div class="alert">
   <

h2>{{ $title }}</h2>
   <div>{{ $slot }}</div>
</div>
```

În acest șablon, conținutul transmis componentei va fi inserat în locul variabilei `$slot`.

#### Exemplu de utilizare a sloturilor

La apelarea componentei, poți transmite atât date (de exemplu, titlul), cât și conținutul care va fi inserat în slot:

```blade
<x-alert title="Avertisment">
   <!-- Acest conținut va fi inserat în locul variabilei $slot -->
   <strong>Aceasta este o avertizare!</strong>
   <p>Text suplimentar</p>
</x-alert>
```

Rezultatul:

```html
<div class="alert">
  <h2>Avertisment</h2>
  <div>
    <!-- Acest conținut va fi inserat în locul variabilei $slot -->
    <strong>Aceasta este o avertizare!</strong>
    <p>Text suplimentar</p>
  </div>
</div>
```

Astfel, slotul îți permite să inserezi conținut variat într-o componentă, făcând-o universală.

Acest lucru este deosebit de util atunci când trebuie să transmiți atât date statice, cât și conținut dinamic.

#### Sloturi denumite

**Sloturile denumite** îți permit să transmiți mai multe fragmente diferite de conținut unei componente. Acest lucru este util atunci când componenta trebuie să aibă mai multe zone pentru inserarea datelor.

Să vedem un exemplu de componentă cu sloturi denumite:

```blade
<!-- resources/views/components/form.blade.php -->
<form>
   {{ $slot }} <!-- Conținutul principal -->
</form>

<div id="buttons">
   {{ $buttons }} <!-- Conținut suplimentar (de exemplu, butoane) -->
</div>
```

În acest exemplu:

- `$slot` — este slotul principal, în care va fi inserat conținutul principal.
- `$buttons` — este un slot denumit pentru conținut suplimentar, cum ar fi butoanele.

#### Utilizarea sloturilor denumite

Pentru a transmite conținut în sloturile denumite, folosește directiva `x-slot` cu specificarea numelui slotului.

Exemplu:

```blade
<x-form>
   <x-slot:buttons>
      <button>Trimite</button>
   </x-slot:buttons>

   <input type="text" name="name" />
</x-form>
```

În acest exemplu:

- Conținutul din interiorul directivei `<x-slot:buttons>` va fi inserat în slotul `$buttons`.
- Restul conținutului transmis componentei va fi inserat în slotul principal `$slot`.

## `@include` vs. componentele Blade

După cum putem observa, în Laravel Blade există mai multe modalități de a diviza un șablon în componente: directiva `@include`, componente bazate pe clase și componente anonime.

Utilizează directiva `@include` dacă:

1. Ai nevoie să incluzi un fragment simplu de HTML (de exemplu, footer, header sau meniu) care nu conține logică complexă;
2. O parte a șablonului nu necesită cod reutilizabil și poate rămâne statică sau minim dinamică.

În celelalte cazuri, se recomandă utilizarea componentelor Blade, deoarece acestea oferă numeroase avantaje:

## Exemplu de împărțire a șablonului în componente

Să vedem un exemplu de împărțire a unui șablon în componente în Laravel. Acest tip de abordare ajută la organizarea codului, îl face mai lizibil și ușurează întreținerea.

### `layouts/app.blade.php` — șablonul de bază:

```blade
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title')</title>
</head>
<body>
    <header>
        <!-- Header -->
    </header>

    <main>
        @yield('content')
    </main>

    <footer>
        <!-- Footer -->
    </footer>
</body>
</html>
```

### `auth.blade.php` — șablonul paginii de autentificare:

```blade
@extends('layouts.app')

@section('title', 'Autentificare')

@section('content')
    <x-auth.login-form />
@endsection
```

### `components/forms/text-input.blade.php` — componenta câmpului text:

```blade
@props([
   'name',
   'type' => 'text',
   'isError' => false,
])

<input
   type="{{ $type }}"
   name="{{ $name }}"
   placeholder="{{ $placeholder }}"
   {{ $attributes->class(['error' => $isError]) }}
/>
```

### `components/forms/form.blade.php` — componenta formularului:

```blade
<form {{ $attributes }}>
   {{ $slot }}
</form>

<div id="buttons">
   {{ $extraButtons }}
</div>

<ul>
   <li><a href="{{ route('user.agreement') }}">Acordul utilizatorului</a></li>
   <li><a href="{{ route('user.policy') }}">Politica de confidențialitate</a></li>
</ul>
```

### `auth/login-form.blade.php` — componenta formularului de autentificare:

```blade
<x-forms.form {{-- restul atributelor formularului --}} >
   <x-forms.text-input name="login" placeholder="Login" />

   @error('login')
      <p class="error">{{ $message }}</p>
   @enderror

   <x-forms.text-input name="password" placeholder="Parolă" type="password" />

   @error('password')
      <p class="error">{{ $message }}</p>
   @enderror

   <button>Autentificare</button>

   <x-slot:extraButtons>
      <a href="/forgot-password">Ai uitat parola?</a>
   </x-slot>
</x-forms.form>
```

Acest exemplu arată cum poți împărți un șablon în componente, simplificând considerabil întreținerea și îmbunătățind lizibilitatea codului. Poți reutiliza componentele și poți actualiza cu ușurință diferite părți ale aplicației fără a modifica tot șablonul.

[^1]: Components, laravel.com [online]. URL: https://laravel.com/docs/10.x/blade#components  
[^2]: Anonymous Components, laravel.com [online]. URL: https://laravel.com/docs/10.x/blade#anonymous-components  
[^3]: 5 Ways Component-Based Design Empowers Web Development, it-consultis.com [online]. URL: https://it-consultis.com/blog/component-based-design/