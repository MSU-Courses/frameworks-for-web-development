# Lucrul cu sistemul de fișiere în Laravel

În Laravel, manipularea fișierelor este gestionată printr-un API puternic și intuitiv oferit de clasa `Storage`. Acest API facilitează interacțiunea cu sistemul de fișiere și permite dezvoltatorilor să evite detaliile operațiunilor de nivel scăzut. Prin `Storage`, puteți lucra atât cu fișiere locale, cât și cu stocări cloud, cum ar fi Amazon S3 sau Google Cloud Storage, fără a modifica codul aplicației.

O caracteristică esențială a sistemului de fișiere Laravel este utilizarea **driverelor**, care definesc unde și cum sunt stocate fișierele. **De exemplu**, puteți folosi sistemul de fișiere local, un disc de rețea sau un serviciu de stocare cloud. Laravel oferă metode precum `put`, `get`, `delete`, `exists` și multe altele pentru gestionarea fișierelor.

## Exemplu de utilizare a `Storage` pentru salvarea unui fișier

```php
use Illuminate\Support\Facades\Storage;

// Salvarea unui fișier
Storage::disk('local')->put('example.txt', 'Acesta este conținutul fișierului.');

// Citirea unui fișier
$content = Storage::disk('local')->get('example.txt');
echo $content;
```

## Funcționalități principale ale `Storage`

### Salvarea fișierelor

Pentru a salva un fișier, folosiți metoda `put`, care primește ca parametri calea fișierului și conținutul acestuia. Implicit, fișierele sunt salvate în directorul `storage/app`.

```php
use Illuminate\Support\Facades\Storage;

// Salvarea fișierului
Storage::put('file.txt', 'Conținutul fișierului');
```

### Citirea fișierelor

Pentru a citi un fișier, utilizați metoda `get`, care returnează conținutul fișierului sub formă de șir de caractere.

```php
use Illuminate\Support\Facades\Storage;

// Citirea fișierului
$content = Storage::get('file.txt');
```

### Verificarea existenței fișierului

Pentru a verifica dacă un fișier există, folosiți metoda `exists`. Aceasta returnează `true` dacă fișierul există și `false` în caz contrar.

```php
use Illuminate\Support\Facades\Storage;

// Verificarea existenței fișierului
if (Storage::exists('file.txt')) {
    echo 'Fișierul există';
} else {
    echo 'Fișierul nu a fost găsit';
}
```

### Ștergerea fișierelor

Pentru a șterge un fișier, utilizați metoda `delete`.

```php
use Illuminate\Support\Facades\Storage;

// Ștergerea fișierului
Storage::delete('file.txt');
```

### Descărcarea fișierelor

Descărcarea fișierelor constă în transferul fișierului din stocare pe computerul clientului. Pentru aceasta, se utilizează metoda `download`.

```php
use Illuminate\Support\Facades\Storage;

class FileController extends Controller
{
    public function download()
    {
        return Storage::download('file.txt');
    }
}
```

### Crearea unui URL public

Un **URL public** este un link accesibil prin browser pentru a obține fișierul. Laravel oferă metoda `url` pentru generarea acestuia.

```php
use Illuminate\Support\Facades\Storage;

// Crearea unui URL public
// Dacă fișierul este în storage/app/public, URL-ul va fi /storage/file.txt
$url = Storage::url('file.txt');
```

### Lucrul cu directoare

Pentru gestionarea directoarelor, Laravel oferă metode precum `makeDirectory`, `deleteDirectory`, `directories` și `allDirectories`.

```php
use Illuminate\Support\Facades\Storage;

// Crearea unui director
Storage::makeDirectory('public/images');

// Ștergerea unui director
Storage::deleteDirectory('public/images');

// Obținerea listelor de directoare
$directories = Storage::directories('public');

// Obținerea tuturor directoarelor
$allDirectories = Storage::allDirectories('public');
```

## Încărcarea fișierelor printr-un formular

Încărcarea fișierelor prin formulare este o sarcină frecventă în dezvoltarea web. Laravel face acest proces simplu și sigur.

### Crearea unui formular pentru încărcarea fișierelor

Formularul HTML pentru încărcarea fișierelor trebuie să conțină atributul `enctype="multipart/form-data"` și un câmp `input type="file"`.

```html
<form action="/upload" method="post" enctype="multipart/form-data">
  @csrf
  <input type="file" name="file" />
  <button type="submit">Încărcați</button>
</form>
```

### Gestionarea fișierelor încărcate

Pentru procesarea fișierelor încărcate, Laravel oferă metoda `store` din clasa `Request`. Aceasta salvează fișierul pe server și returnează calea acestuia.

```php
// routes/web.php

use App\Http\Controllers\FileController;

Route::post('/upload', [FileController::class, 'upload']);
```

```php
// app/Http/Controllers/FileController.php

use Illuminate\Http\Request;

class FileController extends Controller
{
    public function upload(Request $request)
    {
        // Validarea fișierului
        $request->validate([
            'file' => 'required|file|mimes:jpg,png,pdf|max:2048',
        ]);

        // Salvarea fișierului și obținerea căii acestuia
        // Calea va fi storage/app/public/uploads/filename.jpg
        $path = $request->file('file')->store('public/uploads');

        // Obținerea URL-ului public
        $url = Storage::url($path);
        
        return redirect('/')->with('success', 'Fișier încărcat: ' . $url);
    }
}
```

### Validarea fișierelor încărcate

Pentru validarea fișierelor, Laravel utilizează metoda `validate` din clasa `Request`. Validarea permite verificarea tipului, dimensiunii și altor caracteristici ale fișierului.

Reguli de validare:

- `required` — câmp obligatoriu  
- `file` — trebuie să fie un fișier  
- `mimes:jpg,png,pdf` — tipuri de fișiere acceptate  
- `max:2048` — dimensiune maximă în kilobytes  
- `dimensions:min_width=100,min_height=200,max_width=1000,max_height=2000` — dimensiuni ale imaginilor  

### Afișarea erorilor de validare

Pentru afișarea mesajelor de eroare, se utilizează directiva `@error` în șabloanele Blade.

```blade
@error('file')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```


## Concluzie

Gestionarea sistemului de fișiere în Laravel este o componentă esențială a dezvoltării aplicațiilor web. Cu ajutorul clasei `Storage` și a API-ului intuitiv, dezvoltatorii pot manipula fișiere cu ușurință, asigurând în același timp securitatea și fiabilitatea aplicației.