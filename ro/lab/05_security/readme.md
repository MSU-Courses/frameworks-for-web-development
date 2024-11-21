# Lucrarea de laborator nr. 5. Componentele de securitate în Laravel

## Scopul lucrării

Familiarizarea cu bazele componentelor de securitate în Laravel, cum ar fi **autentificarea**, **autorizarea**, **protecția împotriva CSRF**, precum și utilizarea mecanismelor integrate pentru gestionarea accesului.

Studierea abordărilor dezvoltării sigure, inclusiv crearea rutelor protejate și gestionarea rolurilor utilizatorilor.

## Condiții

În această lucrare de laborator, veți implementa componentele principale de securitate, precum autentificarea, autorizarea, protecția rutelor și gestionarea de bază a rolurilor. În plus, veți configura mecanismul de resetare a parolei și veți explora logarea acțiunilor utilizatorilor.

> [!NOTE]
> Puteți continua munca anterioară sau începe un proiect nou.

### Nr. 1. Pregătirea pentru lucru

1. Creați un nou proiect Laravel (dacă nu este instalat) sau continuați cu proiectul anterior.
2. Asigurați-vă că variabilele de mediu din fișierul `.env` sunt configurate corect, inclusiv conexiunea la baza de date.

### Nr. 2. Autentificarea utilizatorilor

1. Creați un controller `AuthController` pentru gestionarea autentificării utilizatorilor.
2. Adăugați și implementați metodele pentru înregistrarea, autentificarea și deconectarea utilizatorului:
   - `register()` pentru afișarea formularului de înregistrare.
   - `storeRegister()` pentru procesarea datelor din formularul de înregistrare.
   - `login()` pentru afișarea formularului de autentificare.
   - `storeLogin()` pentru procesarea datelor din formularul de autentificare.
3. Creați rute pentru înregistrarea, autentificarea și deconectarea utilizatorului.
4. Actualizați vizualizările pentru formularele de înregistrare și autentificare.
5. Creați o clasă separată `Request` pentru validarea datelor de înregistrare sau autentificare sau adăugați validarea direct în controller.
6. Verificați dacă înregistrarea și autentificarea utilizatorului funcționează corect.

### Nr. 3. Autentificarea utilizatorilor cu ajutorul componentelor existente

> [!NOTE]
> Acest pas este opțional și poate fi realizat după partea principală a lucrării.

1. Instalați biblioteca **Laravel Breeze** (sau Fortify, Jetstream) pentru o configurare rapidă a autentificării:
    ```bash
    php artisan breeze:install
    npm install && npm run dev
    php artisan migrate
    ```
2. Urmați instrucțiunile de instalare și configurare a pachetului.
3. Verificați dacă rutele `/register`, `/login`, `/logout` funcționează corect.

### Nr. 4. Autorizarea utilizatorilor

1. Implementați o pagină „Panou personal”, accesibilă doar utilizatorilor autentificați.
2. Configurați verificarea accesului la această pagină, adăugând middleware-ul `auth` în rută sau implementând verificarea în controller.
3. Actualizați vizualizarea paginii „Panou personal” pentru a afișa informațiile disponibile exclusiv utilizatorilor autentificați.

### Nr. 5. Rolurile utilizatorilor

1. Adăugați un sistem de roluri: **Administrator** și **Utilizator**.
2. Configurați comportamentul pentru fiecare rol:
   1. **Administrator**: are posibilitatea de a vizualiza panourile personale ale tuturor utilizatorilor.
   2. **Utilizator**: poate vizualiza doar propriul panou personal.
3. Implementați verificările rolurilor folosind metoda `can`, `Gate` sau `middleware`, pentru a asigura distribuirea corectă a drepturilor de acces.

### Nr. 6. Deconectarea și protecția împotriva CSRF

1. Adăugați un buton pentru deconectarea utilizatorului pe pagină.
2. Asigurați protecția împotriva atacurilor CSRF pe formulare.
3. Verificați că deconectarea utilizatorului funcționează corect și sigur.

## Întrebări de control

1. Ce soluții integrate pentru autentificare oferă Laravel?
2. Ce metode de autentificare a utilizatorilor cunoașteți?
3. Care este diferența dintre autentificare și autorizare?
4. Cum se asigură protecția împotriva atacurilor CSRF în Laravel?