# Utilizarea bazei de date în Laravel

După crearea migrațiilor, puteți începe să lucrați cu baza de date.

În Laravel, putem interacționa direct cu baza de date folosind instrumentele integrate ale framework-ului.

## Executarea interogărilor SQL

Pentru executarea interogărilor SQL în Laravel, se folosește clasa `DB`. Aceasta permite efectuarea interogărilor de tipul: `select`, `insert`, `update`, `delete` și `statement` [^1].

### Interogarea `select`

Exemplu de utilizare:

```php
// $posts - un array de obiecte stdClass
$posts = DB::select('select * from posts where published = ?', [true]);

// utilizarea mai multor condiții
$posts = DB::select('select * from posts where published = ? and author_id = ?', [true, 1]);
```

După cum se observă, al doilea parametru este un array de valori care vor fi plasate în locul semnului `?` în interogare. Acest lucru ajută la protecția împotriva injecțiilor SQL. Aceste valori pot fi obținute, de exemplu, dintr-un formular sau alte surse externe.

Exemplu de utilizare într-un controller:

```php
<?php

class PostController extends Controller
{
    public function index(int $isPublished)
    {
        $posts = DB::select('select * from posts where published = ?', [$isPublished]);

        return view('posts.index', ['posts' => $posts]);
    }
}
```

Astfel, puteți lucra în siguranță și eficient cu baza de date prin interogări SQL în Laravel.

#### Utilizarea parametrilor denumiți

În loc să utilizați semnul `?`, puteți folosi parametri denumiți în interogări:

```php
$posts = DB::select('select * from posts where id = :id', ['id' => 1]);
```

Acest lucru face codul mai lizibil și ușor de înțeles, în special atunci când se folosesc mai mulți parametri.

### Interogarea `insert`

Pentru adăugarea de înregistrări în baza de date, se utilizează metoda `insert`:

```php
DB::insert('insert into posts (title, content) values (?, ?)', ['New Post', 'Content']);

// sau folosind parametri denumiți
DB::insert('insert into posts (title, content) values (:title, :content)', ['title' => 'New Post', 'content' => 'Content']);
```

Al doilea parametru este un array de valori care vor fi inserate în interogare. Această abordare este similară metodei `select` și ajută la evitarea injecțiilor SQL.

### Interogarea `update`

Pentru actualizarea înregistrărilor din baza de date, se utilizează metoda `update`:

```php
DB::update('update posts set title = ? where id = ?', ['Updated Post', 1]);

// sau folosind parametri denumiți
DB::update('update posts set title = :title where id = :id', ['title' => 'Updated Post', 'id' => 1]);
```

### Interogarea `delete`

Pentru ștergerea înregistrărilor din baza de date, se utilizează metoda `delete`:

```php
DB::delete('delete from posts where id = ?', [1]);

// sau folosind parametri denumiți
DB::delete('delete from posts where id = :id', ['id' => 1]);
```

### Interogarea `statement`

Metoda `statement` permite executarea interogărilor SQL arbitrare, cum ar fi ștergerea tabelelor sau modificarea schemei bazei de date:

```php
DB::statement('drop table posts');
```

> [!NOTE]
> Această metodă este utilă pentru executarea interogărilor care modifică direct structura bazei de date, cum ar fi crearea sau ștergerea tabelelor, indexurilor etc.

## Utilizarea tranzacțiilor

Laravel suportă tranzacțiile, care permit gruparea mai multor interogări într-o singură operațiune logică. Dacă una dintre interogări eșuează, toate modificările vor fi anulate.

Exemplu de utilizare a unei tranzacții:

```php
DB::transaction(function () {
    DB::update('update posts set title = "New Title" where id = 1');
    DB::delete('delete from comments where post_id = 1');
});
```

Tranzacțiile ajută la păstrarea integrității datelor în cazul apariției unor erori.

## Concluzie

Lucrul cu baza de date în Laravel devine mult mai simplu datorită instrumentelor puternice integrate în framework.

Cu ajutorul metodelor din clasa `DB`, puteți executa ușor diverse interogări SQL, precum `select`, `insert`, `update` și `delete`, și puteți gestiona datele în siguranță utilizând parametrii denumiți, reducând astfel riscul injecțiilor SQL.

Posibilitatea de a executa interogări SQL arbitrare prin metoda `statement` permite o gestionare flexibilă a structurii bazei de date.

În plus, suportul pentru tranzacții ajută la garantarea integrității datelor atunci când se efectuează mai multe interogări. Toate aceste funcționalități fac lucrul cu baza de date în Laravel atât convenabil, cât și sigur.

[^1]: Database: Getting Started, laravel.com [online]. URL: https://laravel.com/docs/database