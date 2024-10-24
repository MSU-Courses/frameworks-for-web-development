# Фабрики и Сидеры в Laravel

Когда мы начинаем разработку и тестирование приложения, часто возникает необходимость создавать тестовые данные для проверки его работы. Заполнять базу данных вручную не всегда удобно и эффективно, поэтому Laravel предоставляет два полезных инструмента для автоматизации этого процесса: фабрики и сидеры.

Обычно эти инструменты не используются в продакшене, но они крайне полезны на этапах **разработки** и **тестирования**.

## Фабрики

**Фабрики** – это классы, которые автоматически создают тестовые данные для моделей.

Можно провести аналогию: фабрика в реальном мире производит продукцию, а в Laravel фабрика создаёт фейковые данные для моделей.

Например, фабрика для постов будет генерировать тестовые данные для модели Post.

### Создание фабрики

Для создания фабрики используется команда `make:factory`:

```bash
php artisan make:factory PostFactory
```

Фабрику можно создать вместе с моделью (*более удобно, если модель ещё не существует*):

```bash
# -f создаст фабрику одновременно с моделью
php artisan make:model Post -f
```

### Определение фабрики

После автоматического создания фабрики, в файле `database/factories/PostFactory.php` появится следующий код:

```php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

/**
 * @extends \Illuminate\Database\Eloquent\Factories\Factory<\App\Models\Post>
 */
class PostFactory extends Factory
{
    /**
     * Define the model's default state.
     *
     * @return array<string, mixed>
     */
    public function definition(): array
    {
        return [
            //
        ];
    }
}
```

Теперь нужно определить данные, которые будет создавать фабрика. Для этого в методе `definition` возвращается массив с данными:

```php
public function definition(): array
{
    return [
        'title' => $this->faker->sentence(),
        'content' => $this->faker->paragraph(),
        'thumbnail' => $this->faker->imageUrl(),
    ];
}
```

Как видно, для генерации данных используется свойство `$this->faker`. Оно предоставляет доступ к генератору фейковых данных.

### Faker

**Faker** – это библиотека для генерации фейковых данных. Она позволяет создавать разнообразные типы данных: строки, числа, даты, адреса и многое другое.

Примеры использования:

```php
$this->faker->sentence(); // Генерация случайного предложения
$this->faker->paragraph(); // Генерация абзаца текста
$this->faker->imageUrl(); // Генерация URL изображения
$this->faker->name(); // Генерация имени
$this->faker->text(200); // Генерация текста длиной 200 символов
$this->faker->numberBetween(1, 100); // Генерация числа от 1 до 100
$this->faker->dateTime(); // Генерация случайной даты
```

Фабрики облегчают создание больших объёмов данных, что ускоряет процесс тестирования приложения.

### Использование фабрики

Для создания одной или нескольких моделей с помощью фабрики используется метод `create`:

```php
// Создание 10 постов
$posts = Post::factory(10)->create();
```

Кроме того, фабрику можно использовать напрямую, без обращения к модели:

```php
// Создание одного поста
$post = PostFactory::new()->create();

// Создание нескольких постов
$posts = PostFactory::new()->count(10)->create();
```

Этот подход позволяет гибко управлять процессом генерации данных, создавая нужное количество экземпляров модели для тестирования.

## Сидеры

**Сидеры** – это классы, предназначенные для заполнения базы данных тестовыми данными.

### Разница между фабриками и сидерами

Фабрики создают тестовые данные для моделей, а сидеры заполняют этими данными базу данных. Фабрики генерируют данные, а сидеры используют их для массового наполнения базы данных.

### Использование сидеров

Когда в проекте мало моделей, уже созданный сидер `DatabaseSeeder.php` может быть использован для вызова фабрик и заполнения базы данных. Этот файл находится в директории `database/seeders` и содержит метод `run`, в котором можно вызвать фабрики для создания тестовых данных.

Пример использования сидера:

```php
<?php

namespace Database\Seeders;

use App\Models\Post;
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Заполнение базы данных.
     */
    public function run(): void
    {
        // Пример использования фабрики для создания 10 постов
        Post::factory(10)->create();
    }
}
```

Этот код добавит 10 постов в базу данных, используя фабрику для модели Post. Таким образом, сидеры обеспечивают простой способ массового наполнения базы данных для тестирования или первоначальной настройки проекта.

[^1]: Database: Seeding, laravel.com [online]. URL: https://laravel.com/docs/11.x/seeding