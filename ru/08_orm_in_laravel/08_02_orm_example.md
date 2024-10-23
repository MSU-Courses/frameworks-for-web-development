# Пример использования ORM в Laravel

Рассмотрим пример использования Eloquent ORM в Laravel.

В качестве примера возьмём модель `Post`, которая представляет собой пост в блоге.

1. Создание миграций для таблицы `posts`:

```php
// database/migrations/2021_09_01_000000_create_posts_table.php
// ...
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

// ...
```


2. Создание модели `Post`.
   
```php
// app/models/Post.php
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

3. Создание контроллера `PostController`.

```php
// app/Cotrollers/PostController.php
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

        // или
        // Post::create($request->only(['title', 'content']));

        return redirect()->route('post.index');
    }
}
```

4. Создание маршрутов для контроллера `PostController` с использованием метода `resource`.

```php
// routes/web.php
Route::resource('post', PostController::class)
    ->only(['index', 'show', 'create', 'store']);
```

5. Создание представления для списка постов `index.blade.php`.

```blade
{{-- resources/views/post/index.blade.php --}}
@extends('layouts.app')

@section('content')
    <h1>Post List</h1>
    <div>
        <ul>
            @forelse ($posts as $post)
                <li>
                    <a href="{{ route('post.show', ['post' => $post->id]) }}">
                        {{ $post->title }}
                    </a>
                </li>
            @empty
                  <li>No post found</li>
            @endforelse
        </ul>
    </div>
@endsection
```

6. Создание представления для отображения поста `show.blade.php`.

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

7. Создание представления для создания поста `create.blade.php`.

```blade
{{-- resources/views/post/create.blade.php --}}

@extends('layouts.app')

@section('content')
    <div>
        <form action="{{ route('post.store') }}" method="POST">
            @csrf
            <div>
                <label for="title">Title</label>
                <input type="text" name="title" id="title">
            </div>
            <div>
                <label for="content">Content</label>
                <textarea name="content" id="content" cols="30" rows="10"></textarea>
            </div>
            <button type="submit">Create Post</button>
        </form>
    </div>
@endsection
```

## Заключение

В данном материале показан один из самых простых примеров использования Eloquent ORM в Laravel. В дальнейшем мы будем рассматривать более сложные примеры использования ORM в Laravel.