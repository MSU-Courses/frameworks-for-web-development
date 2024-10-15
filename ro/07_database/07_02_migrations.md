# Migrațiile în Laravel [^1]

Unul dintre cele mai puternice instrumente pentru lucrul cu baza de date sunt migrațiile.

## Ce sunt migrațiile?

**Migrațiile** sunt un instrument care permite gestionarea structurii bazei de date folosind cod, similar cu modul în care ați utiliza un sistem de control al versiunilor pentru baza de date [^2].

Migrațiile oferă posibilitatea de a aduce modificări în structura bazei de date (de exemplu, adăugarea, modificarea sau ștergerea de tabele și coloane), păstrând în același timp datele. Acest lucru este deosebit de util în lucrul în echipă: în loc să comunicați manual colegilor ce modificări trebuie aduse bazei de date, pur și simplu creați o migrație, iar fiecare dezvoltator poate aplica automat acea modificare.

## Crearea unei migrații

Să încercăm să creăm o migrație. Pentru aceasta, folosim comanda `make:migration` și indicăm numele migrației.

> [!NOTE] 
> **Numele migrației** trebuie să reflecte schimbările pe care le aduceți bazei de date. De exemplu, dacă adăugați tabela `users`, numele migrației poate fi `create_users_table`, iar dacă adăugați coloana `email` în tabela `users`, numele migrației poate fi `add_email_to_users_table`.

Să creăm o migrație pentru adăugarea tabelei `posts`.

```bash
php artisan make:migration create_posts_table
```

Laravel va încerca să ghicească tipul migrației pe baza numelui. În acest caz, Laravel presupune că este vorba despre o migrație pentru crearea unei tabele.

Migrația va fi creată în directorul `database/migrations`. Deschideți fișierul migrației și examinați conținutul acestuia.

## Structura unei migrații

O migrație în Laravel conține două metode: `up` și `down`.

- Metoda `up` este folosită pentru a descrie modificările care trebuie realizate la aplicarea migrației.
- Metoda `down` conține codul pentru anularea acestor modificări, în cazul în care este necesară revenirea la starea anterioară.

**Exemplu de migrație**:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

Așa cum se poate observa din exemplu, în loc să scriem interogări SQL pentru crearea unei tabele, folosim metodele din clasa `Schema`. Acest lucru simplifică codul și îl face mai ușor de citit.

În plus, migrațiile permit abstractizarea de la o anumită bază de date. Aceasta înseamnă că puteți trece ușor de la o bază de date la alta (de exemplu, să folosiți SQLite în faza de dezvoltare și MySQL în producție) fără a modifica codul migrației — trebuie doar să schimbați setările de conectare în fișierul `.env`.

## Aplicarea migrațiilor

Pentru gestionarea migrațiilor în Laravel, se folosesc următoarele comenzi:

1. Executarea tuturor migrațiilor care nu au fost încă aplicate:

   ```bash
   php artisan migrate
   ```

2. Anularea ultimei migrații:

   ```bash
   php artisan migrate:rollback
   ```

3. Anularea tuturor migrațiilor:

   ```bash
   php artisan migrate:reset
   ```

4. Anularea și reaplicarea tuturor migrațiilor:

   ```bash
   php artisan migrate:refresh

   // echivalent cu
   php artisan migrate:reset
   php artisan migrate
   ```

5. Ștergerea tuturor tabelelor și reaplicarea tuturor migrațiilor:

   ```bash
   php artisan migrate:fresh
   ```

6. Anularea migrațiilor pentru un anumit număr de pași:

   ```bash
   php artisan migrate:rollback --step=3
   ```

> [!NOTE]
> Acesta nu este un set complet de comenzi pentru lucrul cu migrațiile. Pentru mai multe informații, consultați [documentația oficială Laravel](https://laravel.com/docs/migrations).

## Tabele

### Crearea tabelelor

Așa cum am văzut în exemplul de mai sus, crearea tabelelor în Laravel cu ajutorul migrațiilor este un proces simplu și convenabil.

Pentru crearea unei noi tabele, se folosește metoda `Schema::create()`. Primul argument este **numele tabelei**, iar al doilea este o **funcție** în care se descriu **coloanele tabelei**.

```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->text('content');
    $table->timestamps();
});
```

### Modificarea tabelelor

Pentru a modifica o tabelă existentă, se folosește metoda `Schema::table()`. De exemplu, pentru a adăuga o nouă coloană într-o tabelă, folosiți această metodă în loc de `Schema::create()`.

```php
Schema::table('posts', function (Blueprint $table) {
    $table->string('slug');
});
```

În acest caz, tabela `posts` există deja, iar migrația adaugă o nouă coloană `slug`. Astfel, veți avea două migrații: una pentru crearea tabelei `posts` și alta pentru adăugarea coloanei `slug`.

### Ștergerea tabelelor

Pentru a șterge o tabelă, se folosește metoda `Schema::dropIfExists()`. De obicei, aceasta este folosită în metoda `down` a migrației.

```php
// Ștergerea tabelei
Schema::drop('posts');

// Ștergerea tabelei dacă există
// O metodă mai sigură
Schema::dropIfExists('posts');
```

### Redenumirea tabelelor

Pentru redenumirea unei tabele în Laravel, se folosește metoda `Schema::rename()`.

```php
Schema::rename('old_table', 'new_table');
```

## Coloane

După ce am creat migrația, trebuie să definim structura tabelei. Am văzut deja exemple de creare a coloanelor, dar să analizăm mai detaliat ce tipuri de coloane pot fi utilizate.

### Tipuri de coloane

În Laravel, există o varietate de tipuri de coloane pe care le puteți folosi la crearea unei tabele. Lista completă a tipurilor de coloane poate fi găsită în [documentația oficială Laravel](https://laravel.com/docs/migrations#columns).

Să analizăm cele mai comune tipuri de coloane.

#### Tipul `id`

Tipul `id` este folosit pentru crearea unei chei primare în tabelă. În mod implicit, Laravel folosește tipul `bigIncrements`, care creează o coloană `id` de tip `BIGINT` cu auto-increment.

```php
$table->id();
```

#### Tipul `string`

Tipul `string` este folosit pentru crearea unei coloane de tip `VARCHAR`.

```php
$table->string('name');
// specificarea lungimii
$table->string('name', 100);
```

#### Tipul `text`

Tipul `text` este folosit pentru crearea unei coloane de tip `TEXT`.

```php
$table->text('description');
```

#### Tipul `integer`

Tipul `integer` este folosit pentru crearea unei coloane de tip `INT`.

```php
$table->integer('age');
```

Există și tipurile `bigInteger`, `mediumInteger`, `smallInteger` și `tinyInteger` pentru crearea coloanelor de alte dimensiuni de numere întregi (`BIGINT`, `MEDIUMINT`, `SMALLINT`, `TINYINT`).

#### Tipul `float`

Tipul `float` este folosit pentru crearea unei coloane de tip `FLOAT`.

```php
$table->float('price');
// specificarea preciziei și a scalei
// (8 cifre în total, 2 cifre după virgulă)
$table->float('price', 8, 2);
```

#### Tipul `date` și `dateTime`

Tipurile `date` și `dateTime` sunt folosite pentru crearea coloanelor de tip `DATE` și `DATETIME`.

```php
$table->date('birth_date');
$table->dateTime('created_at');
```

#### Tipul

 `boolean`

Tipul `boolean` este folosit pentru crearea unei coloane de tip `BOOLEAN`.

```php
$table->boolean('is_active');
```

#### Tipul `timestamps`

Tipul `timestamps` este folosit pentru crearea a două coloane: `created_at` și `updated_at`.

La crearea acestor coloane, Laravel le actualizează automat la momentul creării și actualizării înregistrărilor.

```php
$table->timestamps();
```

O migrație completă poate arăta astfel:

### Modificatori de coloane

În Laravel, pe lângă tipurile de coloane, există **modificatori** care permit configurarea mai detaliată a coloanelor, similar cu SQL.

#### Modificatorul `nullable`

Modificatorul `nullable` indică faptul că o coloană poate avea valoarea `NULL`.

```php
$table->string('email')->nullable();
```

#### Modificatorul `default`

Modificatorul `default` setează o valoare implicită pentru o coloană.

```php
$table->string('role')->default('user');
```

#### Modificatorul `unsigned`

Modificatorul `unsigned` este folosit pentru coloanele de tip întreg și interzice stocarea valorilor negative.

```php
$table->integer('age')->unsigned();
```

#### Modificatorul `unique`

Modificatorul `unique` face ca valorile din coloană să fie unice, prevenind duplicarea acestora.

```php
$table->string('email')->unique();
```

#### Modificatorul `after`

Modificatorul `after` permite setarea poziției unei coloane noi în raport cu coloanele existente. De obicei, este folosit la modificarea unei tabele, deoarece la crearea unei tabele noi, acest modificator nu are sens.

```php
$table->string('name')->after('id');
```

#### Modificatorul `comment`

Modificatorul `comment` permite adăugarea unui comentariu la o coloană.

```php
$table->string('name')->comment('Numele utilizatorului');
```

Modificatorii pot fi combinați pentru a personaliza coloanele în funcție de cerințe.

> [!NOTE]  
> Mai multe informații despre tipurile și modificatorii coloanelor pot fi găsite în [documentația oficială Laravel](https://laravel.com/docs/migrations#column-modifiers).

### Modificarea coloanelor

Pentru modificarea coloanelor existente în Laravel, se folosește metoda `change()`.

```php
// Tabela 'posts' are deja coloana 'title'.
// Dorim să îi schimbăm tipul în 'text'.
// Dacă era nullable, trebuie să specificăm acest lucru explicit.
Schema::table('posts', function (Blueprint $table) {
    $table->text('title')->nullable()->change();
});
```

Când modificați o coloană, parametrii acesteia sunt actualizați. Fiți atenți, mai ales când lucrați cu tabele care conțin deja date.

### Ștergerea coloanelor

Pentru ștergerea unei coloane dintr-o tabelă, se folosește metoda `dropColumn()`.

```php
Schema::table('posts', function (Blueprint $table) {
    $table->dropColumn('title');
});
```

### Redenumirea coloanelor

Pentru redenumirea unei coloane în Laravel, se folosește metoda `renameColumn()`.

```php
Schema::table('posts', function (Blueprint $table) {
    $table->renameColumn('old_title', 'new_title');
});
```

## Exemplu de migrație

Să vedem un exemplu de migrație care creează tabela `posts` cu mai multe coloane.

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title', 200);
            $table->text('content')->nullable();
            $table->string('slug')->unique()->comment('slug-ul postării');
            $table->boolean('published')->default(false);
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

## Concluzie

**Migrațiile** sunt un instrument puternic pentru gestionarea structurii bazei de date în Laravel.

Ele vă permit să creați, să modificați și să ștergeți tabele și coloane, făcând procesul de dezvoltare mai convenabil și sigur.

Utilizați migrațiile pentru a gestiona structura bazei de date în proiectele voastre și asigurați-vă că aveți întotdeauna o versiune actualizată a bazei de date în orice moment.

[^1]: Database: Migrations, laravel.com [online]. URL: https://laravel.com/docs/migrations.

[^2]: What are database migrations?, prisma.io [online]. URL: https://www.prisma.io/dataguide/types/relational/what-are-database-migrations