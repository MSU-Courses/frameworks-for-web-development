# Composer - менеджер зависимостей для PHP

Перед тем как начать изучение фреймворков, необходимо понять, что такое **Composer**.

Давайте представим ситуацию, что вы хотите использовать в своем проекте стороннюю библиотеку, например, для работы с базой данных. Скорее всего, многие разработчики уже написали такую библиотеку и вам не нужно писать ее с нуля. Вам просто нужно подключить эту библиотеку к своему проекту.

Вместо того чтобы качать библиотеку с GitHub, распаковывать архив и копировать файлы в свой проект, вы можете использовать пакетный менеджер (или менеджер зависимостей) **Composer** [^1].

> [!NOTE]
> **Менеджер зависимостей** — это инструмент, который автоматически загружает и устанавливает сторонние библиотеки в ваш проект.

## Что такое Composer?

**Composer** — это менеджер зависимостей для языка программирования PHP. Он позволяет управлять зависимостями в PHP-проектах, а также устанавливать и обновлять сторонние библиотеки [^2].

**Composer** выполняет следующие функции:

1. Позволяет управлять зависимостями в PHP-проектах.
2. Позволяет устанавливать и обновлять сторонние библиотеки.
3. Позволяет создавать и поддерживать файл `composer.json`, в котором указываются зависимости проекта.
4. Позволяет автоматически загружать и устанавливать библиотеки из [Packagist](https://packagist.org/), официального репозитория пакетов для PHP.

> [!NOTE]
> В этом пособии часто будет упоминаться слово зависимость. 
> **Зависимость** — это библиотека или пакет, который используется в вашем проекте. Например, библиотека для работы с базой данных или пакет для работы с пользовательским интерфейсом. Она называется зависимостью, потому что ваш проект зависит от нее для своей работы.

## `composer.json`

Главным файлом вашего проекта, использующего **Composer**, является `composer.json`.

Этот файл содержит список всех зависимостей проекта — библиотек и пакетов, необходимых для его работы, а также другие важные настройки.

 В `composer.json` можно указать:
   
1. Версию PHP
2. Минимальные требования к окружению
3. Название проекта
4. Информацию об авторах
5. Другие метаданные...

Зачем нужен этот файл? Простыми словами, он помогает управлять зависимостями проекта. Вместо того чтобы вручную искать и подключать библиотеки, Composer делает это за вас. Файл `composer.json` служит "списком покупок" для вашего проекта, где перечислены все инструменты и библиотеки, которые он использует.

Пример файла `composer.json`:

```json
{
    "name": "my-project",
    "description": "This is my project",
    "require": {
        "monolog/monolog": "^2.0",
        "laravel/framework": "^9.0"
        "laravel/ui": "^3.0"
    }
}
```

В этом примере указаны три зависимости проекта:

1. `monolog/monolog` — библиотека для логирования.
2. `laravel/framework` — фреймворк Laravel.
3. `laravel/ui` — пакет для работы с пользовательским интерфейсом в Laravel.

## Установка Composer

Для установки **Composer** на ваш компьютер, перейдите на [официальный сайт Composer](https://getcomposer.org/) и следуйте инструкциям по установке.

## Использование Composer

После установки Composer, вы можете использовать его в командной строке для управления зависимостями в своих проектах.

1. Установка зависимости

```bash
composer require monolog/monolog
```

2. Удаление зависимости

```bash
composer remove monolog/monolog
```

3. Установка зависимостей из файла `composer.json` (у вас должен быть файл `composer.json` в корне проекта с описанием зависимостей)

```bash
composer install
```

4. Обновление зависимостей (устанавливает последние версии зависимостей, указанных в файле `composer.json`)

```bash
composer update
```

5. Инициализация нового проекта

```bash
composer init
```

6. Просмотр списка установленных зависимостей

```bash
composer show
```

6. Просмотр списка доступных команд

```bash
composer
```

> [!TIP]
> Для использования Composer в командной строке, вам необходимо находиться в корневой директории вашего проекта.

[^1]: Composer, getcomposer.org [online]. Available at: https://getcomposer.org/
[^2]: What is Composer? webjump.ai [online]. Available at: https://webjump.ai/en/composer-what-it-is-how-to-use-it-and-how-to-install-it