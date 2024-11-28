# Autorizarea

> [!NOTE]  
> **Autorizarea în aplicațiile web** reprezintă procesul de determinare a drepturilor și privilegiilor unui utilizator atunci când accesează diverse resurse și funcționalități ale sistemului. După autentificare, care stabilește identitatea utilizatorului, urmează autorizarea, care definește acțiunile pe care utilizatorul le poate efectua. Autorizarea răspunde la întrebarea: „_Ce poate face utilizatorul?_”.

## Tipuri de autorizare în aplicațiile web

Există mai multe modele de autorizare, fiecare fiind potrivit pentru cerințe specifice ale sistemului:

### 1. Modelul de acces bazat pe roluri (Role-Based Access Control, RBAC)

În acest model, utilizatorilor li se atribuie roluri specifice, fiecare având un set de permisiuni.  
**De exemplu**, rolurile pot include „_Administrator_”, „_Editor_” și „_Utilizator_”, fiecare având acces la anumite funcționalități ale sistemului.

**Utilizare**:

- Sisteme corporative cu atribuții clar definite.  
- Aplicații web cu diferite niveluri de acces la conținut.  

**Avantaje și dezavantaje**:

| Avantaje                                   | Dezavantaje                                                                               |
| ------------------------------------------ | ---------------------------------------------------------------------------------------- |
| Gestionare simplă a drepturilor de acces   | Poate fi insuficient de flexibil pentru sisteme complexe cu cerințe diverse de acces     |
| Ușor de atribuit și modificat rolurile     |                                                                                          |

### 2. Modelul de acces discreționar (Discretionary Access Control, DAC)

În acest model, proprietarul unei resurse decide cine are acces la aceasta și ce acțiuni sunt permise.  
Utilizatorii pot controla accesul la propriile resurse.

**Utilizare**:

- Sisteme de partajare a fișierelor.  
- Rețele sociale, unde utilizatorii decid cine poate accesa conținutul lor.

**Avantaje și dezavantaje**:

| Avantaje                                  | Dezavantaje                                                 |
| ----------------------------------------- | ---------------------------------------------------------- |
| Flexibilitate în gestionarea accesului    | Poate duce la acordarea accidentală a accesului            |
| Control asupra propriilor date            | Dificultăți în administrarea drepturilor în sisteme mari   |


### 3. Modelul de acces mandatar (Mandatory Access Control, MAC)

În acest model, accesul la resurse este determinat de un administrator centralizat, în funcție de politici stricte de securitate.  
Utilizatorii nu pot modifica singuri drepturile de acces.

**Utilizare**:

- Sisteme militare și guvernamentale cu cerințe ridicate de securitate.  
- Sisteme care gestionează date confidențiale.

**Avantaje și dezavantaje**:

| Avantaje                                      | Dezavantaje                        |
| --------------------------------------------- | ---------------------------------- |
| Nivel ridicat de control și securitate        | Mai puțină flexibilitate pentru utilizatori |
| Reducerea riscurilor de acces neautorizat     | Administrare mai complexă          |


### 4. Modelul de acces bazat pe atribute (Attribute-Based Access Control, ABAC)

ABAC definește drepturile de acces pe baza unui set de atribute asociate utilizatorului, resursei, acțiunii și contextului.  
Acest model permite crearea unor politici complexe și flexibile de acces.

**Utilizare**:

- Sisteme cu cerințe dinamice și complexe de acces.  
- Servicii cloud și arhitecturi bazate pe microservicii.

**Avantaje și dezavantaje**:

| Avantaje                                                                          | Dezavantaje                                             |
| --------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Flexibilitate și granularitate ridicată în controlul accesului                    | Complexitate crescută în dezvoltarea și întreținerea politicilor |
| Posibilitatea de a lua în considerare factori contextuali (ex. timp, locație)     | Necesită o gestionare atentă a atributelor și actualizării acestora |

---

## Combinarea modelelor de autorizare

În aplicațiile reale, se combină adesea mai multe modele pentru a echilibra securitatea și ușurința în utilizare. De exemplu, RBAC poate fi combinat cu ABAC pentru a defini roluri generale, iar apoi a rafina accesul utilizând atribute specifice.

**Exemplu**:

- **RBAC:** Atribuirea utilizatorului rolului de „Manager”.  
- **ABAC:** Permisiunea managerului de a accesa doar datele departamentului său, bazată pe atributul „Departament”.

Această combinație permite construirea unor sisteme flexibile și scalabile, care răspund atât cerințelor generale, cât și celor specifice.

> [!NOTE]  
> În contextul Laravel, vom analiza două modele principale de autorizare: **RBAC** și **ABAC**.  
> Laravel oferă instrumente puternice pentru implementarea ambelor modele, permițând dezvoltarea aplicațiilor web sigure și flexibile.

## Implementarea autorizării în Laravel

În Laravel, autorizarea se bazează pe utilizarea **Politicilor** (Policies) și **Porților** (Gates), care definesc regulile de acces la resurse și acțiuni.

- **Politicile** stabilesc regulile de acces pentru modele specifice (de exemplu, utilizatori sau postări).  
- **Porțile** permit definirea regulilor de acces la nivelul acțiunilor (de exemplu, crearea, editarea sau ștergerea).

### Porțile (Gates)

**Porțile** sunt funcții care determină dacă un utilizator poate efectua o anumită acțiune. Acestea sunt utile pentru autorizarea acțiunilor care nu implică modele specifice.

#### Definirea porților

Porțile sunt de obicei definite în metoda `boot` a clasei `App\Providers\AuthServiceProvider`, folosind `Gate`.

```php
use Illuminate\Support\Facades\Gate;

public function boot()
{
    $this->registerPolicies();
    
    Gate::define('view-dashboard', function ($user) {
        return $user->isAdmin();
    });
}
```

În exemplul de mai sus, porțile `view-dashboard` verifică dacă utilizatorul este administrator.

#### Utilizarea porților

După ce porțile sunt definite, acestea pot fi utilizate în controllere sau vizualizări pentru a verifica accesul.

```php
if (Gate::allows('view-dashboard')) {
    // Utilizatorul poate vizualiza dashboard-ul
}

if (Gate::denies('view-dashboard')) {
    // Accesul este refuzat
}
```

În șabloanele Blade, porțile pot fi utilizate cu directivele `@can` și `@cannot`.

```blade
@can('view-dashboard')
    <!-- Afișează dashboard-ul -->
@endcan

@cannot('view-dashboard')
    <!-- Conținut pentru utilizatorii obișnuiți -->
@endcannot
```

### Politicile (Policies)

**Politicile** sunt clase care stabilesc regulile de acces pentru modele specifice. Fiecare politică conține metode pentru diverse acțiuni (de exemplu, vizualizare, creare, editare, ștergere).

#### Crearea unei politici

Pentru a crea o politică, utilizați comanda Artisan:

```bash
php artisan make:policy PostPolicy
```

Aceasta va genera o clasă de politică în directorul `app/Policies`.

#### Definirea metodelor politicii

În clasa de politică, definiți metodele pentru acțiunile pe care doriți să le autorizați:

```php
namespace App\Policies;

use App\Models\Post;
use App\Models\User;

class PostPolicy
{
    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }
}
```

Metoda verifică dacă utilizatorul este autorul postării înainte de a permite actualizarea.

#### Înregistrarea politicii

După crearea politicii, trebuie să o înregistrați în metoda `boot` a clasei `App\Providers\AuthServiceProvider`:

```php
namespace App\Providers;

use App\Models\Post;
use App\Policies\PostPolicy;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    protected $policies = [
        Post::class => PostPolicy::class,
    ];

    public function boot()
    {
        $this->registerPolicies();
    }
}
```

Acum Laravel va folosi această politică pentru a autoriza acțiunile asupra modelului `Post`.

#### Utilizarea politicii

După înregistrare, politica poate fi utilizată în controllere sau vizualizări:

```php
if ($request->user()->can('update', $post)) {
    // Utilizatorul poate actualiza postarea
}
```

În Blade, directivele `@can` și `@cannot` pot fi utilizate:

```blade
@can('update', $post)
    <!-- Afișează butonul de editare -->
@endcan

@cannot('update', $post)
    <!-- Ascunde but

onul de editare -->
@endcannot
```

#### Răspunsuri personalizate ale politicilor

Politicile pot returna diverse valori pentru a aproba sau refuza accesul:

- `true` — acces aprobat.  
- `false` — acces refuzat.  
- `null` — politica nu este definită pentru acțiunea respectivă.  
- `Response` — un obiect special care explică motivul refuzului.

Exemplu de utilizare a `Response`:

```php
use Illuminate\Auth\Access\Response;

public function update(User $user, Post $post)
{
    if ($user->id === $post->user_id) {
        return Response::allow();
    } else {
        return Response::deny('Nu sunteți autorul acestei postări.');
    }
}
```

## Concluzie

Autorizarea este esențială pentru securitatea aplicațiilor web, permițând controlul accesului utilizatorilor la resurse și funcționalități. Laravel oferă un sistem puternic de autorizare bazat pe **Politici** și **Porți**, care permite crearea unor reguli flexibile și sigure. Înțelegerea și utilizarea corectă a acestora contribuie la dezvoltarea unor aplicații web robuste.