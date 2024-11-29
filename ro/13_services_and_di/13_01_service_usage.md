# Utilizarea Serviciilor

## Conceptul de Servicii într-o Aplicație

În secțiunea despre arhitectura aplicațiilor web, am menționat deja conceptul de _servicii_. Atunci, acestea au fost prezentate ca o parte a arhitecturii generale a sistemului. În acest capitol, ne vom concentra pe rolul lor în cadrul unei singure aplicații.

**Un serviciu** este o clasă care îndeplinește o sarcină specifică. Scopul principal al serviciilor este de a structura codul, împărțind funcționalitatea aplicației în părți independente. Această abordare face codul mai ușor de înțeles, testat și întreținut.

Pentru a înțelege mai bine, putem folosi o analogie din viața reală. Imaginați-vă un restaurant:

- Bucătarul prepară mâncarea.
- Chelnerul servește clienții.
- Administratorul gestionează procesele.

Fiecare își îndeplinește sarcina specifică, iar împreună asigură funcționarea restaurantului. Serviciile dintr-o aplicație funcționează pe același principiu: fiecare răspunde de o funcție proprie, iar împreună susțin funcționarea sistemului.

Exemple de sarcini pe care le pot îndeplini serviciile:

- Gestionarea bazelor de date.
- Manipularea fișierelor.
- Procesarea logicii de afaceri.

Serviciile ajută la distribuirea responsabilităților între diferitele părți ale aplicației, făcând-o mai flexibilă și mai ușor de întreținut.

---

## Ce sunt logica de afaceri și domeniul de aplicare?

Înainte de a studia serviciile, este important să înțelegem două concepte-cheie: _logica de afaceri_ și _domeniul de aplicare_.

**Logica de afaceri** reprezintă regulile și procesele care definesc modul în care aplicația își îndeplinește sarcinile în cadrul unui domeniu specific.

**Domeniul de aplicare** reprezintă domeniul de cunoștințe pentru care este creată aplicația. De exemplu:

- Pentru un magazin online: vânzarea produselor.
- Pentru o rețea socială: interacțiunea dintre utilizatori.
- Pentru o aplicație financiară: gestionarea finanțelor.

> [!NOTE]
> În proiectele mari, se recomandă utilizarea unei abordări _orientate pe domeniul de aplicare ([Domain-Driven Design](https://www.techtarget.com/whatis/definition/domain-driven-design), DDD)_, care ajută la concentrarea pe logica de afaceri și pe modelarea proceselor reale, împărțind aplicația în domenii clare. Această metodă facilitează crearea unei arhitecturi scalabile, îmbunătățește înțelegerea cerințelor și simplifică întreținerea codului.

Logica de afaceri definește **ce face aplicația**. Ea nu include aspectele tehnice, cum ar fi procesarea cererilor HTTP sau gestionarea interfeței.

Exemple de diferențe între logica de afaceri și logica infrastructurii:

| Aparține logicii de afaceri     | Nu aparține logicii de afaceri   |
| ------------------------------- | -------------------------------- |
| Calculul costului unei comenzi  | Gestionarea cererilor HTTP       |
| Determinarea reducerilor        | Validarea datelor din formulare  |
| Verificarea accesului la resurse| Trimiterea notificărilor e-mail  |

---

## De ce utilizarea serviciilor îmbunătățește arhitectura aplicației?

Dacă într-o aplicație nu se folosesc servicii, logica poate fi concentrată într-un singur loc, cum ar fi un controler. Acest lucru duce la următoarele probleme:

- Codul devine dificil de citit și înțeles.
- Este greu de testat.
- Orice modificare crește riscul de erori și de duplicare a codului.

Să analizăm un exemplu de metodă într-un controler care gestionează adăugarea unui produs în coș:

```php
class CartController extends Controller
{
    public function addToCart(Request $request)
    {
        $data = $request->validate([
            'product_id' => 'required|integer',
            'quantity' => 'required|integer',
        ]);

        $product = Product::findOrFail($data['product_id']);
        $cart = Cart::firstOrCreate(['user_id' => auth()->id()]);
        $cartItem = CartItem::firstOrNew([
            'cart_id' => $cart->id,
            'product_id' => $product->id,
        ]);
        $cartItem->quantity += $data['quantity'];
        $cartItem->save();

        return redirect()->route('cart');
    }
}
```

În acest cod sunt combinate mai multe sarcini:

- Validarea datelor.
- Căutarea sau crearea unui coș.
- Adăugarea unui produs în coș.

Aceasta încalcă **Principiul responsabilității unice (Single Responsibility Principle)** din SOLID, conform căruia o clasă sau o metodă ar trebui să aibă o singură responsabilitate. În plus, dacă se adaugă noi reguli sau se modifică logica, va fi necesară editarea întregii metode, ceea ce crește riscul de erori și duplicare a codului.

---

## Separarea logicii folosind servicii

O soluție pentru această problemă este **separarea logicii de afaceri în servicii dedicate**. De exemplu, putem crea `CartService`, care să gestioneze operațiunile legate de coșul de cumpărături.

```php
class CartService
{
    /**
     * Adaugă un produs în coș.
     *
     * @param array $data Datele produsului.
     */
    public function addToCart(array $data): void
    {
        $product = Product::findOrFail($data['product_id']);
        $cart = Cart::firstOrCreate(['user_id' => auth()->id()]);
        $cartItem = CartItem::firstOrNew([
            'cart_id' => $cart->id,
            'product_id' => $product->id,
        ]);
        $cartItem->quantity += $data['quantity'];
        $cartItem->save();
    }
}
```

Acum controlerul devine mai simplu și mai clar:

```php
class CartController extends Controller
{
    protected CartService $cartService;

    public function __construct(CartService $cartService)
    {
        $this->cartService = $cartService;
    }

    public function addToCart(CartRequest $request)
    {
        $this->cartService->addToCart($request->validated());

        return redirect()->route('cart');
    }
}
```

Este important de înțeles că serviciile pot fi de diferite tipuri. Unele dintre ele gestionează logica de afaceri, de exemplu:

- `CartService` — gestionarea coșului.
- `OrderService` — procesarea comenzilor.
- `UserService` — gestionarea utilizatorilor.

Alte servicii rezolvă sarcini legate de infrastructură:

- `FileService` — manipularea fișierelor.
- `MailService` — trimiterea e-mailurilor.
- `MessageGenerator` — generarea mesajelor.
- `DataImporter` — importul datelor.
- `FileUploader` — încărcarea fișierelor.

De asemenea, fabricile din Laravel pot fi considerate exemple de servicii.