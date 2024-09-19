### Lucrare de laborator nr. 2. Cereri HTTP și template-uri în Laravel

## Scopul lucrării

Studierea principiilor de bază ale lucrului cu cererile HTTP în Laravel și utilizarea template-urilor Blade pe baza aplicației web `To-Do App pentru echipe`, o aplicație pentru gestionarea sarcinilor echipei.

Aplicația trebuie să fie destinată unei echipe care dorește să gestioneze sarcinile, să le aloce membrilor echipei, să urmărească statusul și prioritățile acestora (similar cu Github Issues).

## Cerințe

### Nr. 1. Pregătirea pentru lucru, instalarea Laravel

1. Deschideți terminalul și creați un nou proiect Laravel cu numele `todo-app` (numele proiectului poate fi oricare) folosind Composer:
   ```bash
   composer create-project --prefer-dist laravel/laravel todo-app
   ```
2. Accesați directorul proiectului:
   ```bash
   cd todo-app
   ```
3. Porniți serverul încorporat Laravel:
   ```bash
   php artisan serve
   ```
   Ce vedeți în browser când deschideți pagina `http://localhost:8000`?

### Nr. 2. Configurarea mediului

1. Deschideți fișierul `.env` și setați următoarele configurații pentru aplicație:
   ```ini
   APP_NAME=
   APP_ENV=
   APP_KEY=
   APP_DEBUG=true
   APP_URL=http://localhost:8000
   ```
2. Generați cheia aplicației, care va fi folosită pentru criptarea datelor:
   ```bash
   php artisan key:generate
   ```
   > **Ce se va întâmpla dacă această cheie ajunge pe mâna unor persoane rău intenționate?**

### Nr. 3. Bazele lucrului cu cererile HTTP

1. Creați o clasă controller `HomeController` pentru a gestiona cererile pentru pagina principală și adăugați metoda `index`.
2. Creați ruta `/` pentru pagina principală în fișierul `routes/web.php`.
3. Creați ruta `/about` pentru pagina „Despre noi” în fișierul `routes/web.php`.
4. Creați o clasă controller `TaskController` pentru a gestiona cererile legate de sarcini, cu următoarele metode:
   - `index` — afișarea listei de sarcini;
   - `create` — afișarea formularului de creare a unei sarcini;
   - `store` — salvarea unei noi sarcini;
   - `show` — afișarea unei sarcini;
   - `edit` — afișarea formularului de editare a unei sarcini;
   - `update` — actualizarea unei sarcini;
   - `destroy` — ștergerea unei sarcini.
    
     _Deocamdată lăsați aceste metode goale, în curând veți renderiza view-uri și veți lucra cu date._  

     Exemplu de metodă `index`:
   ```php
   public function index()
   {
       return 'This is Index';
   }
   ```
5. Creați rutele pentru metodele controller-ului `TaskController` în fișierul `routes/web.php` și indicați metodele corecte ale controller-ului.
6. Folosiți gruparea rutelor pentru controller-ul `TaskController` cu prefixul `/tasks` pentru a simplifica rutarea și pentru a îmbunătăți lizibilitatea codului.
7. Definiți numele corecte ale rutelor pentru controller-ul `TaskController`, de exemplu:
   1. `tasks.index` — lista sarcinilor;
   2. `tasks.create` — formularul de creare a sarcinii;
   3. ...
8. În loc să creați manual rute pentru fiecare metodă, în Laravel puteți folosi un controller de resurse, care va crea automat rutele pentru toate **operațiunile CRUD**.
   1. În fișierul `routes/web.php`, înlocuiți crearea manuală a rutelor pentru controller-ul `TaskController` cu un controller de resurse:
      ```php
      Route::resource('tasks', TaskController::class);
      ```

### Nr. 4. Templating cu Blade

#### Nr. 4.1. Crearea layout-ului paginii

1. Creați layout-ul paginilor principale `layouts/app.blade.php` cu elementele comune ale paginii:
   1. Titlul paginii;
   2. Meniul de navigare;
   3. Conținutul paginii.

#### Nr. 4.2. Utilizarea template-urilor Blade

1. Creați view-ul pentru pagina principală `home.blade.php` folosind layout-ul `layouts/app.blade.php` în directorul `resources/views`. Pe pagina principală trebuie să existe:
   1. Un mesaj de bun venit: titlul și o scurtă descriere a aplicației, de exemplu "To-Do App pentru echipe".
   2. **Navigare**: linkuri către secțiunile principale, cum ar fi:
      1. Lista sarcinilor;
      2. Crearea unei sarcini.
   3. Informații despre aplicație: o scurtă descriere a scopului aplicației și a principalelor sale funcții.
2. Creați view-ul pentru pagina „Despre noi” `about.blade.php` folosind layout-ul `layouts/app.blade.php` în directorul `resources/views`.
3. Creați view-uri pentru sarcini cu următoarele template-uri în directorul `resources/views/tasks`:
   1. `index.blade.php` — lista sarcinilor;
   2. `create.blade.php` — formularul de creare a unei sarcini;
   3. `show.blade.php` — afișarea unei sarcini;
   4. `edit.blade.php` — formularul de editare a unei sarcini.

Deoarece încă nu lucrăm cu baza de date și modele, folosiți date statice transmise din controller către componentă pentru a afișa informații despre sarcini. **Nu este necesară implementarea logicii**.

De exemplu,

```php
public function show($id)
{
    $task = [
        'id' => $id,
        'title' => 'Task Title',
        // ...
    ];

    return view('tasks.show', [
         'task' => $task
    ]);
}
```

#### Nr. 4.3. Componente anonime Blade

> [!TIP]  
> În `Laravel` există posibilitatea de a crea `componente anonime` pentru a simplifica structura template-urilor și reutilizarea blocurilor de cod.

1. Creați o componentă anonimă pentru afișarea `header`. Utilizați componenta creată în layout-ul `layouts/app.blade.php`.
2. Creați o componentă anonimă pentru afișarea unei sarcini.
   1. Componenta trebuie să fie simplă și să folosească parametrii transmiși prin directiva `@props`. Acest lucru va face template-urile mai flexibile și reutilizabile pe diferite pagini.
   2. Componenta trebuie să afișeze informații despre sarcină, cum ar fi:
      1. Titlul sarcinii;
      2. Descrierea sarcinii;
      3. Data creării sarcinii;
      4. Data actualizării sarcinii;
      5. Acțiuni pentru sarcină (editare, ștergere);
      6. Statusul sarcinii (completată/necompletată);
      7. Prioritatea sarcinii (mică/medie/mare);
      8. Executorul sarcinii (numele utilizatorului atribuit sarcinii).

### Nr. 5. Sarcini suplimentare

> [!IMPORTANT]  
> Această sarcină este suplimentară și nu este obligatorie, dar poate fi utilizată pentru o înțelegere mai aprofundată a subiectului.

1. Afișați ultima sarcină pe pagina principală folosind `View Composer`.
2. Adăugați o componentă anonimă suplimentară pentru afișarea priorității sarcinii (`mică`, `medie`, `mare`) cu culori sau pictograme diferite pentru fiecare prioritate.

## Întrebări de control

1. Ce este un controller de resurse în Laravel și ce rute creează?
2. Explicați diferența dintre crearea manuală a rutelor și utilizarea unui controller de resurse. Ce nume de rute vor fi create automat?
3. Ce avantaje oferă utilizarea componentelor anonime Blade?
4. Ce metode HTTP sunt utilizate pentru efectuarea operațiunilor **CRUD**?