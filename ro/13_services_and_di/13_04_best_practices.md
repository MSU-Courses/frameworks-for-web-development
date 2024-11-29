# Cele mai bune practici în Laravel

Atunci când proiectezi o aplicație, este important să urmezi cele mai bune practici pentru a menține arhitectura curată, ușor de înțeles și ușor de întreținut.

Un excelent ghid pe tema Laravel și arhitecturii aplicațiilor complexe este cartea scrisă de Adel Faizrakhmanov: [„Arhitectura aplicațiilor web complexe. Exemple pe Laravel”](https://github.com/adelf/acwa_book_ru).

Mai jos sunt principalele recomandări privind utilizarea serviciilor și separarea responsabilităților.



## Când să folosești serviciile?

**Serviciile** sunt clase care incapsulează logica de business, făcând-o accesibilă altor părți ale aplicației. Utilizarea serviciilor este recomandată în următoarele situații:

1. **Logică de business complexă**  
   Dacă logica unei operațiuni implică mai mulți pași, este recomandat să o muți într-un serviciu pentru a simplifica codul.

2. **Reutilizare**  
   Dacă aceeași logică este utilizată în mai multe locuri din aplicație (ex. în controllere sau comenzi console), ar trebui să fie extrasă într-un serviciu.

3. **Testare mai ușoară**  
   Serviciile sunt mai ușor de testat separat de alte componente, deoarece nu depind de framework (ex. solicitări HTTP).

4. **Separarea clară a logicii de business**  
   Controllerele ar trebui să rămână cât mai simple, în timp ce logica principală a aplicației ar trebui să fie mutată în servicii.



## Cum să eviți complicarea arhitecturii?

Este important să eviți o arhitectură excesiv de complicată, care poate îngreuna întreținerea aplicației și reduce eficiența echipei. Iată câteva sfaturi:

1. **Nu crea servicii fără necesitate**  
   Nu muta logica simplă, utilizată într-un singur loc, într-un serviciu separat.

2. **Respectă principiul „o acțiune — un serviciu”**  
   Fiecare serviciu ar trebui să aibă o singură responsabilitate clară. Nu îl încărca cu logică inutilă.

3. **Evită abstractizările excesive**  
   Un număr prea mare de interfețe și niveluri de abstractizare poate face codul inutil de complicat.

4. **Urmează principiul YAGNI (You Aren't Gonna Need It)**  
   Nu adăuga funcționalități care s-ar putea să nu fie necesare în viitor.



## Separarea responsabilităților (Single Responsibility Principle)

**Principiul responsabilității unice (SRP)** spune că o clasă ar trebui să îndeplinească o singură sarcină. Respectarea acestui principiu face codul mai ușor de testat, înțeles și întreținut.

### Exemplu de încălcare a SRP:

```php
class AuthController extends Controller
{
    public function login(Request $request)
    {
        $user = User::where('email', $request->email)->first();

        if (!$user || !Hash::check($request->password, $user->password)) {
            return response()->json(['error' => 'Invalid credentials'], 401);
        }

        $token = $user->createToken('auth_token')->plainTextToken;

        return response()->json(['token' => $token]);
    }
}
```

În acest exemplu, controllerul îndeplinește mai multe sarcini:

1. Verifică utilizatorul.
2. Hasează și verifică parola.
3. Creează un token.

Acest lucru face codul dificil de testat și încalcă principiul SRP.



## Exemplu: Separarea logicii de autentificare într-un serviciu

Logica de autentificare poate fi mutată într-un serviciu separat:

```php
class AuthService
{
    public function authenticate(string $email, string $password): string
    {
        $user = User::where('email', $email)->first();

        if (!$user || !Hash::check($password, $user->password)) {
            throw new \Exception('Invalid credentials');
        }

        return $user->createToken('auth_token')->plainTextToken;
    }
}
```

Acum controllerul devine mult mai simplu:

```php
class AuthController extends Controller
{
    protected AuthService $authService;

    public function __construct(AuthService $authService)
    {
        $this->authService = $authService;
    }

    public function login(Request $request)
    {
        try {
            $token = $this->authService->authenticate($request->email, $request->password);
            return response()->json(['token' => $token]);
        } catch (\Exception $e) {
            return response()->json(['error' => $e->getMessage()], 401);
        }
    }
}
```



## Avantajele abordării

1. **Testare mai ușoară**  
   Acum poți testa `AuthService` independent de solicitările HTTP, garantând fiabilitatea logicii.

2. **Claritatea codului**  
   Controllerul îndeplinește o singură sarcină — gestionează cererile și le transmite serviciului.

3. **Reutilizare**  
   `AuthService` poate fi utilizat în alte părți ale aplicației, cum ar fi comenzi console sau evenimente.

4. **Ușurința întreținerii**  
   Orice modificare în logica de autentificare va necesita doar actualizarea serviciului, fără a afecta restul aplicației.



## Rezumat

1. Folosește serviciile pentru logica complexă, reutilizabilă sau independentă.
2. Respectă principiul SRP — separă responsabilitățile între clase.
3. Evită complicațiile inutile prin abstractizări excesive.
4. Organizează codul pentru a fi ușor de testat, întreținut și extins.