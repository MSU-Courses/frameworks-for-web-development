# Arhitectura ciclului de viață al unui request în Laravel

Când folosiți orice instrument, este esențial să înțelegeți cum funcționează. Acest lucru vă va ajuta să vă simțiți mai încrezători și să rezolvați problemele care pot apărea. La baza funcționării Laravel se află **ciclul de viață al procesării unui request** (request lifecycle).  
În acest articol, vom explora cum se procesează un request în Laravel.

## Ciclul de viață al procesării unui request

### Procesarea inițială a request-ului

Punctul de intrare în aplicația Laravel este fișierul `public/index.php`.

Toate cererile care ajung pe server sunt direcționate către acest fișier pentru a fi procesate.

### Nucleul HTTP și nucleul consolei

După ce request-ul ajunge în fișierul `public/index.php`, acesta este transmis către nucleul HTTP sau nucleul consolei.

- **Nucleul HTTP** se ocupă de cererile care vin prin HTTP.
- **Nucleul consolei** gestionează cererile care vin prin consola de comandă.

Principala funcție a nucleului HTTP este să ruleze o serie de **bootstrappers** înainte de a procesa cererea. Acești bootstrappers configurează diverse elemente, precum logarea, gestionarea erorilor, determinarea mediului de lucru al aplicației (ex: dezvoltare sau producție) și alte sarcini importante care trebuie realizate înainte de a continua. De regulă, în aceste clase are loc configurarea setărilor Laravel, de care dezvoltatorii nu trebuie să se îngrijoreze.

Nucleul HTTP se asigură, de asemenea, că request-ul trece printr-un "stack de middleware-uri". Aceste middleware-uri se ocupă de sarcini specifice, precum gestionarea sesiunilor, verificarea token-urilor CSRF sau verificarea stării aplicației (de exemplu, dacă se află în modul de întreținere).

### Service Providers (Furnizori de servicii)

> [!NOTE]  
> Încă nu sunteți familiarizați cu **furnizorii de servicii** (service providers), dar nu vă faceți griji, vom detalia acest subiect în articolele următoare.

Unul dintre cele mai importante aspecte ale nucleului Laravel este rularea **furnizorilor de servicii** (service providers), care configurează diferitele componente ale aplicației, cum ar fi baza de date, validarea datelor, lucrul cu cozi de sarcini (queues) și rutarea.

Laravel parcurge lista acestor furnizori de servicii, îi creează și îndeplinește două sarcini majore: inițializarea lor prin metoda `register` și apoi rularea lor cu metoda `boot`, pentru a pregăti toate componentele necesare aplicației.

Aproape toate funcționalitățile importante din Laravel sunt configurate prin acești furnizori, ceea ce îi face o parte esențială a încărcării aplicației. De asemenea, puteți crea propriii furnizori de servicii, dacă este necesar.

Acești furnizori se găsesc în directorul `app/Providers`, și îi vom analiza mai detaliat în articolele viitoare.

### Rutarea

După ce aplicația a fost încărcată, request-ul este transmis către un sistem special care decide ce să facă în continuare — fie să trimită request-ul către un anumit **rute** sau către un **controller** pentru a fi procesat.

> [!TIP]  
> De exemplu, când introduceți adresa `http://example.com/about`, Laravel trebuie să înțeleagă că trebuie să afișeze pagina "Despre noi". Aceasta este realizată prin sistemul de rutare din Laravel.

Înainte de a fi procesat, request-ul trece prin middleware-uri. Acestea sunt programe care verifică anumite condiții. De exemplu, un middleware poate verifica dacă utilizatorul este autentificat. Dacă nu, middleware-ul redirecționează utilizatorul către pagina de autentificare. Dacă totul este în regulă, request-ul continuă procesarea.

Unele middleware-uri verifică toate cererile din aplicație (de exemplu, când aplicația este în modul de întreținere), în timp ce altele funcționează doar pe anumite rute. După ce request-ul trece prin toate middleware-urile necesare, codul corespunzător este executat, iar rezultatul este trimis înapoi utilizatorului prin aceleași middleware-uri.

### Finalizarea procesării request-ului

După ce metoda din ruta sau controller-ul corespunzător returnează un răspuns, acesta trece din nou prin middleware-uri. Middleware-urile pot modifica sau verifica răspunsul înainte ca acesta să fie trimis utilizatorului.

După aceasta, metoda `handle` a nucleului HTTP returnează răspunsul aplicației. Apoi este apelată metoda `send`, care trimite răspunsul către browserul utilizatorului.

Acesta este întregul proces prin care trece un request în Laravel, iar utilizatorul primește răspunsul!

![Laravel HTTP Lifecycle](https://imgur.com/LkJvRcb.png) [^2]

[^1]: Request Lifecycle, laravel.com [online]. URL: https://laravel.com/docs/10.x/lifecycle
[^2]: Laravel Request Lifecycle, medium [online]. URL: https://medium.com/@ankitatejani84/laravel-request-lifecycle-7c2145aa1257