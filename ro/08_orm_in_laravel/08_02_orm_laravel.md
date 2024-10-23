# ORM în Laravel

În Laravel, se folosește Eloquent ORM, care oferă instrumente convenabile pentru lucrul cu bazele de date [^1].

Eloquent permite lucrul cu tabelele bazelor de date sub formă de obiecte, simplificând astfel interacțiunea cu datele.

Atunci când folosești Eloquent în Laravel, fiecare tabelă din baza de date este reprezentată printr-un model.

**Modelul în Laravel** este o clasă care reprezintă o tabelă din baza de date și permite efectuarea interogărilor asupra acesteia.

## Crearea unui model

Pentru a crea un model în Laravel, se folosește comanda `php artisan make:model ModelName`.

De exemplu, pentru a crea modelul `Post`, executați comanda următoare:

```bash
php artisan make:model Post
```

Dacă doriți să creați modelul împreună cu o migrație, utilizați flag-ul `--migration`:

```bash
php artisan make:model Post --migration
```

Modelele sunt salvate în directorul `app/Models`.

### Cum știe Eloquent care tabelă corespunde modelului?

Eloquent folosește convenții de denumire pentru a determina ce tabelă corespunde unui model.

Prin convenție, numele tabelei va fi numele clasei în format `snake_case` la plural, dacă nu se specifică altceva explicit.

**De exemplu**:

1. Modelul `Post` va corespunde tabelei `posts`.
2. Modelul `UserProfile` va corespunde tabelei `user_profiles`.

Dacă numele tabelei este diferit de convenția Eloquent, îl puteți specifica explicit în model:

```php
class Post extends Model
{
    protected $table = 'my_posts';
}
```

## Cheia primară

Implicit, Eloquent așteaptă ca cheia primară a tabelei să se numească `id` și să fie auto-incrementată.

Dacă numele cheii primare diferă sau nu doriți să fie auto-incrementată, puteți specifica acest lucru în model:

```php
class Post extends Model
{
   // Numele cheii primare
   protected $primaryKey = 'post_id';

   // Cheia primară nu este auto-incrementată
   public $incrementing = false;
}
```

### UUID ca și cheie primară

> [!NOTE]  
> **UUID** (_Universally Unique Identifier_) este un număr pe 128 de biți, garantat unic la nivel global [^2].

Dacă doriți să folosiți UUID ca și cheie primară, utilizați trait-ul `HasUuids`:

```php
class Post extends Model
{
    use HasUuids;

    // ...
}

// De exemplu
$post->id; // 550e8400-e29b-41d4-a716-446655440000
```

## Obținerea datelor

Pentru obținerea datelor în Laravel, nu este necesar să folosiți direct interogări SQL. În schimb, puteți utiliza metodele Eloquent.

### Obținerea tuturor înregistrărilor

```php
$posts = Post::all();
// echivalent cu
$posts = DB::table('posts')->get();
```

### Crearea interogărilor

Metodele Eloquent permit crearea interogărilor folosind constructorul de interogări, pe care l-ați studiat deja.

Acum nu mai folosiți `DB`, ci lucrați direct cu modelul.

```php
$posts = Post::where('status', 'published')
    ->orderBy('created_at', 'desc')
    ->get();
```

Aceste metode fac procesul de lucru cu baza de date mai simplu și mai intuitiv.

### Extracția unei singure înregistrări

Pentru extragerea unei singure înregistrări, puteți folosi metoda `find($id)`, unde `$id` este cheia primară a înregistrării.

```php
// Obține modelul pe baza cheii primare
// Dacă modelul nu este găsit, se returnează null
$post = Post::find(1);
```

Pentru căutarea pe baza unui alt câmp, cum ar fi `slug`, folosiți metoda `first()`:

```php
// Obține modelul pe baza altui câmp
$post = Post::where('slug', 'first-post')->first();
```

Un rezultat similar poate fi obținut cu metoda `firstWhere`:

```php
// Folosiți metoda firstWhere
$post = Post::firstWhere('slug', 'first-post');
```

Dacă trebuie să executați o acțiune când înregistrarea nu este găsită, utilizați metoda `findOr()`, care acceptă o funcție anonimă pentru a gestiona lipsa înregistrării:

```php
// Dacă înregistrarea nu este găsită, se execută funcția transmisă
// În acest caz, se returnează un răspuns 404
$post = Post::findOr(1, function () {
    return response('Post not found', 404);
});
```

Pentru un control mai strict, utilizați metoda `findOrFail($id)`, care va genera o excepție `ModelNotFoundException` dacă înregistrarea nu este găsită:

```php
// Generează o excepție dacă înregistrarea nu este găsită
$post = Post::findOrFail(1);
```

Această metodă este utilă mai ales atunci când doriți să gestionați imediat lipsa datelor, cum ar fi trimiterea automată a unui răspuns de tip eroare 404.

### Utilizarea funcțiilor agregate

Pentru a folosi funcții agregate precum `count`, `sum`, `avg`, `min`, `max`, puteți folosi metodele Eloquent:

```php
// Obține numărul total de înregistrări
$postCount = Post::count();

// Obține numărul de înregistrări publicate
$postCount = Post::where('status', 'published')->count();

// Obține suma valorilor unui câmp
$sum = Post::sum('views');

// Obține valoarea maximă a câmpului views
$maxSum = Post::where('status', 'published')->max('views');
```

## Inserarea datelor

Pentru a insera date în baza de date, folosiți metoda `save()`:

```php
$post = new Post();
$post->title = 'New Post';
$post->content = 'Content';
$post->save();
```

## Actualizarea datelor

Pentru actualizarea unei înregistrări existente, folosiți de asemenea metoda `save()`:

```php
$post = Post::find(1);
$post->title = 'Updated Post';
$post->save();
```

## Atribuirea în masă

> [!NOTE]  
> **Atribuirea în masă (mass assignment)** este procesul prin care mai multe atribute ale unui model sunt atribuite simultan și salvate în baza de date.

În exemplele de creare și actualizare, am folosit metoda `save()`, care funcționează cu o singură înregistrare. În Laravel, pentru crearea sau actualizarea mai multor înregistrări, se folosesc metodele `create()` și `update()`.

### Crearea în masă

Pentru crearea în masă, folosiți metoda `create()`, care acceptă un array de date pentru crearea noilor înregistrări.

Exemplu pentru o singură înregistrare:

```php
Post::create([
   'title' => 'New Post',
   'content' => 'Content',
   'status' => 'draft'
]);
```

Exemplu pentru mai multe înregistrări:

```php
Post::create([
    ['title' => 'Post 1', 'content' => 'Content 1', 'status' => 'draft'],
    ['title' => 'Post 2', 'content' => 'Content 2', 'status' => 'draft'],
    ['title' => 'Post 3', 'content' => 'Content 3', 'status' => 'draft']
]);
```

Dacă datele vin dintr-o cerere (de exemplu, dintr-un formular), le puteți salva în baza de date cu o singură comandă:

```php
// Creare în masă dintr-o cerere
Post::create($request->all());
```

#### Metoda `fill`

Dacă aveți nevoie să creați un obiect al modelului, dar nu doriți să îl salvați imediat, puteți folosi metoda `fill()`. Această metodă atribuie atributele modelului, dar nu îl salvează.

Exemplu de utilizare:

```php
$post = new Post();

$post->fill([
    'title' => 'New Post',
    'content' => 'Content',
    'status' => 'draft'
]);

// După ce datele sunt completate, modelul poate fi salvat
$post->save();
```

Metoda `fill()` oferă flexibilitate în completarea modelului cu date, fiind utilă când trebuie să efectuați acțiuni suplimentare înainte de salvarea înregistrării în baza de date.

### Actualizarea în masă

Pentru actualizarea în masă a înregistrărilor, folosi

ți metoda `update()`, care acceptă un array de date pentru modificarea înregistrărilor existente:

```php
// Actualizarea tuturor înregistrărilor cu status "draft" la "published"
Post::where('status', 'draft')->update(['status' => 'published']);
```

De asemenea, puteți folosi metoda `fill()` pentru actualizări:

```php
// Actualizarea unei înregistrări cu id = 1
$post = Post::find(1)->fill([
   'title' => 'Updated Post',
   'content' => 'Updated Content',
]);
$post->save();
```

## Câmpurile `$fillable` și `$guarded`

Pentru atribuirea în masă a datelor, trebuie să specificați în Laravel ce câmpuri pot fi completate. Acest lucru se face prin proprietățile `$fillable` sau `$guarded` din model. Este important de menționat că folosirea ambelor simultan nu are sens, deoarece se exclud reciproc.

### `$fillable`

Proprietatea `$fillable` definește lista câmpurilor care pot fi completate prin atribuirea în masă. Dacă doriți să permiteți doar anumitor câmpuri să fie atribuite, folosiți `$fillable`.

Exemplu:

```php
class Post extends Model
{
    protected $fillable = ['title', 'content', 'status'];
}
```

### `$guarded`

Pe de altă parte, `$guarded` definește câmpurile care **nu pot** fi atribuite în masă. Toate celelalte câmpuri care nu sunt specificate în `$guarded` vor fi disponibile pentru atribuirea în masă.

Exemplu:

```php
class Post extends Model
{
    protected $guarded = ['is_published'];
}
```

## Ștergerea datelor

Pentru a șterge o înregistrare în Laravel, se folosește metoda `delete()`, însă mai întâi trebuie să obțineți modelul dorit:

```php
$post = Post::find(1);
$post->delete();

// sau într-o singură linie
Post::find(1)->delete();

// sau condiționat
Post::findOrFail(1)->delete();
```

Folosind metoda `destroy()`, puteți șterge înregistrări pe baza cheii primare:

```php
Post::destroy(1);

// sau pentru mai multe înregistrări
Post::destroy([1, 2, 3]);
```

## Concluzie

Eloquent ORM din Laravel oferă un instrument puternic și comod pentru lucrul cu bazele de date, permițând manipularea tabelelor ca obiecte și simplificând interacțiunea cu datele. Prin intermediul comenzilor pentru crearea modelelor, definirea corespondențelor între modele și tabele și metodele pentru lucrul cu înregistrările, dezvoltatorii pot crea, actualiza și șterge date fără a scrie interogări SQL.

[^1]: Eloquent: Getting Started. laravel.com [online]. URL: https://laravel.com/docs/eloquent  
[^2]: The Benefits of Using UUIDs for Unique Identification, tiDB [online]. URL: https://www.pingcap.com/article/the-benefits-of-using-uuids-for-unique-identification/