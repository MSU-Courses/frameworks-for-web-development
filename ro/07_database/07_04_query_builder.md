# Constructorul de interogări în Laravel

În capitolul anterior am analizat lucrul cu baza de date folosind interogări SQL. Totuși, în Laravel se poate utiliza un instrument special pentru construirea interogărilor — **constructorul de interogări** (query builder) [^1].

**Constructorul de interogări** este un instrument comod care permite crearea interogărilor către baza de date pas cu pas.

Totul începe de la un element principal — tabela la care dorim să accesăm datele. Apoi, ca blocuri de construcție, se adaugă diverse operațiuni: selecție, filtrare, sortare, grupare, unirea tabelelor etc. În final, obținem o interogare completă.

## Obținerea datelor

Pentru a extrage date dintr-o tabelă se folosește metoda `table`:

```php
$users = DB::table('posts')->get();
// Interogare formată: select * from posts

// De asemenea, se putea folosi metoda select('*')
// dar query builder este mai preferabil
DB::select('select * from posts');
```

- `table` este baza de la care începe interogarea, în acest caz selectăm tabela `posts`.
- `get` returnează toate rândurile din tabelă sub forma unui array de **obiecte**.

Folosirea constructorului de interogări face codul mai ușor de citit, de înțeles, sigur și flexibil — simplificând formarea dinamică a interogărilor.

> [!IMPORTANT]  
> În capitolele următoare vom folosi des metoda `get`. Este important de reținut că metoda `get` returnează datele. Fără apelarea acestei metode, interogarea va fi doar construită, dar nu și executată.

## Selectarea datelor

Pentru a selecta anumite coloane dintr-o tabelă, se folosește metoda `select`:

```php
$users = DB::table('posts')
            ->select('title', 'content as text')
            ->get();
// Interogare formată: select title, content as text from posts
```

- `select` permite specificarea coloanelor care trebuie selectate.

Pentru eliminarea duplicatelor se folosește metoda `distinct`:

```php
$users = DB::table('posts')
            ->select('title')
            ->distinct()
            ->get();
// Interogare formată: select distinct title from posts
```

### Adăugarea de coloane cu `addSelect`

Pentru a adăuga coloane suplimentare la cele deja existente, se folosește metoda `addSelect`:

```php
// Interogare formată: select title, content from posts
$query = DB::table('posts')
            ->select('title');

// Dacă este transmis parametrul getContent, adăugăm coloana content
if ($request->has('getContent')) {
      $query->addSelect('content');
}

// Dacă parametrul este transmis, interogarea va fi
//    select title, content from posts
// Dacă nu este transmis, interogarea va fi
//    select title from posts
$posts = $query->get();
```

## Filtrarea datelor

Pentru filtrarea datelor se folosește metoda `where`:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->get();
// În acest caz este utilizat operatorul de egalitate (=)
// Interogare formată: select * from posts where id = 1
```

- `where` permite specificarea unei condiții pentru filtrarea datelor.

Dacă este necesară adăugarea unui operator de comparație, de exemplu, `>=`, se folosește un al doilea parametru:

```php
$users = DB::table('posts')
            ->where('id', '>=', 1)
            ->get();
// Interogare formată: select * from posts where id >= 1
```

Puteți folosi orice operator de comparație: `=`, `>`, `<`, `>=`, `<=`, `<>`, `LIKE`, `BETWEEN`, `IN`, `NOT IN`.

```php
$users = DB::table('posts')
            ->where('title', 'like', 'New%')
            ->get();
// Interogare formată: select * from posts where title like 'New%'
```

### Condiții multiple

Pentru adăugarea mai multor condiții se poate folosi metoda `where` de mai multe ori:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->where('title', 'New Post')
            ->get();
// Interogare formată: select * from posts where id = 1 and title = 'New Post'
```

Sau se poate folosi metoda `where` cu un array:

```php
$users = DB::table('posts')
            ->where([
                ['id', 1],
                ['title', 'New Post']
            ])
            ->get();
// Interogare formată: select * from posts where id = 1 and title = 'New Post'
```

> [!TIP]
> La folosirea metodei `where` de mai multe ori, condițiile sunt unite cu operatorul `AND`.

### Metoda `orWhere`

Pentru utilizarea operatorului `OR` se folosește metoda `orWhere`:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->orWhere('title', 'New Post')
            ->get();
// Interogare formată: select * from posts where id = 1 or title = 'New Post'
```

Funcția `orWhere` poate primi o funcție pentru a combina condițiile:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->orWhere(function($query) {
                $query->where('title', 'New Post')
                      ->where('content', 'Content');
            })
            ->get();
// Interogare formată: select * from posts where id = 1 or (title = 'New Post' and content = 'Content')
```

### Metodele `whereNot`, `whereAny`, `whereAll`

Pentru utilizarea operatorului `NOT` se folosește metoda `whereNot`:

```php
$users = DB::table('posts')
            ->whereNot('id', 1)
            ->get();
// Interogare formată: select * from posts where id != 1
```

Pentru utilizarea operatorului `OR` cu mai multe valori se folosește metoda `whereAny`:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->whereAny([
               'title',
               'description'
            ], 'like', 'New%')
            ->get();
```

**Interogare formată**:

```sql
select *
from posts
where id = 1 and (
   title like 'New%' or
   description like 'New%'
);
```

Pentru utilizarea operatorului `AND` cu mai multe valori se folosește metoda `whereAll`:

```php
$users = DB::table('posts')
            ->where('id', 1)
            ->whereAll([
               'title',
               'description'
            ], 'like', 'New%')
            ->get();
```

**Interogare formată**:

```sql
select *
from posts
where id = 1 and (
   title like 'New%' and
   description like 'New%'
);
```

### Metoda `whereBetween`

Pentru filtrarea datelor într-un interval, se folosește metoda `whereBetween`:

```php
$users = DB::table('posts')
            ->whereBetween('id', [1, 10])
            ->get();
// Interogare formată: select * from posts where id between 1 and 10
```

### Metoda `whereIn`

Pentru filtrarea datelor pe baza unei liste de valori, se folosește metoda `whereIn`:

```php
$users = DB::table('posts')
            ->whereIn('id', [1, 2, 3])
            ->get();
// Interogare formată: select * from posts where id in (1, 2, 3)
```

Metoda `whereIn` poate fi folosită și pentru subinterogări:

```php
$query = DB::table('users')->select('id')
               ->from('posts')
               ->where('id', 1);

$posts = DB::table('posts')
            ->whereIn('user_id', $query)
            ->get();
```

**Interogare formată**:

```sql
select *
from posts
where user_id in (
   select id
   from users
   from posts
   where id = 1
);
```

## Sortarea datelor

### Metoda `orderBy`

Pentru sortarea datelor se folosește metoda `orderBy`:

```php
$users = DB::table('posts')
            ->orderBy('title', 'desc')
            ->get();
// Interogare formată: select * from posts order by title desc
```

- `orderBy` permite specificarea coloanei și a direcției de sortare.

### Metodele `latest`, `oldest`

Pentru sortarea după data

 de creare se folosesc metodele `latest` și `oldest`:

```php
$users = DB::table('posts')
            ->latest()
            ->get();
// Interogare formată: select * from posts order by created_at desc
```

```php
$users = DB::table('posts')
            ->oldest()
            ->get();
// Interogare formată: select * from posts order by created_at asc
```

Pentru selectarea primului sau ultimului rând se folosesc metodele `first` și `last`:

```php
$user = DB::table('posts')
            ->first();
// Interogare formată: select * from posts limit 1
```

```php
$user = DB::table('posts')
            ->last();
// Interogare formată: select * from posts order by id desc limit 1
```

### Metoda `inRandomOrder`

Pentru sortarea aleatorie se folosește metoda `inRandomOrder`:

```php
$users = DB::table('posts')
            ->inRandomOrder()
            ->get();
// Interogare formată: select * from posts order by rand()
```

## Gruparea datelor

Pentru gruparea datelor se folosește metoda `groupBy`:

```php
$users = DB::table('posts')
            ->select('user_id, count(*) as total')
            ->groupBy('user_id')
            ->get();
// Interogare formată: select user_id, count(*) as total from posts group by user_id
```

## Limitarea și offset-ul datelor

Pentru a limita numărul de rânduri returnate se folosește metoda `limit`:

```php
$users = DB::table('posts')
            ->limit(10)
            ->get();
// Interogare formată: select * from posts limit 10
```

Pentru a stabili un offset (decalaj) pentru selecția rândurilor, se folosește metoda `offset`:

```php
$users = DB::table('posts')
            ->offset(10)
            ->get();
// Interogare formată: select * from posts offset 10
```

## Inserarea datelor

Pentru inserarea datelor într-o tabelă se folosește metoda `insert`:

```php
DB::table('posts')
    ->insert([
        'title' => 'New Post',
        'content' => 'Content'
    ]);
```

- `insert` acceptă un array cu datele de inserat.

### Obținerea ID-ului rândului inserat

Dacă dorim să obținem ID-ul rândului inserat, se folosește metoda `insertGetId`:

```php
$id = DB::table('posts')
        ->insertGetId([
            'title' => 'New Post',
            'content' => 'Content'
        ]);
```

## Actualizarea datelor

Pentru actualizarea datelor într-o tabelă se folosește metoda `update`:

```php
DB::table('posts')
    ->where('id', 1)
    ->update([
        'title' => 'Updated Post',
        'content' => 'Updated Content'
    ]);
```

- `update` acceptă un array cu datele de actualizat.

## Ștergerea datelor

Pentru ștergerea datelor dintr-o tabelă se folosește metoda `delete`:

```php
DB::table('posts')
    ->where('id', 1)
    ->delete();
```

- `delete` șterge rândurile care satisfac condiția specificată.

## Actualizare sau inserare de date

Există cazuri în care este necesar să actualizăm datele dacă acestea există sau să inserăm noi date dacă nu există. Pentru aceasta se folosește metoda `updateOrInsert`:

```php
DB::table('posts')
    ->updateOrInsert(
        ['title' => 'New Post'],
        ['content' => 'Content']
    );
```

## Incrementare și decrementare

Uneori este necesar să incrementăm sau să decrementăm valoarea unei coloane cu un anumit număr. Pentru aceasta se folosesc metodele `increment` și `decrement`:

```php
DB::table('posts')
    ->where('id', 1)
    ->increment('views');
```

```php
DB::table('posts')
    ->where('id', 1)
    ->decrement('views');
```

## Depanare

Pentru depanarea interogărilor, puteți folosi panoul de depanare integrat al Laravel. Pentru a-l activa, setați modul de depanare în fișierul `.env`:

```ini
APP_DEBUG=true
```

Sau folosiți următoarele metode:

```php
// Obțineți interogarea SQL formată
$sql = DB::table('posts')->dd();

// Obțineți interogarea SQL formată cu parametrii inserați
$sql = DB::table('posts')->where('id', 5)->orderBy('title')->ddRawSql();
```

> [!NOTE]
> În acest capitol am acoperit metodele de bază ale constructorului de interogări în Laravel. Pentru interogări mai complexe, cum ar fi unirea tabelelor, utilizarea subinterogărilor etc., se recomandă consultarea documentației Laravel.

## Concluzie

Constructorul de interogări în Laravel este un instrument puternic și convenabil pentru lucrul cu baza de date, permițând dezvoltatorilor să creeze interogări SQL flexibile și sigure cu un efort minim.

Utilizarea query builder nu doar că face codul mai lizibil și ușor de înțeles, dar permite și crearea dinamică a interogărilor complexe, reducând riscul injecțiilor SQL.

Cu ajutorul său, puteți efectua cu ușurință selecția, filtrarea, sortarea, gruparea, inserarea, actualizarea și ștergerea datelor, fără a scrie explicit SQL.

Aceasta simplifică semnificativ dezvoltarea aplicațiilor, asigurând în același timp performanță și securitate în lucrul cu bazele de date.

[^1]: Database: Query Builder, laravel.com [online]. URL: https://laravel.com/docs/queries