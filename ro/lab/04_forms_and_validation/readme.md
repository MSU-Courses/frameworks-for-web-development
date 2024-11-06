# Lucrarea de laborator Nr. 4. Formulare și validarea datelor

## Scopul lucrării

Familiarizarea cu elementele de bază ale creării și gestionării formularelor în **Laravel**.

Deprinderea mecanismelor de validare a datelor pe server, utilizarea regulilor de validare predefinite și personalizate, precum și învățarea gestionării erorilor și asigurarea securității datelor.

## Condiții

În această lucrare de laborator, veți crea formulare HTML, implementați verificarea datelor pe partea de server și asigurați interacțiunea sigură cu utilizatorul, prevenind vulnerabilități precum **XSS** și **CSRF**.

> [!NOTE]
> În această lucrare de laborator puteți continua munca anterioară sau începe un proiect nou.

### №1. Pregătirea lucrării

1. Creați un proiect nou Laravel (dacă nu este deja instalat) sau continuați cu proiectul anterior.
2. Actualizați variabilele de mediu în `.env`, dacă este necesară conectarea la o bază de date.

### №2. Crearea formularului

1. Creați un formular pentru adăugarea unei sarcini noi:
   1. Formularul trebuie să conțină următoarele câmpuri: `Titlu`, `Descriere`, `Data limită`, `Categorie`.
   2. Folosiți șabloanele Blade pentru a reda formularul.
   3. Câmpul `Categorie` trebuie să fie o listă derulantă, încărcată din tabelul de categorii din baza de date.
   4. Asigurați-vă că formularul trimite date prin metoda **POST** către o rută creată pentru procesarea datelor.
2. Creați ruta `POST /tasks` pentru salvarea datelor din formular în baza de date. Pentru ușurință, puteți folosi un controller de tip resursă.
3. Actualizați controllerul `TaskController`:
   1. Adăugați metoda `create`, care returnează vizualizarea cu formularul.
   2. Adăugați metoda `store`, care procesează datele din formular și le salvează.

### №3. Validarea datelor pe partea de server

1. Implementați validarea datelor direct în metoda `store` a controllerului `TaskController`.
2. Cerințele pentru câmpuri:
   - `title` — obligatoriu, șir de caractere, lungime minimă de 3 caractere.
   - `description` — șir de caractere, opțional, lungime maximă de 500 de caractere.
   - `due_date` — obligatoriu, dată, trebuie să fie cel puțin data de astăzi.
   - `category_id` — obligatoriu, trebuie să existe în tabelul categories.
3. Gestionați erorile de validare și returnați-le la formular, afișând mesajele de eroare lângă câmpuri.
4. Verificați funcționarea corectă a validării și asigurați-vă că erorile sunt afișate corect.

### №4. Crearea unei clase de cerere personalizată (Request)

1. În a doua etapă, creați o clasă personalizată de cerere pentru validarea formularului sarcinii:
   - Folosiți comanda `php artisan make:request CreateTaskRequest`
   - În clasa `CreateTaskRequest`, definiți regulile de validare similare celor din controller.
   - Actualizați metoda `store` a controllerului `TaskController` pentru a folosi `CreateTaskRequest` în loc de `Request`.
2. Adăugați logica de validare pentru datele asociate
   - Verificați că valoarea `category_id` există efectiv în baza de date și aparține unei categorii specificate.
3. Asigurați-vă că datele trec prin validarea TaskRequest și că toate erorile sunt gestionate corect și returnate la formular.

### №5. Adăugarea mesajelor flash

1. Actualizați formularul HTML pentru a afișa un mesaj de confirmare la salvarea cu succes a sarcinii (mesaj flash).
2. Actualizați metoda `store` a controllerului `TaskController` pentru a adăuga un mesaj flash la salvarea cu succes a sarcinii.

### №6. Protecția împotriva CSRF

1. Asigurarea securității datelor din formulare:
   - Adăugați directiva `@csrf` în formular pentru protecția împotriva atacurilor **CSRF**.
   - Asigurați-vă că formularul este trimis doar prin metoda **POST**.

### №7. Actualizarea sarcinii

1. Adăugați posibilitatea de editare a sarcinii:
   1. Creați un formular pentru editarea sarcinii.
   2. Creați o nouă clasă Request `UpdateTaskRequest` cu reguli de validare similare.
   3. Creați ruta `GET /tasks/{task}/edit` și metoda `edit` în controllerul `TaskController`.
   4. Creați ruta `PUT /tasks/{task}` pentru actualizarea sarcinii.
   5. Actualizați metoda `update` în controllerul `TaskController` pentru a procesa datele din formular.

### Sarcină suplimentară

1. Creați o regulă personalizată de validare pentru a verifica că `description` nu conține cuvinte interzise.
2. Folosiți comanda Artisan: `php artisan make:rule NoRestrictedWords`.
3. Aplicați această regulă câmpului `description` în clasa `CreateTaskRequest`.

## Întrebări de control

1. Ce este validarea datelor și de ce este necesară?
2. Cum se asigură protecția formularului împotriva atacurilor CSRF în Laravel?
3. Cum se creează și utilizează clasele personalizate de cerere (Request) în Laravel?
4. Cum se protejează datele împotriva atacurilor XSS la afișarea în vizualizare?