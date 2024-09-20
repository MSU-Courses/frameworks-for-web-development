# Lucrare de laborator nr. 2. Cereri HTTP și șablonizare în Laravel

## Scopul lucrării

Să se studieze principiile de bază ale lucrului cu cererile HTTP în Laravel și șablonizarea folosind Blade, pe baza unei aplicații web „To-Do App pentru echipe” — o aplicație pentru gestionarea sarcinilor în cadrul unei echipe.

Aplicația este destinată unei echipe care dorește să își gestioneze sarcinile, să le atribuie membrilor și să monitorizeze starea și prioritatea sarcinilor (similar cu Github Issues).

## Condiții

### Nr. 1. Pregătirea pentru lucru, instalarea Laravel

1. Deschideți terminalul și creați un nou proiect Laravel cu numele `todo-app` (numele proiectului poate fi orice) folosind Composer:
   ```bash
   composer create-project laravel/laravel:^10 todo-app
   ```
2. Intrați în directorul proiectului:
   ```bash
   cd todo-app
   ```
3. Porniți serverul încorporat Laravel:
   ```bash
   php artisan serve
   ```
   **Întrebare**: Ce vedeți în browser când deschideți pagina `http://localhost:8000`?

### Nr. 2. Configurarea mediului

1. Deschideți fișierul `.env` și setați următoarele configurări ale aplicației:
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
   **Întrebare**: Ce s-ar întâmpla dacă această cheie ar ajunge pe mâna unui răufăcător?

### Nr. 3. Principiile de bază ale lucrului cu cererile HTTP

#### Nr. 3.1. Crearea rutelor pentru pagina principală și pagina "Despre noi"

1. Creați un controller `HomeController` pentru gestionarea cererilor către pagina principală.
2. Adăugați metoda `index` în `HomeController`, care va afișa pagina principală.
3. Creați ruta pentru pagina principală în fișierul `routes/web.php`.
   ```php
   public function index()
   {
      return view('home');
   }
   ```
   - Deschideți browserul și accesați adresa `http://localhost:8000`. Asigurați-vă că pagina goală se încarcă, deoarece vizualizarea `home.blade.php` nu a fost încă creată.
4. În același controller `HomeController`, creați o metodă pentru pagina **"Despre noi"**.
5. Adăugați ruta pentru pagina "Despre noi" în fișierul `routes/web.php`.

#### Nr. 3.2. Crearea rutelor pentru sarcini

1. Creați un controller `TaskController` pentru gestionarea cererilor legate de sarcini și adăugați următoarele metode:
   - `index` — afișarea listei de sarcini;
   - `create` — afișarea formularului pentru crearea unei sarcini;
   - `store` — salvarea unei sarcini noi;
   - `show` — afișarea unei sarcini;
   - `edit` — afișarea formularului pentru editarea unei sarcini;
   - `update` — actualizarea sarcinii;
   - `destroy` — ștergerea sarcinii.

   **Notă**: _Deoarece încă nu am studiat lucrul cu formulare și cereri POST și PUT, metodele `store`, `create`, `edit`, `update`, `destroy` trebuie lăsate goale. Ne vom întoarce la ele mai târziu în curs. În acest moment, ne concentrăm pe afișarea informațiilor._

   Exemplu de metodă `index`:
   ```php
   public function index()
   {
       return 'Aceasta este o listă de sarcini';
   }
   ```

2. Creați rutele pentru metodele controllerului `TaskController` în fișierul `routes/web.php` și specificați metodele HTTP corecte pentru fiecare rută.
3. Utilizați gruparea rutelor pentru controllerul `TaskController` cu prefixul `/tasks` pentru a simplifica rutarea și a îmbunătăți lizibilitatea codului.
4. Definiți nume corecte pentru rutele controllerului `TaskController`, de exemplu:
   - `tasks.index` — lista de sarcini;
   - `tasks.show` — afișarea unei sarcini individuale.
   - ...
5. Adăugați validarea parametrilor rutei `id` pentru sarcini. Asigurați-vă că parametrul `id` este un număr întreg pozitiv. Utilizați metoda `where` pentru a limita valorile parametrului `id`.
6. În loc să creați manual rute pentru fiecare metodă, puteți folosi **un controller de resurse**, care va crea automat rute pentru toate operațiunile **CRUD**:
   - În fișierul `routes/web.php`, înlocuiți crearea manuală a rutelor pentru controllerul `TaskController` cu un controller de resurse:
      ```php
      Route::resource('tasks', TaskController::class);
      ```
   - **Întrebare**: Explicați diferența între crearea manuală a rutelor și utilizarea unui controller de resurse. Ce rute și ce nume de rute vor fi create automat?
7. Verificați rutele create cu ajutorul comenzii `php artisan route:list`.

### Nr. 4. Șablonizarea folosind Blade

#### Nr. 4.1. Crearea unui layout pentru pagini

1. Creați un layout pentru paginile principale `layouts/app.blade.php` cu următoarele elemente comune ale paginii:
   1. Titlul paginii;
   2. Meniu de navigare;
   3. Conținutul paginii.
2. Folosiți directiva `@yield` pentru a defini zona în care va fi inserat conținutul diferitelor pagini.

#### Nr. 4.2. Utilizarea șabloanelor Blade

1. Creați vizualizarea pentru pagina principală `home.blade.php` folosind layoutul `layouts/app.blade.php` în directorul `resources/views`.
2. Pe pagina principală trebuie să fie:
   1. **Mesaj de bun venit**: titlu și o scurtă descriere a aplicației, de exemplu „To-Do App pentru echipe”.
   2. **Navigație**: linkuri către secțiunile principale, cum ar fi:
      - Lista de sarcini;
      - Crearea unei sarcini.
   3. **Informații despre aplicație**: o scurtă descriere a scopului aplicației și a principalelor sale funcții.
3. Creați vizualizarea pentru pagina "Despre noi" — `about.blade.php` folosind layoutul `layouts/app.blade.php` în directorul `resources/views`.
4. Creați vizualizări pentru sarcini cu următoarele șabloane în directorul `resources/views/tasks`:
   - `index.blade.php` — lista de sarcini;
   - `show.blade.php` — afișarea unei sarcini;
   - ...
5. Randați lista de sarcini pe pagina `index.blade.php` folosind date statice transmise din controller cu ajutorul directivei `@foreach`.

**Notă**: Deoarece încă nu lucrăm cu baza de date și modele, utilizați date statice, transmise din controller în șablon, pentru a afișa informații despre sarcini. Logica de prelucrare a datelor nu este necesară în acest moment.

Exemplu de metodă pentru afișarea unei sarcini:
```php
public function show($id)
{
    $task = [
        'id' => $id,
        'title' => 'Titlul sarcinii',
        // ...
    ];

    return view('tasks.show', ['task' => $task]);
}
```

#### Nr. 4.3. Componente anonime Blade

> [!TIP]
> În Laravel, se pot crea componente anonime pentru a simplifica structura șabloanelor și reutilizarea blocurilor de cod.

1. Creați o componentă anonimă pentru afișarea antetului (`header`). Folosiți componenta creată în layoutul `layouts/app.blade.php`.
2. Creați o componentă anonimă pentru afișarea sarcinilor:
   1. Componenta trebuie să fie simplă și să folosească parametri transmiși prin directiva `@props`. Acest lucru va face șabloanele mai flexibile și reutilizabile pe diverse pagini.
   2. Componenta trebuie să afișeze informații despre sarcină:
      1. Titlul sarcinii;
      2. Descrierea sarcinii;
      3. Data creării sarcinii;
      4. Data actualizării sarcinii;
      5. Acțiuni asupra sarcinii (editare, ștergere);
      6. Starea sarcinii (finalizată/nu este finalizată);
      7. Prioritatea sarcinii (scăzută/medie/ridicată);
      8. Responsabilul sarcinii (Assignment), adică numele utilizatorului căruia i-a fost atribuită sarcina.
3. Afișați componenta de sarcină creată pe pagina `show.blade.php` folosind parametrii transmiși.

#### Nr. 4.4. Stilizarea paginilor

1. Adăugați stiluri pentru pagini folosind CSS sau preprocesatoare (de exemplu, Sass sau Less).
2. Creați un fișier de stiluri `app.css` în directorul `public/css` și includeți-l în layoutul `layouts/app.blade.php`.
3. Adăugați stiluri pentru elementele paginii, cum ar fi titluri, meniuri de navigare, butoane, formulare etc.
4. Opțional, puteți folosi biblioteci de stiluri, cum ar fi `Bootstrap` sau `Tailwind CSS`.

### Nr. 5. Sarcini suplimentare

> [!IMPORTANT]
> Această sarcină nu este obligatorie, dar va aprofunda înțelegerea materialului.

1. Afișați ultima sarcină creată pe pagina principală folosind **View Composer**.
2. Creați o componentă anonimă suplimentară pentru afișarea priorității sarcinii (scăzută, medie, ridicată) cu culori sau pictograme diferite pentru fiecare prioritate.

## Întrebări de control

1. Ce este un controller de resurse în Laravel și ce rute creează?
2. Explicați diferența între crearea manuală a rutelor și utilizarea unui controller de resurse.
3. Ce avantaje oferă utilizarea componentelor anonime Blade?
4. Ce metode de cereri HTTP sunt folosite pentru a executa operațiunile **CRUD**?