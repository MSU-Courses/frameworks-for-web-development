# Validarea datelor în Laravel

## Ce este validarea și de ce este importantă?

**Validarea datelor** reprezintă procesul de verificare a datelor introduse pentru a se asigura că acestea respectă anumite reguli înainte de a fi procesate. În Laravel, validarea este integrată în cadrul framework-ului și oferă instrumente flexibile și intuitive pentru gestionarea acestui proces. Scopul principal al validării în Laravel este de a preveni utilizarea datelor incorecte sau potențial periculoase de către aplicație.

Laravel oferă mai multe modalități de a implementa validarea:

1. Validarea direct în controllere.
2. Utilizarea obiectelor `Form Request`.
3. Validarea manuală prin intermediul fațadei `Validator`.

Fiecare abordare are avantajele sale și se potrivește unor scenarii diferite. **De exemplu**, validările simple pot fi realizate direct în controler, în timp ce validările mai complexe pot fi gestionate în clase dedicate.

## Validarea în Laravel

### Regulile de bază pentru validare

Laravel pune la dispoziție un set extins de reguli predefinite, cum ar fi:

- `required` — câmp obligatoriu.
- `email` — verifică dacă datele sunt un email valid.
- `max:value` — limitează numărul maxim de caractere.
- `min:value` — impune un număr minim de caractere.
- `unique:table,column` — verifică unicitatea în baza de date.

Lista completă a regulilor este disponibilă în [documentația Laravel](https://laravel.com/docs/11.x/validation#available-validation-rules).

Exemplu de utilizare a regulilor predefinite:

```php
$validatedData = $request->validate([
    'title' => 'required|min:3',
    'email' => 'required|email',
    'password' => 'required|min:6',
]);
```

Acest cod verifică următoarele:
- `title` este obligatoriu și trebuie să aibă cel puțin 3 caractere.
- `email` este obligatoriu și trebuie să fie o adresă validă.
- `password` trebuie să aibă minimum 6 caractere.

### Mesajele de eroare

Laravel generează automat mesaje de eroare pentru regulile standard, însă acestea pot fi personalizate fie în fișierul de traducere `resources/lang/{locale}/validation.php`, fie direct în metoda `validate`:

```php
$validatedData = $request->validate([
    'title' => 'required|min:3',
    'email' => 'required|email',
    'password' => 'required|min:6',
], [
    'title.required' => 'Câmpul "Titlu" este obligatoriu.',
    'email.email' => 'Câmpul "Email" trebuie să conțină o adresă validă.',
]);
```

### Validarea în controllere

Cel mai simplu mod de a valida datele este utilizarea metodei `validate` în controler:

```php
public function store(Request $request)
{
    $validatedData = $request->validate([
        'title' => 'required|min:3',
        'email' => 'required|email',
        'password' => 'required|min:6',
    ]);

    // Datele sunt valide și pot fi procesate
}
```

### Cum funcționează metoda `validate`?

Metoda `validate` primește doi parametri:
1. Un array de reguli de validare.
2. Un array de mesaje personalizate de eroare.

Dacă datele nu trec validarea, Laravel redirecționează utilizatorul **înapoi la pagina anterioară** și afișează erorile.

## Crearea claselor personalizate Request

Clasele `Form Request` sunt clase specializate pentru procesarea și validarea datelor. Ele permit separarea logicii de validare de controlere, îmbunătățind lizibilitatea și întreținerea codului.

Fiecare clasă `Form Request` extinde clasa de bază `Illuminate\Foundation\Http\FormRequest`. În aceste clase puteți defini regulile de validare și drepturile de acces pentru cererea respectivă.

### Crearea unei clase personalizate Request

Pentru a crea o nouă clasă `Form Request`, utilizați comanda Artisan:

```bash
php artisan make:request CreateTaskRequest
```

Aceasta va crea o nouă clasă `CreateTaskRequest` în directorul `app/Http/Requests`. În această clasă puteți defini regulile de validare și mesajele de eroare:

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class CreateTaskRequest extends FormRequest
{
    public function authorize()
    {
        return true;
    }

    public function rules()
    {
        return [
            'title' => 'required|min:3',
            'email' => 'required|email',
            'password' => 'required|min:6',
        ];
    }

    public function messages()
    {
        return [
            'title.required' => 'Câmpul "Titlu" este obligatoriu.',
            'email.email' => 'Câmpul "Email" trebuie să fie o adresă validă.',
        ];
    }
}
```

### Utilizarea clasei Request în controler

Pentru a utiliza clasa `Form Request` într-un controler, înlocuiți obiectul `Request` cu noua clasă:

```php
public function store(CreateTaskRequest $request)
{
    $validatedData = $request->validated();
}
```

Metoda `validated()` returnează toate datele validate, eliminând necesitatea unor verificări suplimentare.

## Afișarea erorilor în Blade

Laravel gestionează automat afișarea mesajelor de eroare generate de validare. Acestea pot fi afișate în șabloanele Blade utilizând variabila globală `$errors`.

### Afișarea tuturor mesajelor de eroare

```blade
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

### Afișarea erorii pentru un câmp specific

```blade
<div>
    <label for="name">Name</label>
    <input type="text" name="name" id="name" value="{{ old('name') }}">
    @error('name')
        <div class="text-danger">{{ $message }}</div>
    @enderror
</div>
```

Funcția `@error` verifică existența unei erori pentru câmpul specificat și afișează mesajul corespunzător.

De asemenea, funcția `old()` poate fi utilizată pentru a păstra valorile introduse de utilizator după o eroare:

```blade
<input type="text" name="email" value="{{ old('email') }}">
```

## Validarea datelor relaționate (ORM)

Adesea este necesar să verificați nu doar câmpuri individuale, ci și relații între date. De exemplu, pentru a verifica dacă un `category_id` există în baza de date, puteți utiliza regula `exists`:

```php
public function rules()
{
    return [
        'category_id' => 'required|exists:categories,id',
    ];
}
```

Laravel efectuează un interogare SQL pentru a verifica existența valorii respective în baza de date:

```sql
SELECT COUNT(*) FROM categories WHERE id = :value;
```

Dacă valoarea nu este găsită, validarea eșuează.

### `unique`

Regula `unique` verifică unicitatea unei valori într-o tabelă. De exemplu, pentru a verifica unicitatea unui email:

```php
public function rules()
{
    return [
        'email' => 'required|email|unique:users,email',
    ];
}
```

Această regulă se asigură că valoarea pentru `email` nu este deja utilizată în tabelul `users`.

## Concluzie

Validarea datelor în Laravel este esențială pentru crearea unor aplicații sigure și robuste. Utilizând regulile predefinite, clasele `Form Request` și funcționalitățile integrate, puteți implementa validări complexe într-un mod eficient și ușor de întreținut. Aceasta nu doar protejează aplicația împotriva erorilor și vulnerabilităților, ci și îmbunătățește experiența utilizatorului.