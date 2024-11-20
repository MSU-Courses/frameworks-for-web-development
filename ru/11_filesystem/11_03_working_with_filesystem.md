# Работа с файловой системой в Laravel

В Laravel работа с файлами организована через удобный и мощный API, предоставляемый классом `Storage`. Этот API упрощает взаимодействие с файловой системой и позволяет разработчикам абстрагироваться от низкоуровневых операций. С помощью `Storage` можно работать с локальными файлами, а также с облачными хранилищами, такими как Amazon S3 или Google Cloud Storage, без изменения кода приложения.

Основная особенность файловой системы Laravel заключается в использовании **драйверов**, которые определяют, где и как хранятся файлы. **Например**, можно использовать локальную файловую систему, диск в сети или облачное хранилище. Для работы с файлами Laravel предоставляет методы, такие как put, get, delete, exists и многие другие.

Пример использования `Storage` для сохранения файла:

```php
use Illuminate\Support\Facades\Storage;

// Сохранение файла
Storage::disk('local')->put('example.txt', 'Это содержимое файла.');

// Чтение файла
$content = Storage::disk('local')->get('example.txt');
echo $content;
```

## Основные возможности Storage

### Сохранение файлов

Для сохранения файла используется метод `put`, который принимает путь к файлу и его содержимое. По умолчанию файлы сохраняются в директории `storage/app`.

```php
use Illuminate\Support\Facades\Storage;

// Сохранение файла
Storage::put('file.txt', 'Содержимое файла');
```

### Чтение файлов

Для чтения файла используется метод `get`, который возвращает содержимое файла в виде строки.

```php
use Illuminate\Support\Facades\Storage;

// Чтение файла
$content = Storage::get('file.txt');
```

### Проверка существования

Для проверки существования файла используется метод `exists`, который возвращает `true`, если файл существует, и `false` в противном случае.

```php
use Illuminate\Support\Facades\Storage;

// Проверка существования файла
if (Storage::exists('file.txt')) {
    echo 'Файл существует';
} else {
    echo 'Файл не найден';
}
```

### Удаление файлов

Для удаления файла используется метод `delete`.

```php
use Illuminate\Support\Facades\Storage;

// Удаление файла
Storage::delete('file.txt');
```

### Скачивание файлов

Скачивание файлов — это процесс передачи файла из хранилища на клиентский компьютер. Для этого можно использовать метод `download`.

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

### Создание публичного URL

**Публичный URL** — это URL-адрес, по которому можно получить доступ к файлу из веб-браузера. Для создания публичного URL используется метод `url`.

```php
use Illuminate\Support\Facades\Storage;

// Создание публичного URL
// Если файл находится в директории storage/app/public, то URL будет /storage/file.txt
$url = Storage::url('file.txt');
```

### Работа с директориями

Для работы с директориями используются методы `makeDirectory`, `deleteDirectory`, `directories` и `allDirectories`.

```php
use Illuminate\Support\Facades\Storage;

// Создание директории
Storage::makeDirectory('public/images');

// Удаление директории
Storage::deleteDirectory('public/images');

// Получение списка директорий
$directories = Storage::directories('public');

// Получение всех директорий
$allDirectories = Storage::allDirectories('public');
```

## Загрузка файлов с формы

**Загрузка файлов с пользовательской формы** — это частая задача в веб-разработке. Laravel делает этот процесс простым и безопасным.

### Создание формы для загрузки файла

Для загрузки файла с формы необходимо создать HTML-форму с атрибутом `enctype="multipart/form-data"` и полем `input type="file"`.

```html
<form action="/upload" method="post" enctype="multipart/form-data">
  @csrf
  <input type="file" name="file" />
  <button type="submit">Загрузить</button>
</form>
```

### Обработка загруженного файла

Для обработки загруженного файла в Laravel используется метод `store` класса `Request`. Этот метод сохраняет файл на сервере и возвращает путь к файлу.

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
        // Валидация файла
        $request->validate([
            'file' => 'required|file|mimes:jpg,png,pdf|max:2048',
        ]);

        // Сохранение файла и получение пути к файлу
        // Путь будет вида storage/app/public/uploads/filename.jpg
        $path = $request->file('file')->store('public/uploads');

        // Получение URL для доступа
        $url = Storage::url($path);
        
        return redirect('/')->with('success', 'Файл загружен: ' . $url);
    }
}
```

### Валидация загружаемого файла

Как можно видеть в примере выше, для валидации загружаемого файла используется метод `validate` класса `Request`. Валидация файла позволяет проверить его тип, размер, формат и другие параметры.

Используются следующие правила валидации для файла:

- `required` — обязательное поле
- `file` — файл
- `mimes:jpg,png,pdf` — разрешенные типы файлов
- `max:2048` — максимальный размер файла в килобайтах
- `min:2048` — минимальный размер файла в килобайтах
- `dimensions:min_width=100,min_height=200,max_width=1000,max_height=2000` — размеры изображения

### Отображение сообщений об ошибках

Для отображения сообщений об ошибках валидации можно использовать директиву `@error` в Blade-шаблоне.

```blade
@error('file')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

## Заключение

Работа с файловой системой в Laravel — это важная часть разработки веб-приложений. Благодаря классу `Storage` и удобным методам API, разработчики могут легко управлять файлами, сохранять и загружать их, а также обеспечивать безопасность и надежность приложения.