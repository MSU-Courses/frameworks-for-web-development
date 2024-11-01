# Fabrici și Seeder-e în Laravel

Când începem dezvoltarea și testarea unei aplicații, deseori apare nevoia de a crea date de test pentru a verifica funcționalitatea acesteia. Încărcarea manuală a bazei de date nu este întotdeauna convenabilă și eficientă, motiv pentru care Laravel oferă două instrumente utile pentru automatizarea acestui proces: fabricile și seeder-ele.

În mod obișnuit, aceste instrumente nu sunt utilizate în producție, dar sunt extrem de utile în etapele de **dezvoltare** și **testare**.

## Fabricile

**Fabricile** sunt clase care creează automat date de test pentru modele.

Putem face o analogie: o fabrică în lumea reală produce produse, iar în Laravel o fabrică creează date fictive pentru modele.

De exemplu, o fabrică pentru postări va genera date de test pentru modelul Post.

### Crearea unei fabrici

Pentru a crea o fabrică, se folosește comanda `make:factory`:

```bash
php artisan make:factory PostFactory
```

O fabrică poate fi creată împreună cu modelul (*este mai convenabil dacă modelul încă nu există*):

```bash
# -f va crea simultan fabrica și modelul
php artisan make:model Post -f
```

### Definirea fabricii

După crearea automată a fabricii, în fișierul `database/factories/PostFactory.php` va apărea următorul cod:

```php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

/**
 * @extends \Illuminate\Database\Eloquent\Factories\Factory<\App\Models\Post>
 */
class PostFactory extends Factory
{
    /**
     * Definește starea implicită a modelului.
     *
     * @return array<string, mixed>
     */
    public function definition(): array
    {
        return [
            //
        ];
    }
}
```

Acum este necesar să definim datele pe care fabrica le va crea. Pentru aceasta, în metoda `definition` se returnează un array cu datele:

```php
public function definition(): array
{
    return [
        'title' => $this->faker->sentence(),
        'content' => $this->faker->paragraph(),
        'thumbnail' => $this->faker->imageUrl(),
    ];
}
```

După cum se poate observa, pentru generarea datelor se folosește proprietatea `$this->faker`. Aceasta oferă acces la generatorul de date fictive.

### Faker

**Faker** este o bibliotecă pentru generarea de date fictive. Ea permite crearea unei varietăți de tipuri de date: șiruri de caractere, numere, date, adrese și multe altele.

Exemple de utilizare:

```php
$this->faker->sentence(); // Generare propoziție aleatorie
$this->faker->paragraph(); // Generare paragraf de text
$this->faker->imageUrl(); // Generare URL imagine
$this->faker->name(); // Generare nume
$this->faker->text(200); // Generare text de 200 de caractere
$this->faker->numberBetween(1, 100); // Generare număr între 1 și 100
$this->faker->dateTime(); // Generare dată aleatorie
```

Fabricile simplifică crearea unor volume mari de date, accelerând procesul de testare a aplicației.

### Utilizarea fabricii

Pentru a crea una sau mai multe modele folosind fabrica, se folosește metoda `create`:

```php
// Crearea a 10 postări
$posts = Post::factory(10)->create();
```

De asemenea, fabrica poate fi utilizată direct, fără apel la model:

```php
// Crearea unei postări
$post = PostFactory::new()->create();

// Crearea mai multor postări
$posts = PostFactory::new()->count(10)->create();
```

Acest mod permite gestionarea flexibilă a procesului de generare a datelor, creând numărul necesar de instanțe ale modelului pentru testare.

## Seeder-ele

**Seeder-ele** sunt clase destinate pentru a popula baza de date cu date de test.

### Diferența între fabrici și seeder-e

Fabricile generează date de test pentru modele, în timp ce seeder-ele populează baza de date cu aceste date. Fabricile creează date, iar seeder-ele le folosesc pentru a umple baza de date în mod masiv.

### Utilizarea seeder-elor

Atunci când proiectul are puține modele, seeder-ul `DatabaseSeeder.php`, deja creat, poate fi utilizat pentru a apela fabricile și a popula baza de date. Acest fișier se află în directorul `database/seeders` și conține metoda `run`, unde se pot apela fabricile pentru a crea date de test.

Exemplu de utilizare a seeder-ului:

```php
<?php

namespace Database\Seeders;

use App\Models\Post;
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Popularea bazei de date.
     */
    public function run(): void
    {
        // Exemplu de utilizare a fabricii pentru a crea 10 postări
        Post::factory(10)->create();
    }
}
```

Acest cod va adăuga 10 postări în baza de date folosind fabrica pentru modelul Post. Astfel, seeder-ele asigură un mod simplu de umplere masivă a bazei de date pentru testare sau configurarea inițială a proiectului.