## Afișarea datelor din sesiune în șabloanele Blade

În șabloanele Blade, poți afișa date din sesiune care au fost setate în controller. Pentru aceasta, se folosește funcția `session()`.

```php
<!DOCTYPE html>
<html>
<head>
    <title>Profil utilizator</title>
</head>
<body>
    <h1>Profil utilizator</h1>
    <p>Nume: {{ session('name') }}</p>
    <p>Prenume: {{ session('surname') }}</p>
    <p>Vârstă: {{ session('age') }}</p>
</body>
</html>
```

În acest exemplu, folosim funcția `session()` pentru a obține datele stocate în sesiune. Aceste date sunt setate în controller cu ajutorul metodei `put()`.

```php
public function showProfile()
{
    session()->put('name', 'Ivan');
    session()->put('surname', 'Ivanov');
    session()->put('age', 25);

    return view('profile');
}
```

Deși sesiunile sunt utilizate mai rar decât variabilele transmise prin metoda `view()`. Ele sunt utile, de exemplu, pentru transmiterea mesajelor de tip flash.

## Mesaje flash

**Mesajele flash** sunt mesaje care sunt afișate utilizatorului o singură dată după o redirecționare. Acestea sunt utilizate în mod obișnuit pentru notificări despre rezultatul unor operațiuni, cum ar fi actualizarea cu succes a profilului sau apariția unor erori.

Pentru a seta un mesaj flash, se folosește metoda `flash()`.

```php
public function updateProfile(Request $request)
{
    session()->flash('success', 'Profilul a fost actualizat cu succes!');

    return view('profile');
}

// Sau la redirecționare cu metoda redirect()

public function updateProfile(Request $request)
{
    return redirect()->route('profile')->with('success', 'Profilul a fost actualizat cu succes!');
}
```

Pentru a afișa un mesaj flash în șablonul Blade, poți folosi o verificare cu funcția `session()`.

```blade
@if (session('success'))
    <div class="alert alert-success">
        {{ session('success') }}
    </div>
@endif
```

În acest exemplu, verificăm dacă există un mesaj flash, iar dacă este setat, acesta este afișat în șablon. După reîncărcarea paginii, mesajul va dispărea.