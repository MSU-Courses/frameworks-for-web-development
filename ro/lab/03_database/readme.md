# Lucrarea de laborator nr. 3. Bazele lucrului cu baze de date în Laravel

## Scopul lucrării

Familiarizarea cu principiile de bază ale lucrului cu baze de date în Laravel. Învățarea creării de migrații, modele și seed-uri pe baza aplicației web `To-Do App`.

## Condiția

În cadrul acestei lucrări de laborator, veți continua dezvoltarea aplicației `To-Do App` pentru echipe, începută în lucrările de laborator anterioare.

Veți adăuga funcționalitatea de lucru cu baza de date, veți crea modele și migrații, veți configura relațiile dintre modele și veți învăța să utilizați fabrici și seed-uri pentru generarea datelor de testare.

### Pregătirea pentru lucru

> [!TIP]
> Tematici: S3

1. Instalați SGBD-ul MySQL, PostgreSQL sau SQLite pe calculatorul dumneavoastră.
2. Crearea bazei de date: Creați o nouă bază de date pentru aplicația dvs. **todo_app**.
3. Configurați variabilele de mediu în fișierul `.env` pentru a vă conecta la baza de date:
   ```env
   DB_CONNECTION=baza_dvs_de_date (mysql, pgsql, sqlite)
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=todo_app
   DB_USERNAME=utilizator_dvs
   DB_PASSWORD=parola_dvs
   ```

### Crearea modelelor și migrațiilor

> [!TIP]
> Tematici: S6, S7

1. Creați modelul `Category` — categoria unei sarcini.
   - `php artisan make:model Category -m`
2. Definirea structurii tabelei **category** în migrație:
   - Adăugați câmpuri:
     - `id` — cheia primară;
     - `name` — numele categoriei;
     - `description` — descrierea categoriei;
     - `created_at` — data creării categoriei;
     - `updated_at` — data actualizării categoriei.
3. Creați modelul `Task` — sarcina.
4. Definirea structurii tabelei **task** în migrație:
   - Adăugați câmpuri:
     - `id` — cheia primară;
     - `title` — titlul sarcinii;
     - `description` — descrierea sarcinii;
     - `created_at` — data creării sarcinii;
     - `updated_at` — data actualizării sarcinii.
5. Rulați migrarea pentru a crea tabelele în baza de date:
   ```bash
   php artisan migrate
   ```
6. Creați modelul `Tag` — eticheta unei sarcini.
7. Definirea structurii tabelei **tag** în migrație:
   - Adăugați câmpuri:
     - `id` — cheia primară;
     - `name` — numele etichetei;
     - `created_at` — data creării etichetei;
     - `updated_at` — data actualizării etichetei.
8. Adăugați câmpul `$fillable` în modelele `Task`, `Category` și `Tag` pentru a permite atribuirea în masă a datelor.

### Relația dintre tabele

> [!TIP]
> Tematici: S8

1. Creați o migrare pentru a adăuga câmpul `category_id` în tabela **task**.
   - `php artisan make:migration add_category_id_to_tasks_table --table=tasks`
   - Definiți structura câmpului `category_id` și adăugați cheia externă pentru a face legătura cu tabela **category**.
2. Creați o tabelă intermediară pentru relația de tipul multe-la-multe dintre sarcini și etichete:
   - `php artisan make:migration create_task_tag_table`
3. Definirea structurii corespunzătoare a tabelei în migrație.
   - Această tabelă trebuie să lege sarcinile și etichetele prin identificatorii lor.
   - **Exemplu**: `task_id` și `tag_id`: sarcina `10` este legată de eticheta `5`.
4. Rulați migrarea pentru a crea tabela în baza de date.

### Relațiile dintre modele

> [!TIP]
> Tematici: S8

1. Adăugați relații în modelul `Category` (O categorie poate avea multe sarcini)
   - Deschideți modelul `Category` și adăugați metoda:
     ```php
     public function tasks()
     {
         return $this->hasMany(Task::class);
     }
     ```
2. Adăugați relații în modelul `Task`
   - Sarcina este legată de o categorie.
   - Sarcina poate avea multe etichete.
3. Adăugați relații în modelul `Tag` (O etichetă poate fi legată de multe sarcini).
4. Adăugați câmpurile corespunzătoare în `$fillable` ale modelelor.

### Crearea fabricilor și seed-urilor

> [!TIP]
> Tematici: S7, S8

1. Creați o fabrică pentru modelul `Category`:
   - `php artisan make:factory CategoryFactory --model=Category`
   - Definiți structura datelor pentru generarea categoriilor.
2. Creați o fabrică pentru modelul `Task`.
3. Creați o fabrică pentru modelul `Tag`.
4. Creați seed-uri pentru a popula tabelele cu date inițiale pentru modelele `Category`, `Task`, `Tag`.
5. Actualizați fișierul `DatabaseSeeder` pentru a lansa seed-urile și rulați-le:
   ```bash
   php artisan db:seed
   ```

### Lucrul cu controlere și vizualizări

> [!TIP]
> Tematici: S4, S5, S7, S8

1. Deschideți controlerul `TaskController` (`app/Http/Controllers/TaskController.php`).
2. Actualizați metoda `index` pentru a obține lista sarcinilor din baza de date. 
   - Folosiți modelul `Task` pentru a obține toate sarcinile.
3. Actualizați metoda `show` pentru a afișa o sarcină individuală. 
   - Afișați informațiile despre sarcină după identificatorul acesteia.
   - **Obligatoriu** afișați categoria și etichetele sarcinii.
4. În metodele `index` și `show`, folosiți metoda `with` (**Eager Loading**) pentru a încărca modelele asociate.
5. Actualizați vizualizările corespunzătoare pentru a afișa lista de sarcini și o sarcină individuală.
6. Actualizați metoda `create` pentru a afișa formularul de creare a unei sarcini și metoda `store` pentru a salva o sarcină nouă în baza de date.
   - **Notă**: Deoarece nu ați studiat încă formularele, folosiți obiectul `Request` pentru a obține datele. **De exemplu**:
     ```php
     $request->input('title');
     // sau
     $request->all();
     ```
7. Actualizați metoda `edit` pentru a afișa formularul de editare a unei sarcini și metoda `update` pentru a salva modificările în baza de date.
8. Actualizați metoda `destroy` pentru a șterge o sarcină din baza de date.

## Sarcini suplimentare

> [!NOTE]
> **Important**: Aceste sarcini nu sunt obligatorii, dar vor ajuta la aprofundarea materialului.

1. Creați modelul `Comment` pentru comentariile sarcinilor.
   - Adăugați câmpurile corespunzătoare în migrare.
   - Creați relații între modelele Task și Comment.
2. Adăugați posibilitatea de a adăuga comentarii la sarcini.
   - Actualizați vizualizarea pentru a afișa comentariile unei sarcini și pentru a vizualiza lista de comentarii și comentariul după `id`: `/task/{id}/comment`, `/task/{id}/comment/{comment_id}`.
3. Adăugați posibilitatea de a adăuga etichete la sarcini și folosiți tranzacții pentru a salva relațiile dintre sarcini și etichete.
   ```php
   DB::transaction(function () use ($request) {
        // Crearea sarcinii
        // Legarea etichetelor la sarcină
    });
    ```

## Întrebări de control

1. Ce sunt migrațiile și la ce se folosesc?
2. Ce sunt fabricile și seed-urile și cum simplifică procesul de dezvoltare și testare?
3. Ce este ORM? Care sunt diferențele dintre pattern-urile `DataMapper` și `ActiveRecord`?
4. Care

 sunt avantajele utilizării unui ORM comparativ cu interogările SQL directe?
5. Ce sunt tranzacțiile și de ce sunt importante în lucrul cu bazele de date?