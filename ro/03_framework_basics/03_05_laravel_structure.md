# Structura unui proiect Laravel

Înainte de a începe să învățați Laravel, este important să înțelegeți structura proiectului.  
Laravel oferă o structură convenabilă pentru organizarea fișierelor și folderelor.  
Toate fișierele și folderele din Laravel au un scop bine definit și sunt organizate conform principiilor **MVC** (Model-View-Controller).

> [!NOTE]  
> Structura standard a unui proiect nu este obligatorie, dar este recomandată să fie utilizată. Pe măsură ce proiectul crește, structura standard poate deveni mai dificil de gestionat și poate necesita ajustări.

## Structura proiectului

Structura unui proiect Laravel arată astfel [^1]:

```
project/
    app/
        Console/
        Exceptions/
        Http/
            Controllers/
            Middleware/
            ...
        Providers/
    bootstrap/
        app.php
    config/
    database/
        factories/
        migrations/
        seeds/
    public/
        css/
        img/
        js/
        index.php
    resources/
        lang/
        views/
    routes/
        api.php
        channels.php
        console.php
        web.php
    storage/
        app/
        framework/
            cache/
            sessions/
            testing/
            views/
        logs/
    tests/
    vendor/
    .env
    .env.example
    composer.json
    composer.lock
    package.json
    phpunit.xml
```

### Descrierea structurii

- `app/` — conține codul principal al aplicației.
  - `Console/` — comenzile proprii `artisan`.
  - `Exceptions/` — conține gestionarii excepțiilor.
  - `Http/` — conține logica de procesare a cererilor HTTP.
    - `Controllers/` — conține controllerele.
    - `Middleware/` — conține middleware-uri.
- `bootstrap/` — conține fișierele care încarcă și configurează aplicația.
  - `app.php` — încarcă framework-ul și îl configurează.
- `config/` — conține fișierele de configurare.
- `database/` — conține fișierele pentru gestionarea bazei de date (se poate stoca și baza de date SQLite).
  - `factories/`
  - `migrations/`
  - `seeds/`
- `public/` — directorul public unde sunt plasate fișierele statice.
  - `css/` — stiluri.
  - `js/` — scripturi.
  - `index.php` — punctul de intrare al aplicației (fișierul principal).
- `resources/` — conține resurse, cum ar fi șabloanele și fișierele de localizare.
  - `lang/` — fișiere de localizare.
  - `views/` — fișiere șablon.
- `routes/` — conține fișierele de rutare.
  - `api.php`
  - `channels.php`
  - `console.php`
  - `web.php` — rutele pentru interfața web.
- `storage/` — conține fișierele generate în timpul execuției aplicației.
  - `app/` — fișiere generate în timpul funcționării aplicației.
  - `framework/` — fișiere specifice framework-ului.
    - `cache/`
    - `...`
    - `logs/` — jurnalele de sistem.
- `tests/` — testele aplicației.
- `vendor/` — dependențele proiectului.
- `.env` — fișierul cu variabilele de mediu.
- `.env.example` — exemplu de fișier cu variabile de mediu.
- `composer.json` — fișierul de configurare Composer.
- `composer.lock` — fișier cu dependențele Composer.

[^1]: Structura proiectului Laravel, laravel.com [online]. URL: https://laravel.com/docs/10.x/structure