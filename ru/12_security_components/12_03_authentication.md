# Аутентификация в Laravel

> [!NOTE]
> **Аутентификация** — это процесс проверки подлинности пользователя, который предоставляет доступ к ресурсам или функционалу веб-приложения. Аутентификация отвечает на вопрос: "_Кто ты?_".

Laravel предоставляет готовые инструменты через библиотеку **Laravel Breeze** или более комплексное решение **Laravel Jetstream**. Эти библиотеки упрощают реализацию аутентификации, но разработчик также может настроить процесс вручную, если требуется полная кастомизация.

В этой главе мы рассмотрим ручную настройку аутентификации в Laravel, используя **Eloquent** для работы с пользователями и **Blade** для отображения представлений.

## Модель и миграция пользователя

Laravel по умолчанию включает модель **User**, которая используется для управления учетными записями. Эта модель подключена к базе данных через таблицу **users**.

**Основные атрибуты модели**:

- `name` — имя пользователя.
- `email` — электронная почта, которая должна быть уникальной.
- `password` — зашифрованный пароль.
- `remember_token` — токен для функции "Запомнить меня".

Миграция для таблицы пользователей создается автоматически. Она выглядит следующим образом:

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
> Где находится настройка? Таблица `users` создается миграцией, которая находится в папке `database/migrations`. Если нужно изменить структуру, откройте соответствующий файл миграции и внесите правки.

При первом выполнении миграции создается таблица **users** с указанными полями.

## Регистрация пользователя

Регистрация пользователя включает в себя создание новой учетной записи с уникальным email и паролем. Для этого нужно создать форму с полями для ввода данных и обработать их в контроллере.

**Шаги регистрации**:

1. **Создание формы**. Форма содержит поля для ввода имени, email и пароля.
2. **Обработка данных**. Данные из формы отправляются на сервер для создания новой учетной записи.
3. **Хеширование пароля**. Пароль пользователя хешируется перед сохранением в базу данных.
4. **Сохранение пользователя**. Новая учетная запись сохраняется в базе данных.
5. **Аутентификация**. После успешной регистрации пользователь автоматически аутентифицируется.
6. **Перенаправление**. Пользователь перенаправляется на защищенную страницу.

### Создание формы

Форма для регистрации пользователя может быть создана с помощью **Blade**. Пример формы:

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
> Было добавлено поле `password_confirmation` для подтверждения пароля. Это обычная практика для предотвращения ошибок при вводе пароля.

### Обработка данных для регистрации

Для обработки данных создается контроллер, который содержит метод для регистрации пользователя. **Пример контроллера**:

```php
// AuthController.php
use App\Models\User;

public function register(Request $request)
{
    // Валидация данных
    // Свойство `confirmed` проверяет совпадение паролей
    $request->validate([
        'name' => 'required|string|max:255',
        'email' => 'required|string|email|max:255|unique:users',
        'password' => 'required|string|min:8|confirmed',
    ]);

    // Создание нового пользователяÒ
    $user = User::query()->create([
        'name' => $request->name,
        'email' => $request->email,
        'password' => Hash::make($request->password),
    ]);

    // Аутентификация пользователя в системе
    auth()->login($user);

    // Перенаправление на защищенную страницу
    return redirect('/dashboard');
}
```

В данном методе, мы используем **2 новых функции**:

- `Hash::make($request->password)` — хеширует пароль пользователя перед сохранением в базу данных. По умолчанию, Laravel использует алгоритм **bcrypt** для хеширования паролей.
- `auth()->login($user)` — аутентифицирует пользователя в систему. Как именно проходит процесс аутентификации, мы рассмотрим чуть позже.

## Аутентификация пользователя

Аутентификация пользователя включает в себя проверку идентификационных данных (email и пароль) и предоставление доступа к защищенным ресурсам. Для этого нужно создать форму для входа и обработать данные в контроллере.

**Шаги аутентификации**:

1. **Создание формы**. Форма содержит поля для ввода email и пароля.
2. **Обработка данных**. Данные из формы отправляются на сервер для проверки.
3. **Проверка данных**. Проверяется существование пользователя с указанным email и совпадение пароля.
4. **Аутентификация**. После успешной проверки пользователь аутентифицируется.
5. **Перенаправление**. Пользователь перенаправляется на защищенную страницу.

### Создание формы

Форма для аутентификации пользователя может быть создана с помощью **Blade**. Пример формы:

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

### Обработка данных для аутентификации

Для обработки данных создается контроллер, который содержит метод для аутентификации пользователя. **Пример контроллера**:

```php
// AuthController.php

public function login(Request $request)
{
    // Валидация данных
    $request->validate([
        'email' => 'required|string|email',
        'password' => 'required|string',
    ]);

    // Проверка данных пользователя
    if (auth()->attempt($request->only('email', 'password'))) {
        // Перегенерация сессии для предотвращения атак
        $request->session()->regenerate();

        // Аутентификация пользователя в системе
        return redirect('/dashboard');
    }

    // Ошибка аутентификации
    return back()->withErrors([
        'email' => 'The provided credentials do not match our records.',
    ]);
}
```

В данном методе, мы используем **2 новых функции**:

- `auth()->attempt($credentials)` — проверяет идентификационные данные пользователя. Если данные верны, пользователь аутентифицируется в системе, и метод возвращает `true`, иначе `false`.
- `back()->withErrors($errors)` — перенаправляет пользователя на предыдущую страницу с ошибкой аутентификации. В данном случае, выводится сообщение об ошибке.

## Выход пользователя из системы

Выход пользователя из системы заключается в завершении сеанса аутентификации и перенаправлении на страницу входа. Для этого нужно создать ссылку или кнопку для выхода и обработать ее в контроллере.

**Шаги выхода из системы**:

1. **Создание ссылки**. Ссылка или кнопка для выхода пользователя из системы.
2. **Обработка данных**. Данные из ссылки отправляются на сервер для завершения сеанса.
3. **Завершение сеанса**. Сеанс аутентификации пользователя завершается.
4. **Перенаправление**. Пользователь перенаправляется на страницу входа.

> [!NOTE]
> Хорошей практикой является использовать метод `DELETE` для выхода пользователя из системы.

### Создание ссылки

Ссылка для выхода пользователя может быть создана с помощью **Blade**. Пример ссылки:

```html
<form method="POST" action="{{ route('logout') }}">
  @csrf @method('DELETE')
  <button type="submit">Logout</button>
</form>
```

### Обработка данных для выхода

Для обработки данных создается контроллер, который содержит метод для выхода пользователя из системы. **Пример контроллера**:

```php
// AuthController.php

public function logout(Request $request)
{
    // Завершение сеанса аутентификации
    auth()->logout();

    // Перегенерация сессии для предотвращения атак
    request()->session()->invalidate();

    // Перегенерация токена
    request()->session()->regenerateToken();

    // Перенаправление на страницу входа
    return redirect('/login');
}
```

## Сессии и аутентификация

В предыдущей подглаве мы рассмотрели процесс аутентификации пользователя в Laravel, часто используя сессии для хранения данных о пользователе и его состоянии. Чтобы глубже понять, как работает аутентификация, давайте изучим, как именно Laravel использует сессии для хранения данных.

### Сессии

**Как работают сессии в Laravel:**

1. **Создание сессии:** При первом запросе пользователя сервер Laravel создает уникальный идентификатор сессии, который сохраняется в cookie браузера.
2. **Хранение данных:** Информация о пользователе и его состоянии хранится на сервере, по умолчанию в файлах, расположенных в папке `storage/framework/sessions`.
3. **Использование данных:** Данные из сессии доступны на протяжении всего сеанса пользователя на сайте.
4. **Удаление сессии:** При выходе пользователя из системы или завершении сеанса сессия удаляется.

### Аутентификация с помощью сессий

Сессии играют ключевую роль в процессе аутентификации, позволяя приложению "помнить" аутентифицированного пользователя между запросами.

**Процесс аутентификации с использованием сессий:**

1. **Вход пользователя:** Пользователь вводит свои учетные данные (например, email и пароль) и отправляет их на сервер.
2. **Проверка учетных данных:**
   - Laravel использует метод `Auth::attempt()` для проверки предоставленных данных.
   - Метод `attempt()` принимает массив с полями, такими как 'email' и 'password'.
   - Фреймворк извлекает пользователя из базы данных, соответствующего указанному email.
   - Затем сравнивает предоставленный пароль с хешированным паролем, сохраненным в базе данных, используя функцию `Hash::check()`.
3. **Инициализация сессии:**
   - Если учетные данные верны, Laravel генерирует уникальный идентификатор сессии (Session ID).
   - Этот идентификатор сохраняется на стороне клиента в виде cookie.
4. **Сохранение данных в сессии:**
   - Laravel сохраняет в сессии информацию об аутентифицированном пользователе, включая его уникальный идентификатор (обычно `user_id`).
   - Эти данные позволяют идентифицировать пользователя при последующих запросах.

### Загрузка пользователя при каждом запросе

После аутентификации пользователя Laravel загружает данные пользователя при каждом запросе, что позволяет использовать методы для доступа к данным пользователя в любом месте приложения. Таким образом, при каждом входе в приложение нет необходимости повторно аутентифицироваться, поскольку Laravel загружает данные пользователя из сессии.

Laravel выполняет следующие действия при каждом запросе:

1. **Получение идентификатора сессии:**
   - Браузер отправляет cookie с Session ID на сервер вместе с каждым запросом.
   - Laravel извлекает идентификатор сессии из cookie, отправленного браузером.
2. **Загрузка данных сессии:**
   - Laravel использует Session ID для извлечения соответствующих данных сессии из хранилища (по умолчанию, файлы в папке `storage/framework/sessions`).
   - Например, файл сессии может называться `sess_1234567890abcdef`, где `1234567890abcdef` — это Session ID. Этот идентификатор был передан в cookie браузера, и Laravel использует его для поиска соответствующего файла сессии и извлечения оттуда данных (например, `user_id`).
3. **Идентификация пользователя:**
   - Из данных сессии фреймворк получает идентификатор пользователя (`user_id`).
4. **Загрузка модели пользователя:**
   - Используя `user_id`, Laravel извлекает соответствующую запись из таблицы `users` в базе данных.
   - Загруженная модель пользователя становится доступной через класс `Auth` или через хэлпер `auth()->user()`.

Этот процесс повторяется при каждом запросе, обеспечивая, что приложение всегда имеет актуальные данные о текущем пользователе и его правах доступа.

Для отображения информации, доступной только аутентифицированным пользователям, в Laravel используются шаблоны **Blade**. Этот механизм предоставляет удобные методы для проверки статуса аутентификации и ролей пользователя.

### Проверка аутентификации

В шаблонах Blade для проверки, авторизован ли пользователь, применяется директива `@auth`. Она позволяет выводить определенный контент исключительно для аутентифицированных пользователей.

**Пример использования директивы `@auth`:**

```blade
@auth
    <p>Добро пожаловать, {{ auth()->user()->name }}!</p>
@endauth
```

В этом примере блок кода внутри директивы `@auth` будет отображаться только для аутентифицированных пользователей. Если пользователь не прошел аутентификацию, данный блок не будет показан.

Для обработки случаев, когда пользователь не аутентифицирован, можно использовать директиву `@guest`:

```blade
@guest
    <p>Пожалуйста, <a href="{{ route('login') }}">войдите</a> в систему.</p>
@endguest
```

Таким образом, директивы `@auth` и `@guest` позволяют гибко управлять отображением контента в зависимости от статуса аутентификации пользователя. 


## Заключение

Аутентификация — это важный процесс в веб-разработке, который обеспечивает безопасность данных и контроль доступа к ресурсам приложения. Laravel предоставляет удобные инструменты для реализации аутентификации, позволяя разработчикам создавать безопасные и удобные в использовании приложения.

В этой главе мы рассмотрели основные шаги по реализации аутентификации в Laravel, включая создание форм для регистрации и входа, обработку данных в контроллере, аутентификацию пользователя и выход из системы. Мы также изучили, как Laravel использует сессии для хранения данных о пользователе и обеспечения безопасности приложения.