# Configurarea inițială a unui proiect Laravel

După ce ați creat proiectul, este necesar să îl configurați. În această secțiune, vom discuta despre pașii de bază pe care trebuie să îi urmați pentru a realiza configurarea inițială a unui proiect Laravel.

Pe măsură ce veți învăța mai multe despre framework, veți descoperi și alte setări și funcționalități disponibile în Laravel.

## Fișierul de mediu `.env`

Înainte de a înțelege ce este `.env`, trebuie să cunoașteți conceptul de **variabile de mediu**.

**Variabilele de mediu** sunt variabile stocate în sistemul de operare și disponibile pentru toate procesele (programele) care rulează pe acel sistem.

`.env` este un fișier care conține variabilele de mediu ale proiectului. Laravel folosește un mecanism care încarcă automat aceste variabile la pornirea aplicației.

> [!NOTE]  
> **Mediul** este contextul în care rulează aplicația dumneavoastră, de exemplu, _dezvoltare_, _testare_ sau _producție_. În funcție de mediu, pot varia parametrii precum baza de date, serverul, setările de securitate, cache-ul și altele.

### De ce este necesar `.env`?

Fișierul `.env` este utilizat pentru a stoca informații sensibile, cum ar fi parolele, cheile și alți parametri care pot varia în funcție de mediu (de exemplu, dezvoltare, testare, producție). Acest lucru vă permite să păstrați aceste date separat de codul sursă și să evitați expunerea lor în repository-uri [^1].

Este important să rețineți că fișierul `.env` nu ar trebui să fie încărcat într-un repository. În schimb, un fișier `.env.example` ar trebui să fie prezent în repository, conținând o listă a tuturor variabilelor necesare pentru funcționarea aplicației.

Dezvoltatorii care lucrează la proiect trebuie să creeze propriul fișier `.env`, pe baza fișierului `.env.example`, și să îl configureze în funcție de mediul lor de lucru.

Să presupunem că aveți un proiect care utilizează o bază de date.  
Pentru ca acesta să funcționeze, trebuie să specificați parametrii de conectare, cum ar fi numele bazei de date, utilizatorul și parola. Dacă mai mulți dezvoltatori lucrează la proiect, fiecare își poate configura propriul fișier `.env` cu parametrii săi de conectare. De asemenea, va exista un fișier `.env` separat pentru producție, în care sunt specificați parametrii de conectare pentru mediul de lucru.

### Structura fișierului `.env` [^2]

Fișierul `.env` este un fișier text în care fiecare linie conține o variabilă de mediu în formatul `cheie=valoare`.

Exemplu de configurare a variabilelor de bază în fișierul `.env`:

```plaintext
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:qWv3Z
APP_DEBUG=true
APP_URL=http://localhost
```

> [!NOTE]  
> Dacă valoarea unei variabile de mediu conține spații, trebuie să fie încadrată în ghilimele duble.  
> De exemplu: `APP_NAME="My Laravel App"`

Să analizăm câteva dintre aceste **variabile de mediu**:

- `APP_NAME` — numele aplicației dumneavoastră.
- `APP_ENV` — mediul aplicației (de exemplu, `local`, `testing`, `production`).
- `APP_KEY` — cheia aplicației, utilizată pentru criptarea datelor (poate fi generată cu comanda `php artisan key:generate`).
- `APP_DEBUG` — modul de depanare al aplicației.
  - `true` — modul de depanare este activat.
  - `false` — modul de depanare este dezactivat.
- `APP_URL` — URL-ul aplicației dumneavoastră.

Aceste variabile sunt utilizate în multe părți ale aplicației, deci este esențial să fie configurate corect.

> [!NOTE]  
> Alte variabile de mediu vor fi discutate în secțiunile următoare.

### Fișiere de mediu suplimentare

În Laravel, puteți crea fișiere suplimentare pentru medii diferite.

De exemplu, pentru mediul de **testare** se poate crea fișierul `.env.testing`, unde vor fi specificați parametrii pentru testare.

Ce variabile de mediu ar putea fi în fișierul `.env.testing`? De exemplu, parametrii de conectare la baza de date de testare, care va fi utilizată pentru rularea testelor aplicației.

## Directorul `storage`

Când începeți un nou proiect Laravel, trebuie să configurați directorul `storage`.

Directorul `storage` este utilizat pentru a stoca fișiere temporare, cache-uri, sesiuni, jurnale și alte date generate în timpul rulării aplicației.

### Permisiuni de acces

Pentru ca aplicația să funcționeze corect, trebuie să setați permisiunile corecte pentru directorul `storage`.

Executați următoarea comandă pentru a seta permisiunile `775` pentru directorul `storage`, ceea ce înseamnă că Laravel va avea drepturi de scriere în acest director și subdirectoarele sale.

```sh
chmod -R 775 storage
```

### Link simbolic către folderul `storage`

Când încărcați fișiere prin aplicația Laravel, acestea sunt salvate implicit în folderul `storage/app/public`.

> [!NOTE]  
> Pe măsură ce proiectul crește, fișierele încărcate pot ocupa mult spațiu. Pentru a evita supraîncărcarea serverului, puteți muta fișierele pe un serviciu de stocare cloud, cum ar fi Amazon S3 sau Google Cloud Storage, puteți utiliza servere dedicate pentru stocare sau puteți configura stocarea în directoare externe.

Totuși, aceste fișiere nu sunt accesibile prin browser în mod implicit, deoarece, așa cum am menționat, fișierele accesibile trebuie să fie în directorul `public`.

Pentru a rezolva acest lucru, Laravel utilizează un **link simbolic** (prescurtat "symlink").

#### Ce este un link simbolic? [^3]

Gândiți-vă la un link simbolic ca la o scurtătură sau un pod între două directoare. În cazul nostru, symlink-ul creează o legătură între folderul `storage/app/public` și `public/storage`. Acest lucru înseamnă că atunci când browserul va accesa fișierele din calea `public/storage`, ele vor fi, de fapt, preluate din folderul `storage/app/public`. Astfel, puteți încărca fișiere și să le faceți accesibile pentru utilizatori, dar fișierele fizic rămân în folderul `storage`.

#### De ce este necesar un symlink?

Atunci când Laravel lucrează cu fișiere (de exemplu, imagini sau documente), acestea sunt stocate în folderul `storage`, care nu este accesibil direct prin browser, din motive de securitate. În folderul `storage` pot fi prezente date sensibile. Dar dacă trebuie ca utilizatorii să poată vizualiza sau descărca fișierele încărcate, creăm acest "pod" între folderul `storage` și folderul public `public/storage`, accesibil prin web [^4].

#### Cum se creează un symlink?

Pentru a crea un link simbolic în Laravel, folosiți comanda:

```bash
php artisan storage:link
```

Acum, toate fișierele pe care le încărcați prin aplicație vor fi accesibile la adresa `http://my-site.com/storage/my_file`.

#### Exemplu:
1. Încărcați o imagine prin aplicație, aceasta este salvată în folderul `storage/app/public/images/my_file.jpg`.
2. Datorită symlink-ului, imaginea devine accesibilă la adresa `http://my-site.com/storage/images/my_file.jpg`.

#### De ce este necesar acest proces?
- **Securitate**: Puteți stoca date sensibile în folderul `storage` și fiți siguri că nu vor fi accesibile din exterior.
- **Confort**: Symlink-ul permite accesul public la fișiere prin browser, păstrând în același timp structura ordonată a fișierelor.

Astfel, _link-ul simbolic_ este un mod sigur și eficient de a gestiona fișierele pe care trebuie să le faceți accesibile utilizatorilor aplicației dumneavoastră.

## Concluzie

În această secțiune, am discutat despre configurarea inițială a unui proiect Laravel, care include configurarea fiș ierului `.env` și a directorului `storage`.

Pe măsură ce avansați, veți descoperi și alte configurări care vă vor fi necesare în utilizarea framework-ului.

[^1]: What is .env ? codementor.io [online]. URL: https://www.codementor.io/@parthibakumarmurugesan/what-is-env-how-to-set-up-and-run-a-env-file-in-node-1pnyxw9yxj  
[^2]: Configurația Laravel, laravel.com [online]. URL: https://laravel.com/docs/101.x/configuration#environment-configuration  
[^3]: Link simbolic, komprise [online]. URL: https://www.komprise.com/glossary_terms/symbolic-link/  
[^4]: Stocare fișiere, laravel.com [online]. URL: https://laravel.com/docs/10.x/filesystem