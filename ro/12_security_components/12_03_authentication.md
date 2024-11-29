# Autentificarea în Laravel

> [!NOTE]  
> **Autentificarea** este procesul de verificare a identității utilizatorului, care oferă acces la resursele sau funcționalitățile unei aplicații web. Autentificarea răspunde la întrebarea: „_Cine ești?_”.

Laravel oferă instrumente gata de utilizare prin biblioteca **Laravel Breeze** sau o soluție mai complexă, **Laravel Jetstream**. Aceste biblioteci simplifică implementarea autentificării, însă dezvoltatorul poate configura procesul manual dacă este necesară o personalizare completă.

În acest capitol, vom analiza configurarea manuală a autentificării în Laravel, utilizând **Eloquent** pentru gestionarea utilizatorilor și **Blade** pentru afișarea vizualizărilor.

## Modelul și migrarea utilizatorului

Laravel include implicit modelul **User**, utilizat pentru gestionarea conturilor de utilizator. Acest model este conectat la baza de date prin tabelul **users**.

**Atribute principale ale modelului**:

- `name` — numele utilizatorului.  
- `email` — adresa de email, care trebuie să fie unică.  
- `password` — parola criptată.  
- `remember_token` — token pentru funcția „Ține-mă minte”.  

Migrarea pentru tabelul utilizatorilor este creată automat și arată astfel:

```php
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique();
    $table->string('password');
    $table->rememberToken();
    $table->timestamps();
});
```

> [!NOTE]  
> Unde este definită structura? Tabelul `users` este creat printr-o migrare aflată în folderul `database/migrations`. Dacă este necesară modificarea structurii, deschideți fișierul de migrare corespunzător și efectuați ajustările dorite.

La prima rulare a migrațiilor, se creează tabelul **users** cu câmpurile menționate.

## Înregistrarea utilizatorului

Înregistrarea utilizatorului presupune crearea unui nou cont cu un email și o parolă unice. Pentru aceasta, trebuie să creați un formular pentru introducerea datelor și să procesați aceste date într-un controller.

**Pași pentru înregistrare**:

1. **Crearea formularului**. Formularul include câmpuri pentru nume, email și parolă.  
2. **Procesarea datelor**. Datele din formular sunt trimise către server pentru a crea un cont nou.  
3. **Criptarea parolei**. Parola este criptată înainte de salvarea în baza de date.  
4. **Salvarea utilizatorului**. Contul nou este stocat în baza de date.  
5. **Autentificarea**. După înregistrare, utilizatorul este autentificat automat.  
6. **Redirecționarea**. Utilizatorul este direcționat către o pagină protejată.  

### Crearea formularului

Formularul pentru înregistrarea unui utilizator poate fi creat utilizând **Blade**. Exemplu de formular:

```html
<form method="POST" action="{{ route('register') }}">
  @csrf

  <label for="name">Name</label>
  <input id="name" type="text" name="name" required autofocus />

  <label for="email">Email</label>
  <input id="email" type="email" name="email" required />

  <label for="password">Password</label>
  <input id="password" type="password" name="password" required />

  <label for="password_confirmation">Confirm Password</label>
  <input
    id="password_confirmation"
    type="password"
    name="password_confirmation"
    required
  />

  <button type="submit">Register</button>
</form>
```

> [!TIP]  
> Câmpul `password_confirmation` a fost adăugat pentru confirmarea parolei. Aceasta este o practică obișnuită pentru a preveni erorile la introducerea parolei.

### Procesarea datelor pentru înregistrare

Pentru procesarea datelor, se creează un controller care conține o metodă pentru înregistrarea utilizatorilor. **Exemplu de controller**:

```php
// AuthController.php
use App\Models\User;

public function register(Request $request)
{
    // Validarea datelor
    $request->validate([
        'name' => 'required|string|max:255',
        'email' => 'required|string|email|max:255|unique:users',
        'password' => 'required|string|min:8|confirmed',
    ]);

    // Crearea unui utilizator nou
    $user = User::query()->create([
        'name' => $request->name,
        'email' => $request->email,
        'password' => Hash::make($request->password),
    ]);

    // Autentificarea utilizatorului
    auth()->login($user);

    // Redirecționare către o pagină protejată
    return redirect('/dashboard');
}
```

În această metodă, utilizăm **2 funcții noi**:

- `Hash::make($request->password)` — criptează parola utilizatorului înainte de salvarea în baza de date. Implicit, Laravel utilizează algoritmul **bcrypt** pentru criptare.  
- `auth()->login($user)` — autentifică utilizatorul în aplicație. Vom analiza detaliat procesul de autentificare ulterior.

## Autentificarea utilizatorului

Autentificarea utilizatorului presupune verificarea datelor de identificare (email și parolă) și oferirea accesului la resursele protejate. Pentru aceasta, trebuie să creați un formular pentru login și să procesați datele într-un controller.

**Pași pentru autentificare**:

1. **Crearea formularului**. Formularul include câmpuri pentru email și parolă.  
2. **Procesarea datelor**. Datele din formular sunt trimise către server pentru verificare.  
3. **Validarea datelor**. Se verifică dacă există un utilizator cu emailul introdus și dacă parola corespunde.  
4. **Autentificarea**. După validare, utilizatorul este autentificat.  
5. **Redirecționarea**. Utilizatorul este direcționat către o pagină protejată.  

### Crearea formularului

Formularul pentru autentificarea utilizatorului poate fi creat utilizând **Blade**. Exemplu de formular:

```html
<form method="POST" action="{{ route('login') }}">
  @csrf

  <label for="email">Email</label>
  <input id="email" type="email" name="email" required autofocus />

  <label for="password">Password</label>
  <input id="password" type="password" name="password" required />

  <button type="submit">Login</button>
</form>
```

### Procesarea datelor pentru autentificare

Pentru procesarea datelor, se creează un controller care conține o metodă pentru autentificare. **Exemplu de controller**:

```php
// AuthController.php

public function login(Request $request)
{
    // Validarea datelor
    $request->validate([
        'email' => 'required|string|email',
        'password' => 'required|string',
    ]);

    // Verificarea datelor utilizatorului
    if (auth()->attempt($request->only('email', 'password'))) {
        // Regenerarea sesiunii pentru prevenirea atacurilor
        $request->session()->regenerate();

        // Autentificarea utilizatorului
        return redirect('/dashboard');
    }

    // Eroare de autentificare
    return back()->withErrors([
        'email' => 'Datele furnizate nu corespund înregistrărilor noastre.',
    ]);
}
```

În această metodă, utilizăm **2 funcții noi**:

- `auth()->attempt($credentials)` — verifică datele de identificare ale utilizatorului. Dacă sunt corecte, utilizatorul este autentificat, iar metoda returnează `true`, altfel `false`.  
- `back()->withErrors($errors)` — redirecționează utilizatorul către pagina anterioară, împreună cu o eroare de autentificare. În acest caz, este afișat un mesaj de eroare.  

## Ieșirea utilizatorului din sistem

Ieșirea utilizatorului din sistem implică terminarea sesiunii de autentificare și redirecționarea către pagina de login. Pentru aceasta, trebuie creat un link sau un buton pentru deconectare și gestionat corespunzător într-un controller.

**Pașii pentru ieșirea din sistem**:

1. **Crearea unui link**. Un link sau un buton pentru deconectarea utilizatorului.  
2. **Procesarea datelor**. Datele sunt trimise către server pentru terminarea sesiunii.  
3. **Terminarea sesiunii**. Sesiunea utilizatorului autentificat este încheiată.  
4. **Redirecționarea**. Utilizatorul este redirecționat către pagina de login.

> [!NOTE]  
> Este o practică bună să se folosească metoda `DELETE` pentru ieșirea utilizatorului din sistem.

### Crearea unui link

Linkul pentru deconectare poate fi creat utilizând **Blade**. Exemplu de link:

```html
<form method="POST" action="{{ route('logout') }}">
  @csrf @method('DELETE')
  <button type="submit">Logout</button>
</form>
```

### Procesarea datelor pentru ieșire

Pentru gestionarea ieșirii din sistem, se creează un controller care conține metoda pentru deconectarea utilizatorului. **Exemplu de controller**:

```php
// AuthController.php

public function logout(Request $request)
{
    // Terminarea sesiunii de autentificare
    auth()->logout();

    // Invalidarea sesiunii pentru prevenirea atacurilor
    request()->session()->invalidate();

    // Regenerarea tokenului sesiunii
    request()->session()->regenerateToken();

    // Redirecționarea către pagina de login
    return redirect('/login');
}
```

## Sesiuni și autentificare

În capitolul anterior, am discutat procesul de autentificare a utilizatorului în Laravel, care implică adesea utilizarea sesiunilor pentru a stoca date despre utilizator și starea acestuia. Pentru a înțelege mai bine mecanismul, să explorăm modul în care Laravel gestionează sesiunile.

### Sesiuni

**Cum funcționează sesiunile în Laravel**:

1. **Crearea sesiunii:** La prima cerere a utilizatorului, serverul Laravel creează un identificator unic de sesiune, care este stocat în cookie-urile browserului.  
2. **Stocarea datelor:** Informațiile despre utilizator și starea acestuia sunt stocate pe server, implicit în fișierele din directorul `storage/framework/sessions`.  
3. **Utilizarea datelor:** Datele din sesiune sunt disponibile pe toată durata sesiunii utilizatorului pe site.  
4. **Ștergerea sesiunii:** La ieșirea din sistem sau la încheierea sesiunii, datele sunt șterse.  

### Autentificarea cu ajutorul sesiunilor

Sesiunile joacă un rol central în procesul de autentificare, permițând aplicației să „își amintească” utilizatorul autentificat între cereri.

**Procesul de autentificare folosind sesiuni**:

1. **Login:** Utilizatorul introduce datele de autentificare (email și parolă) și le trimite serverului.  
2. **Validarea datelor:**  
   - Laravel folosește metoda `Auth::attempt()` pentru validarea datelor furnizate.  
   - Metoda `attempt()` primește un array cu câmpuri precum `email` și `password`.  
   - Framework-ul caută un utilizator în baza de date care corespunde emailului.  
   - Parola introdusă este comparată cu parola hashată stocată, utilizând funcția `Hash::check()`.  
3. **Inițializarea sesiunii:**  
   - Dacă datele sunt valide, Laravel generează un identificator unic de sesiune (Session ID).  
   - Acest identificator este stocat în browser sub forma unui cookie.  
4. **Stocarea datelor în sesiune:**  
   - Laravel salvează în sesiune informații despre utilizator, cum ar fi identificatorul unic (`user_id`).  
   - Aceste date permit identificarea utilizatorului la următoarele cereri.  

### Încărcarea utilizatorului la fiecare cerere

După autentificare, Laravel încarcă automat datele utilizatorului la fiecare cerere, ceea ce permite accesul la informațiile utilizatorului oriunde în aplicație. Astfel, utilizatorul nu trebuie să se autentifice din nou la fiecare accesare.

Procesul se desfășoară astfel:

1. **Obținerea ID-ului sesiunii:**  
   - Browserul trimite cookie-ul cu Session ID către server la fiecare cerere.  
   - Laravel preia acest ID și îl folosește pentru a localiza sesiunea.  
2. **Încărcarea datelor sesiunii:**  
   - Laravel utilizează Session ID pentru a extrage datele sesiunii din stocare (implicit, fișierele din `storage/framework/sessions`).  
3. **Identificarea utilizatorului:**  
   - Framework-ul obține `user_id` din datele sesiunii.  
4. **Încărcarea modelului utilizatorului:**  
   - Pe baza `user_id`, Laravel extrage utilizatorul corespunzător din tabelul `users` din baza de date.  
   - Modelul utilizatorului este disponibil prin clasa `Auth` sau prin `auth()->user()`.  

Acest proces asigură că aplicația are acces la datele actualizate ale utilizatorului și că poate gestiona drepturile de acces.

### Verificarea autentificării în Blade

În Laravel, verificarea autentificării utilizatorului se face cu ajutorul directivei `@auth` în șabloanele Blade. Aceasta permite afișarea unui conținut specific doar utilizatorilor autentificați.

**Exemplu de utilizare a directivei `@auth`:**

```blade
@auth
    <p>Bun venit, {{ auth()->user()->name }}!</p>
@endauth
```

Blocul de cod din interiorul directivei `@auth` va fi afișat numai utilizatorilor autentificați. Pentru utilizatorii neautentificați, se folosește directiva `@guest`:

```blade
@guest
    <p>Vă rugăm să <a href="{{ route('login') }}">autentificați-vă</a>.</p>
@endguest
```

Aceste directive oferă o modalitate simplă și eficientă de a gestiona conținutul în funcție de starea de autentificare a utilizatorului.

## Concluzie

Autentificarea este un proces esențial în dezvoltarea aplicațiilor web, care asigură securitatea datelor și controlul accesului. Laravel oferă instrumente puternice pentru implementarea autentificării, permițând dezvoltatorilor să creeze aplicații sigure și intuitive.

În acest capitol, am acoperit pașii principali pentru configurarea autentificării în Laravel, inclusiv crearea formularelor pentru înregistrare și login, gestionarea datelor în controller, autentificarea utilizatorului și procesul de deconectare. De asemenea, am explorat modul în care Laravel utilizează sesiunile pentru stocarea datelor utilizatorului și pentru gestionarea accesului.