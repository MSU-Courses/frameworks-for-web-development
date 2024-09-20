# Laboratorul nr. 2. Cereri HTTP și șabloane în Laravel

## Scopul lucrării

Să studiem principiile de bază ale lucrului cu cererile HTTP în Laravel și șablonizarea folosind Blade, bazate pe aplicația web `To-Do App pentru echipe` — o aplicație pentru gestionarea sarcinilor într-o echipă.

Aplicația este destinată unei echipe care dorește să gestioneze sarcinile, să le aloce membrilor, să urmărească starea și prioritățile sarcinilor (similar cu Github Issues).

## Condiții

### Nr. 1. Pregătirea pentru lucru, instalarea Laravel

1. Deschideți terminalul și creați un nou proiect Laravel cu numele `todo-app` (numele proiectului poate fi oricare) folosind Composer:
   ```bash
   composer create-project --prefer-dist laravel/laravel todo-app
   ```
2. Intrați în directorul proiectului:
   ```bash
   cd todo-app
   ```
3. Porniți serverul intern Laravel:
   ```bash
   php artisan serve
   ```
   **Întrebare**: Ce vedeți în browser când deschideți pagina `http://localhost:8000`?

### Nr. 2. Configurarea mediului

1. Deschideți fișierul `.env` și specificați următoarele setări pentru aplicație:
   ```ini
   APP_NAME=ToDoApp
   APP_ENV=local
   APP_KEY=
   APP_DEBUG=true
   APP_URL=http://localhost:8000
   ```
2. Generați cheia aplicației, care va fi utilizată pentru criptarea datelor:
   ```bash
   php artisan key:generate
   ```
   **Întrebare**: Ce se va întâmpla dacă această cheie ajunge pe mâinile unui răufăcător?

### Nr. 3. Baza lucrului cu cereri HTTP

#### Nr. 3.1. Crearea rutelor pentru pagina principală și pagina „Despre noi”

1. Creați clasa controler `HomeController` pentru a gestiona cererile pentru pagina principală.
2. Adăugați metoda `index` în `HomeController`, care va fi responsabilă de afișarea paginii principale.
3. Creați ruta pentru pagina principală în fișierul `routes/web.php`.
   ```php
   public function index()
   {
      return view('home'); // Afișează pagina principală
   }
   ```
   - Deschideți browserul și navigați la adresa `http://localhost:8000`. Asigurați-vă că se încarcă o pagină goală, deoarece vizualizarea home.blade.php nu a fost creată încă.
4. În același controler `HomeController`, creați o metodă pentru pagina **„Despre noi”**.
5. Adăugați ruta pentru pagina „Despre noi” în fișierul `routes/web.php`.

#### Nr. 3.2. Crearea rutelor pentru sarcini

1. Creați clasa controler `TaskController` pentru gestionarea cererilor legate de sarcini și adăugați următoarele metode:
   - `index` — afișarea listei de sarcini;
   - `create` — afișarea formularului de creare a unei sarcini;
   - `store` — salvarea unei noi sarcini;
   - `show` — afișarea unei sarcini;
   - `edit` — afișarea formularului de editare a unei sarcini;
   - `update` — actualizarea sarcinii;
   - `destroy` — ștergerea sarcinii.

   _În acest stadiu, aceste metode pot fi lăsate goale. În curând veți începe să lucrați cu vizualizări, iar în lucrările de laborator ulterioare — cu date._

   Exemplu de metodă `index`:
   ```php
   public function index()
   {
       return 'This is a list of tasks';
   }
   ```

2. Creați rutele pentru metodele din controlerul `TaskController` în fișierul `routes/web.php` și specificați metodele HTTP corecte pentru fiecare rută.
3. Utilizați gruparea rutelor pentru controlerul `TaskController` cu prefixul `/tasks` pentru a simplifica rutarea și a îmbunătăți lizibilitatea codului.
4. Definiți numele rutelor corecte pentru metodele din `TaskController`, de exemplu:
   - `tasks.index` — lista sarcinilor;
   - `tasks.create` — formularul de creare a unei sarcini.
   - ...

5. În loc să creați manual rute pentru fiecare metodă, puteți utiliza un **controler resursă**, care va crea automat rutele pentru toate operațiile **CRUD**:
   - În fișierul `routes/web.php`, înlocuiți crearea manuală a rutelor pentru `TaskController` cu un controler resursă:
      ```php
      Route::resource('tasks', TaskController::class);
      ```
   - **Întrebare**: Explicați diferența dintre crearea manuală a rutelor și utilizarea unui controler resursă. Ce rute și nume de rute vor fi create automat?

### Nr. 4. Șablonizare folosind Blade

#### Nr. 4.1. Crearea unui layout pentru pagini

1. Creați layout-ul paginilor principale `layouts/app.blade.php` cu elementele comune ale paginilor:
   1. Titlul paginii;
   2. Meniul de navigare;
   3. Conținutul paginii.
2. Utilizați directiva `@yield` pentru a defini aria în care va fi inserat conținutul diferitelor pagini.

#### Nr. 4.2. Utilizarea șabloanelor Blade

1. Creați vizualizarea pentru pagina principală `home.blade.php` utilizând layout-ul `layouts/app.blade.php` în directorul `resources/views`.
2. Pe pagina principală trebuie să existe:
   1. **Mesaj de bun venit**: un titlu și o scurtă descriere a aplicației, de exemplu "To-Do App pentru echipe".
   2. **Navigare**: linkuri către secțiunile principale, cum ar fi:
      - Lista sarcinilor;
      - Crearea unei sarcini.
   3. **Informații despre aplicație**: o scurtă descriere a scopului aplicației și a principalelor funcționalități.
3. Creați vizualizarea pentru pagina „Despre noi” — `about.blade.php` utilizând layout-ul `layouts/app.blade.php` în directorul `resources/views`.
4. Creați vizualizările pentru sarcini cu următoarele șabloane în directorul `resources/views/tasks`:
   - `index.blade.php` — lista sarcinilor;
   - `create.blade.php` — formularul de creare a unei sarcini;
   - `show.blade.php` — afișarea unei sarcini;
   - `edit.blade.php` — formularul de editare a unei sarcini.

**Notă**: Deoarece deocamdată nu lucrăm cu baza de date și modele, folosiți date statice, transmise din controler în șablon, pentru a afișa informații despre sarcini. Logica de prelucrare a datelor nu este necesară încă.

Exemplu de metodă pentru afișarea unei sarcini:
```php
public function show($id)
{
    $task = [
        'id' => $id,
        'title' => 'Task Title',
        // ...
    ];

    return view('tasks.show', ['task' => $task]);
}
```

#### Nr. 4.3. Componente anonime Blade

> [!TIP]
> În Laravel, se pot crea componente anonime pentru a simplifica structura șabloanelor și reutilizarea blocurilor de cod.

1. Creați o componentă anonimă pentru afișarea unui `header`. Folosiți componenta creată în layout-ul `layouts/app.blade.php`.
2. Creați o componentă anonimă pentru afișarea unei sarcini:
   1. Componenta trebuie să fie simplă și să folosească parametrii transmiși cu ajutorul directivei `@props`. Acest lucru va face șabloanele mai flexibile și reutilizabile pe diferite pagini.
   2. Componenta trebuie să afișeze informațiile despre sarcină:
      1. Titlul sarcinii;
      2. Descrierea sarcinii;
      3. Data creării sarcinii;
      4. Data actualizării sarcinii;
      5. Acțiuni pentru sarcină (editare, ștergere);
      6. Starea sarcinii (finalizată/nefinalizată);
      7. Prioritatea sarcinii (scăzută/medie/ridicată);
      

 8. Responsabilul sarcinii (Assignment), adică numele utilizatorului căruia i-a fost alocată sarcina.

### Nr. 5. Sarcini suplimentare

> [!IMPORTANT]
> Acest task nu este obligatoriu, dar vă va ajuta să aprofundați materialul.

1. Afișați ultima sarcină creată pe pagina principală folosind **View Composer**.
2. Creați o componentă anonimă suplimentară pentru afișarea priorității unei sarcini (scăzută, medie, ridicată) cu culori sau pictograme diferite pentru fiecare prioritate.

## Întrebări de control

1. Ce este un controler resursă în Laravel și ce rute creează?
2. Explicați diferența dintre crearea manuală a rutelor și utilizarea unui controler resursă.
3. Ce avantaje oferă utilizarea componentelor anonime Blade?
4. Ce metode de cereri HTTP sunt utilizate pentru a efectua operațiile **CRUD**?