# Exemplu de utilizare a ORM-ului în Laravel

Să analizăm un exemplu de utilizare a Eloquent ORM în Laravel, în care vom crea o aplicație simplă pentru gestionarea postărilor de pe un blog.

### 1. Crearea migrațiilor pentru tabelul `posts`

Începem prin crearea unei migrații pentru tabelul `posts`, care va stoca postările de pe blog.

```php
// database/migrations/2021_09_01_000000_create_posts_table.php
public function up(): void
{
   Schema::create('posts', function (Blueprint $table) {
      $table->id();
      $table->string('title');
      $table->text('content')->nullable();
      $table->boolean('published')->default(false);
      $table->timestamps();
   });
}
```

### 2. Crearea modelului `Post`

Modelul `Post` va reprezenta tabelul `posts` în Laravel. De asemenea, folosim proprietatea `$fillable` pentru a defini câmpurile care pot fi completate în mod automat.

```php
// app/Models/Post.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    use HasFactory;

    protected $fillable = [
        'title',
        'content',
    ];
}
```

### 3. Crearea controllerului `PostController`

Controllerul `PostController` va gestiona toate operațiunile legate de postări: afișarea, crearea și stocarea datelor în baza de date.

```php
// app/Http/Controllers/PostController.php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::all();

        return view('post.index', compact('posts'));
    }

    public function show(int $id)
    {
        $post = Post::findOrFail($id);

        return view('post.show', compact('post'));
    }

    public function create()
    {
        return view('post.create');
    }

    public function store(Request $request)
    {
        Post::create([
            'title' => $request->title,
            'content' => $request->content,
        ]);

        // Sau
        // Post::create($request->only(['title', 'content']));

        return redirect()->route('post.index');
    }
}
```

### 4. Definirea rutelor pentru `PostController`

Folosim metoda `resource` pentru a defini rutele pentru operațiunile de bază: listare, afișare, creare și stocare a postărilor.

```php
// routes/web.php
Route::resource('post', PostController::class)
    ->only(['index', 'show', 'create', 'store']);
```

### 5. Crearea unei vederi pentru listarea postărilor (`index.blade.php`)

Aceasta este vederea pentru afișarea unei liste cu toate postările existente.

```blade
{{-- resources/views/post/index.blade.php --}}
@extends('layouts.app')

@section('content')
    <h1>Lista Postărilor</h1>
    <div>
        <ul>
            @forelse ($posts as $post)
                <li>
                    <a href="{{ route('post.show', ['post' => $post->id]) }}">
                        {{ $post->title }}
                    </a>
                </li>
            @empty
                  <li>Nu există postări</li>
            @endforelse
        </ul>
    </div>
@endsection
```

### 6. Crearea unei vederi pentru afișarea unei postări (`show.blade.php`)

Această vedere va afișa detaliile unei postări specifice.

```blade
{{-- resources/views/post/show.blade.php --}}
@extends('layouts.app')

@section('content')
    <div>
        <h1>{{ $post->title }}</h1>
        <p>{{ $post->content }}</p>
    </div>
@endsection
```

### 7. Crearea unei vederi pentru crearea unei postări (`create.blade.php`)

Această vedere va afișa un formular pentru crearea unei noi postări.

```blade
{{-- resources/views/post/create.blade.php --}}
@extends('layouts.app')

@section('content')
    <div>
        <form action="{{ route('post.store') }}" method="POST">
            @csrf
            <div>
                <label for="title">Titlu</label>
                <input type="text" name="title" id="title">
            </div>
            <div>
                <label for="content">Conținut</label>
                <textarea name="content" id="content" cols="30" rows="10"></textarea>
            </div>
            <button type="submit">Crează Postare</button>
        </form>
    </div>
@endsection
```

## Concluzie

Acesta este un exemplu simplu de utilizare a Eloquent ORM în Laravel. Am parcurs pașii de la migrarea bazei de date și crearea modelului `Post`, până la scrierea unui controller și a vederilor pentru gestionarea postărilor de pe blog.

Pe viitor, puteți extinde acest exemplu adăugând validări, autentificare sau funcții mai complexe cum ar fi relațiile între modele.