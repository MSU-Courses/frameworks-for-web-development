# Структура проекта Laravel

Перед изучением Laravel необходимо понимать структуру проекта. 
Laravel предоставляет удобную структуру для организации файлов и папок.
Все файлы и папки в Laravel имеют своё назначение и расположены в соответствии с принципами **MVC**.

> [!NOTE]
> Стандартная структура проекта не является строго обязательной, но настоятельно рекомендуется к использованию. По мере роста проекта стандартная структура может становиться всё более сложной для поддержки и может потребовать изменений.

## Структура проекта

Структура проекта Laravel выглядит следующим образом [^1] :

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

### Описание структуры

- `app/` — содержит основной код приложения.
  - `Console/` — собственные команды `artisan`
  - `Exceptions/` — содержит обработчики исключений.
  - `Http/` — содержит логику обработки HTTP-запросов.
    - `Controllers/` — содержит контроллеры.
    - `Middleware/` — содержит мидлвейры.
- `bootstrap/` — содержит файлы, которые загружают приложение и настраивают его.
  - `app.php` — загружает фреймворк и настраивает его.
- `config/` — содержит файлы конфигурации.
- `database/` — содержит файлы для работы с базой данных (можно хранить и файл SQLite базы данных).
  - `factories/` 
  - `migrations/`
  - `seeds/`
- `public/` — публичная директория, где располагаются статические файлы.
  - `css/` — стили.
  - `js/` — скрипты.
  - `index.php` — точка входа в приложение (главный файл).
- `resources/` — содержит ресурсы, такие как шаблоны, локализация и т.д.
  - `lang/` — файлы локализации.
  - `views/` — файлы шаблонов.
- `routes/` — содержит файлы маршрутизации.
  - `api.php`
  - `channels.php`
  - `console.php` 
  - `web.php` — маршруты для веб-интерфейса.
- `storage/` — содержит файлы, которые генерируются во время работы приложения.
  - `app/` — файлы, которые генерируются во время работы приложения.
  - `framework/` — файлы фреймворка.
    - `cache/`
    - `...`
    - `logs/` — логи.
- `tests/` — тесты.
- `vendor/` — зависимости.
- `.env` — файл с переменными окружения.
- `.env.example` — пример файла с переменными окружения.
- `composer.json` — файл настроек Composer.
- `composer.lock` — файл с зависимостями Composer.

[^1]: Laravel project structure, laravel.com [online] Available at: https://laravel.com/docs/10.x/structure