# Legături între tabele și modele în Eloquent

Din cursul de baze de date, deja știți că în bazele de date relaționale se pot crea legături între tabele. Acest lucru permite unirea datelor din tabele diferite și utilizarea lor împreună, evitând duplicarea datelor și, astfel, normalizând structura bazei de date.

Când vorbim despre legături în contextul Laravel, ele sunt organizate similar cu modul în care le-ați întâlnit în bazele de date relaționale. Totuși, utilizarea ORM (Mapare Obiect-Relatională) adaugă un nivel suplimentar de complexitate, care poate îngreuna înțelegerea acestor legături.

Odată cu ORM apare un strat suplimentar de abstracție — **modelele**, astfel că vom analiza două tipuri de legături:

- **Între tabele** din baza de date — acelea pe care le-ați studiat la cursul de baze de date.
- **Între modele** în Laravel — acestea sunt legăturile care apar în lucrul cu ORM-ul Eloquent și permit gestionarea datelor la nivel de obiecte, nu doar tabele.

## Crearea migrațiilor pentru tabele cu legături

**Legăturile între tabele** în baza de date sunt realizate prin chei externe, pe care deja le-ați studiat la cursul de baze de date. În Laravel, aceste legături sunt definite în migrații în timpul creării tabelelor.

Să luăm ca exemplu tabelele `posts` și `categories`, unde fiecare post aparține unei categorii, iar o categorie poate conține mai multe postări. Aceasta este o legătură clasică **unul la mulți**.

Exemplu de creare a tabelului `posts` cu o cheie externă care se referă la tabelul `categories`:

```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->text('content');
    // Coloana pentru cheia externă
    $table->unsignedBigInteger('category_id');
    $table->timestamps();

    // Definirea cheii externe pentru category_id
    $table->foreign('category_id')->references('id')->on('categories')
          ->onDelete('cascade')
          ->onUpdate('cascade');
});
```

- `foreign('category_id')` – creează o cheie externă pentru câmpul `category_id` din tabelul `posts`.
- `references('id')` – face referire la coloana `id` din tabelul `categories`.
- `on('categories')` – specifică tabelul la care se face referire prin cheia externă.
- `onDelete('cascade')` – șterge toate postările asociate categoriei atunci când aceasta este eliminată (**ștergere în cascadă**).
- `onUpdate('cascade')` – actualizează postările asociate în cazul modificării categoriei (**actualizare în cascadă**).

Acest cod este echivalent cu următoarea interogare SQL:

```sql
CREATE TABLE posts (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255),
    content TEXT,
    category_id BIGINT UNSIGNED,
    created_at TIMESTAMP NULL,
    updated_at TIMESTAMP NULL,

    -- Definirea cheii externe
    FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE CASCADE ON UPDATE CASCADE
);
```

### Utilizarea `foreignId`

Începând cu Laravel 7, pentru simplificarea creării cheilor externe a fost adăugată metoda `foreignId`. Acum nu este necesar să specificăm manual tipul de câmp și să creăm separat cheia externă.

Exemplu de utilizare:

```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->text('content');
    $table->foreignId('category_id')->constrained();
    $table->timestamps();
});
```

- `foreignId('category_id')` – creează un câmp `UNSIGNED BIGINT` pentru cheia externă.
- `constrained()` – stabilește automat tabelul și coloana la care se face referire.

### Tabelul "mulți la mulți"

Pentru o legătură "mulți la mulți" este necesar un tabel intermediar care să conțină cheile externe ale ambelor tabele legate.

Să luăm ca exemplu tabelele `users` și `roles`. Un utilizator poate avea mai multe roluri, iar un rol poate aparține mai multor utilizatori.

Exemplu de creare a tabelului intermediar:

```php
Schema::create('role_user', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained();
    $table->foreignId('role_id')->constrained();
});
```

Această migrație creează un tabel pentru legătura dintre utilizatori și roluri, în care fiecare înregistrare va conține două chei externe: una pentru utilizator și una pentru rol.

## Relații în ORM Eloquent

Așa cum s-a menționat, în Laravel, atunci când lucrăm cu baza de date la nivel de obiecte, apare conceptul de **relații între modele**.

> [!NOTE]
> **Relațiile în Laravel** sunt unul dintre conceptele cheie și, în ciuda complexității lor, sunt extrem de importante pentru a lucra eficient cu ORM Eloquent.

Pentru a defini relațiile între modele în Eloquent se folosesc **metode de relație**. Aceste metode facilitează accesul la datele asociate, de exemplu, toate postările unei categorii sau categoria unei postări specifice.

### Legături directe și inverse

În Eloquent ORM există două **tipuri de relații între modele**: **directe** și **inverse**.

- **Legătură directă** — când un model **are** altul. De exemplu, o categorie **are multe** postări.
- **Legătură inversă** — când un model **aparține** altuia. De exemplu, o postare **aparține** unei categorii.

Aceste relații permit o abordare intuitivă în lucrul cu obiectele, utilizând concepte de "deținere" și "apartenență".

Pentru a defini relațiile între modele în Eloquent, se folosesc metodele `has...` și `belongs...`:

- `has...` — denotă **o legătură directă**, când un model "are" alt model. De exemplu, o categorie **are multe** postări (`hasMany`).
- `belongs...` — denotă **o legătură inversă**, când un model "aparține" altuia. De exemplu, o postare **aparține** unei categorii (`belongsTo`).

Relațiile inverse permit accesarea datelor din modelele asociate. De exemplu, puteți extrage toate postările asociate unei categorii sau afla categoria unei postări specifice.

Logica este simplă: dacă un model **are** alt model, atunci acel model, la rândul său, **aparține** primului model.

### Relația „Unu la unu” / „Are unul”

Relația de tip **„unu la unu”** descrie situația în care un rând dintr-un tabel este asociat **doar cu un rând** dintr-un alt tabel.

Să luăm ca exemplu tabelele `users` și `profiles`. **Fiecare utilizator are un singur profil**, iar **fiecare profil aparține unui singur utilizator**.

> [!NOTE]
> Acest tip de relație este util atunci când dorim să stocăm informații suplimentare (nume, prenume, data nașterii etc.) despre un utilizator într-un tabel separat, astfel încât structura datelor să fie logică și ușor de gestionat.

În baza de date, tabelele ar avea următoarea structură:

- `users` — conține date despre utilizatori:
  - `id`
  - `name`
  - `email`
  - `created_at`
  - `updated_at`

- `profiles` — conține date suplimentare despre utilizatori:
  - `id`
  - `user_id` (cheie externă)
  - `phone`
  - `address`
  - `created_at`
  - `updated_at`

#### Relația modelului: `hasOne`

Metoda `hasOne` este utilizată pentru a defini o relație de tip „unu la unu”, în care un **model** _are_ **un** alt model.

Să analizăm exemplul cu modelele `User` și `Profile`.

În cazul nostru, aceasta înseamnă că **un utilizator** _are un_ **profil**.

Pentru a crea această relație între modele, adăugăm în modelul `User` o metodă `profile()` (numele metodei poate fi ales liber), care va returna relația `hasOne` cu modelul `Profile`.

```php
<?php

// app/Models/User.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model {
    // ...

    public function profile() {
        return $this->hasOne(Profile::class);
    }
}
```

Această metodă spune lui Laravel că fiecare utilizator are un profil. Acum, când apelăm `$user->profile`, Eloquent va găsi automat profilul asociat cu acel utilizator.

#### Relația modelului: `belongsTo`

Metoda `belongsTo` este folosită pentru a crea relația inversă „unu la unu”, în care _un model aparține altui model_. Așadar, modelul `Profile` _aparține_ modelului `User`.

Pentru implementare, adăugăm o metodă `user()` în modelul `Profile`, indicând că fiecare profil este asociat cu un anumit utilizator.

```php
<?php

// app/Models/Profile.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Profile extends Model {

    public function user() {
        return $this->belongsTo(User::class);
    }
}
```

Metoda `user()` informează Laravel că un profil este asociat unui anumit utilizator. Acum, când apelăm `$profile->user`, Eloquent va găsi automat utilizatorul de care aparține acest profil.

În concluzie, avem următoarele:

1. **Metoda `hasOne`** în `User` arată că un utilizator are un profil.
2. **Metoda `belongsTo`** în `Profile` arată că profilul aparține unui utilizator.

Aceste relații permit accesul simplu la profilul unui utilizator (`$user->profile`) sau identificarea utilizatorului căruia îi aparține un anumit profil (`$profile->user`).

> [!TIP]
> Aceste relații fac posibilă crearea și utilizarea cu ușurință a legăturilor între modele în Laravel, facilitând lucrul cu datele într-un mod intuitiv.

### Relația „Unu la mulți” / „Are mai multe”

Relația de tip **„unu la mulți”** descrie situația în care un rând dintr-un tabel poate fi asociat cu mai multe rânduri dintr-un alt tabel.

Să luăm exemplul cu tabelele `categories` și `posts`. **Fiecare categorie poate avea mai multe postări**, iar **fiecare postare aparține unei singure categorii**.

În baza de date, structura tabelelor va fi următoarea:

- `categories` — conține informații despre categorii:
  - `id`
  - `name`
  - `created_at`
  - `updated_at`

- `posts` — conține date despre postări:
  - `id`
  - `category_id` (cheie externă)
  - `title`
  - `content`
  - `created_at`
  - `updated_at`

#### Relația modelului: `hasMany`

Metoda `hasMany` este folosită pentru a defini o relație de tip „unu la mulți”, în care un **model** _are mai multe_ **alte modele**.

Să analizăm exemplul cu modelele `Category` și `Post`.

În cazul nostru, aceasta înseamnă că o **categorie** _are mai multe_ **postări**.

Pentru a crea această relație între modele, adăugăm în modelul `Category` o metodă `posts()` (numele metodei poate fi ales liber), care va returna relația `hasMany` cu modelul `Post`.

```php
<?php

// app/Models/Category.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Category extends Model {
    // ...

    public function posts() {
        return $this->hasMany(Post::class);
    }
}
```

Această metodă spune lui Laravel că fiecare categorie poate avea mai multe postări. Acum, când apelăm `$category->posts`, Eloquent va găsi automat toate postările asociate cu acea categorie.

#### Relația modelului: `belongsTo`

Metoda `belongsTo` este folosită pentru a crea relația inversă „unu la mulți”, în care un **model** _aparține_ **altui model**. Aceasta înseamnă că modelul `Post` _aparține_ modelului `Category`. Acest tip de relație a fost descris în detaliu mai sus.

```php
<?php

// app/Models/Post.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model {

    public function category() {
        return $this->belongsTo(Category::class);
    }
}
```

Metoda `category()` informează Laravel că o postare aparține unei anumite categorii. Acum, când apelăm `$post->category`, Eloquent va găsi automat categoria de care aparține acea postare.

În concluzie, avem următoarele:

1. **Metoda `hasMany`** în `Category` arată că o categorie poate avea mai multe postări.
2. **Metoda `belongsTo`** în `Post` arată că fiecare postare aparține unei singure categorii.

Aceste relații permit accesul simplu la toate postările unei categorii (`$category->posts`) sau identificarea categoriei unei postări specifice (`$post->category`).

### Relația „Mulți la mulți”

Relația **„mulți la mulți”** descrie o situație în care un rând dintr-un tabel poate fi asociat cu mai multe rânduri dintr-un alt tabel și invers.

Pentru acest tip de relație este necesar un tabel intermediar, care să conțină chei externe ale ambelor tabele implicate în relație.

Să luăm ca exemplu tabelele `users` și `roles`. **Un utilizator poate avea mai multe roluri**, iar **un rol poate fi atribuit mai multor utilizatori**.

În baza de date, structura tabelelor va fi următoarea:

- `users` — conține date despre utilizatori:
  - `id`
  - `name`
  - `email`
  - `created_at`
  - `updated_at`

- `roles` — conține informații despre roluri:
  - `id`
  - `name`
  - `created_at`
  - `updated_at`

- `role_user` — tabel intermediar pentru relația dintre utilizatori și roluri:
  - `id`
  - `user_id` (cheie externă)
  - `role_id` (cheie externă)

#### Relația modelului: `belongsToMany`

Metoda `belongsToMany` este folosită pentru a defini o relație de tip **„mulți la mulți”** între modele.

Să analizăm exemplul cu modelele `User` și `Role`.

În cazul nostru, aceasta înseamnă că un **utilizator** _are mai multe_ **roluri**, iar un **rol** _aparține mai multor_ **utilizatori**.

Pentru a crea această relație între modele, adăugăm în modelul `User` o metodă `roles()` (numele metodei poate fi ales liber), care va returna relația `belongsToMany` cu modelul `Role`.

```php
<?php

// app/Models/User.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model {
    // ...

    public function roles() {
        return $this->belongsToMany(Role::class);
    }
}
```

În modelul `Role` trebuie să definim, de asemenea, relația inversă adăugând o metodă `users()`, specificând că fiecare rol poate fi atribuit mai multor utilizatori. Și în cazul unei relații de tip mulți la mulți, relația inversă folosește, de asemenea, `belongsToMany`.

```php
<?php

// app/Models/Role.php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Role extends Model {
    // ...

    public function users() {
        return $this->belongsToMany(User::class);
    }
}
```

Acum putem obține toate rolurile pentru un utilizator (`$user->roles`) sau toți utilizatorii care au un anumit rol (`$role->users`).

### Obținerea datelor asociate

După ce am definit relațiile între modele în Eloquent, putem obține cu ușurință datele asociate.

#### Obținerea datelor pentru relația „unu la unu”

Pentru început, să analizăm relația **„unu la unu”** pe exemplul modelelor `User` și `Profile`. În această relație, fiecărui utilizator îi corespunde un profil și fiecărui profil îi corespunde un utilizator.

Pentru a obține profilul unui anumit utilizator, putem folosi metoda de relație `profile` ca pe o proprietate:

```php
// Obținerea profilului pentru utilizatorul cu id 1
$profile = User::find(1)->profile;
```

Aici, utilizăm `profile` ca **proprietate** — Eloquent execută automat interogarea pentru a obține profilul asociat fără a fi necesară apelarea metodei.

Similar, pentru a obține utilizatorul unui profil:

```php
// Obținerea utilizatorului pentru profilul cu id 1
$user = Profile::find(1)->user;
```

#### Obținerea datelor pentru relația „unu la mulți”

În relația **„unu la mulți”**, o înregistrare dintr-un model este asociată cu mai multe înregistrări din alt model. De exemplu, modelul `Category` poate avea mai multe înregistrări în modelul `Post`, iar fiecare postare aparține unei categorii.

Pentru a obține toate postările pentru o anumită categorie, putem utiliza `posts` ca **proprietate**:

```php
// Obținerea tuturor postărilor pentru categoria cu id 1
$category = Category::find(1);
$posts = $category->posts;
```

Dacă dorim să afișăm titlurile tuturor postărilor dintr-o categorie:

```php
foreach ($posts as $post) {
    echo $post->title;
}
```

Pentru a obține categoria unei postări specifice:

```php
// Obținerea categoriei pentru postarea cu id 1
$post = Post::find(1);
$category = $post->category;
```

#### Obținerea datelor pentru relația „mulți la mulți”

Relația **„mulți la mulți”** înseamnă că o înregistrare dintr-un model poate fi asociată cu mai multe înregistrări din alt model și invers. Un exemplu de acest tip de relație este între modelele `User` și `Role`, unde un utilizator poate avea mai multe roluri, iar un rol poate fi atribuit mai multor utilizatori.

Pentru a obține toate rolurile pentru un anumit utilizator, putem utiliza `roles` ca **proprietate**:

```php
// Obținerea tuturor rolurilor pentru utilizatorul cu id 1
$user = User::find(1);
$roles = $user->roles;
```

Pentru a afișa numele tuturor rolurilor unui utilizator:

```php
foreach ($roles as $role) {
    echo $role->name;
}
```

Pentru a obține toți utilizatorii care au un anumit rol:

```php
// Obținerea tuturor utilizatorilor pentru rolul cu id 1
$role = Role::find(1);
$users = $role->users;
```

#### Utilizarea tabelului pivot

Pentru relațiile „mulți la mulți”, Eloquent creează automat un **tabel pivot** care stochează legăturile dintre modele. De exemplu, pentru `User` și `Role`, tabelul pivot poate fi `role_user`, care conține doar `user_id` și `role_id`. Uneori, tabelul pivot poate conține câmpuri suplimentare, cum ar fi `created_at`.

Pentru a accesa datele din tabelul pivot, utilizați proprietatea `pivot`:

```php
// Obținerea tuturor rolurilor pentru utilizator și a datelor din tabelul pivot
$user = User::find(1);
foreach ($user->roles as $role) {
    echo $role->pivot->created_at; // accesarea datelor din tabelul pivot
}
```

Pentru a include câmpuri specifice din tabelul pivot, adăugați `withPivot` în definirea relației:

```php
// În modelul User
public function roles()
{
    return $this->belongsToMany(Role::class)->withPivot('created_at');
}
```

Acum, puteți obține câmpul `created_at` din tabelul pivot atunci când lucrați cu rolurile unui utilizator.

#### Constructor de interogări pentru relații

Eloquent permite utilizarea unui **constructor de interogări** pentru a filtra, sorta sau efectua alte operațiuni asupra datelor asociate. Pentru aceasta, apelați metoda de relație ca **metodă** (cu paranteze `()`), adăugând condițiile necesare:

```php
$publishedPosts = Category::find(1)->posts()
    ->where('is_published', true)
    ->orderBy('created_at', 'desc')
    ->get();
```

În acest exemplu, `posts()` este folosit ca metodă, ceea ce permite adăugarea de condiții și ordonarea rezultatelor, ceea ce nu este posibil atunci când relația este apelată ca proprietate.

#### Utilizarea relațiilor ca metodă sau ca proprietate

Când folosim relația ca **proprietate** (`$user->profile`), Eloquent execută interogarea în momentul accesării. Aceasta este utilă pentru obținerea simplă a datelor, dar dacă este necesară adăugarea de condiții sau sortare, trebuie să utilizăm relația ca **metodă**.

De exemplu:

```php
// Utilizarea relației ca metodă pentru adăugarea de condiții
$publishedPosts = Category::find(1)->posts()
    ->where('is_published', true)
    ->orderBy('created_at', 'desc')
    ->get();
```

Aici, `posts()` este folosit ca **metodă**, permițând personalizarea interogării și obținerea doar a datelor necesare.

### Interogarea relațiilor existente

Atunci când lucrăm cu modele, uneori trebuie să selectăm doar înregistrările care au date asociate.

De exemplu, putem dori să obținem doar acele categorii care au cel puțin o postare asociată. Eloquent oferă metoda convenabilă `has` pentru acest scop.

```php
$categoriesWithPosts = Category::has('posts')->get();
```

Această interogare va returna doar acele categorii care au postări asociate. Dacă dorim să adăugăm o condiție, de exemplu, să selectăm categoriile care au mai mult de o postare, putem face astfel:

```php
$categoriesWithMultiplePosts = Category::has('posts', '>', 1)->get();
```

Astfel, putem filtra cu ușurință înregistrările bazându-ne pe existența datelor asociate.

### Încărcare anticipată (Eager Loading)

Încărcarea anticipată sau **Eager Loading** ajută la rezolvarea unei probleme numite „N+1 query problem”. Această problemă apare atunci când mai întâi interogăm înregistrările principale (de exemplu, categorii) și apoi, pentru fiecare dintre acestea, efectuăm o interogare separată pentru a obține datele asociate (de exemplu, postări).

#### Problema N+1 query

Să presupunem că dorim să obținem o listă de categorii și, pentru fiecare categorie, să obținem toate postările asociate. Fără `Eager Loading`, interogările ar arăta astfel:

```php
$categories = Category::all(); // Prima interogare
foreach ($categories as $category) {
    $posts = $category->posts; // Interogare suplimentară pentru fiecare categorie
}
```

Aici, `Category::all()` execută o interogare, iar apoi, pentru fiecare categorie, Eloquent face o interogare separată pentru a obține postările. Astfel, dacă avem 10 categorii, vom avea 11 interogări (1 pentru categorii + 10 pentru postările fiecărei categorii). Dacă numărul de categorii este mare, numărul de interogări crește rapid, încetinind aplicația.

Pentru a evita acest lucru, putem folosi `Eager Loading` pentru a încărca postările pentru toate categoriile dintr-o singură interogare:

```php
$categories = Category::with('posts')->get();
```

Acum, Eloquent va executa doar două interogări: una pentru categorii și una pentru toate postările asociate acestor categorii. Aceasta rezolvă eficient problema N+1 query și îmbunătățește performanța aplicației.

#### Când datele asociate nu sunt necesare

Uneori, este suficient să obținem doar înregistrările principale, fără datele asociate. De exemplu, dacă dorim să afișăm doar lista categoriilor pentru filtrare sau selecție, nu este necesar să încărcăm postările pentru fiecare categorie, deoarece avem nevoie doar de informațiile de bază.

```php
$categories = Category::all();
```

În acest caz, `Eager Loading` nu este utilizat, deoarece nu sunt necesare date din alte tabele. Economisim resurse și evităm interogările suplimentare, încărcând doar acele date necesare pentru sarcina specifică.

### Inserarea și actualizarea datelor asociate

Eloquent permite și inserarea sau actualizarea ușoară a datelor asociate. De exemplu, dacă avem o categorie și dorim să îi adăugăm o postare nouă, putem proceda astfel:

```php
$category = Category::find(1);
$post = new Post([
    'title' => 'Noua postare',
    'content' => 'Conținutul postării...'
]);

$category->posts()->save($post);
```

Aici, am creat o postare nouă și am asociat-o cu categoria. Eloquent va seta automat `category_id` pentru noua postare. Actualizarea datelor asociate se face la fel de simplu. De exemplu, pentru a schimba titlul unei postări, putem proceda astfel:

```php
$post = Post::find(101);
$post->title = 'Titlu actualizat';
$post->save();
```

Astfel, putem gestiona cu ușurință datele asociate între ele.

### Încărcarea datelor asociate

Când folosim un ORM ca Eloquent, putem încărca datele asociate simplu și direct prin relații. Aceasta ne permite să accesăm nu doar înregistrările principale, ci și datele din tabelele asociate. De exemplu, încărcând categoriile împreună cu postările, putem obține toate postările asociate fiecărei categorii imediat, ceea ce simplifică și accelerează lucrul cu datele.

### Concluzie

Relațiile între tabele și modele în Eloquent sunt un instrument puternic pentru lucrul cu datele, evitând interogările excesive și facilitând accesul la datele asociate. Înțelegerea modului în care funcționează relațiile între tabele și modele ajută la utilizarea eficientă a Laravel-ului și face codul mai structurat și mai ușor de înțeles.

În acest capitol, am învățat cum să creăm relații la nivelul bazei de date prin migrații și cum să definim relații la nivelul modelelor în Laravel. De asemenea, am abordat aspecte importante, cum ar fi problema „N+1 query”, rezolvarea acesteia prin încărcarea anticipată (`Eager Loading`), și am învățat să inserăm și să actualizăm datele asociate.

ORM-ul Eloquent încarcă automat datele asociate la apelarea metodelor de relație, oferind dezvoltatorilor un mod simplu și convenabil de a lucra cu datele. Astfel, putem obține rapid informațiile necesare din tabelele asociate, evitând scrierea manuală a unor interogări SQL complexe.