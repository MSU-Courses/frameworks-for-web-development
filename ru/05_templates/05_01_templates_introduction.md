# Введение в шаблонизацию и шаблонизаторы

Все меньше приложений в настоящее время остаются статическими, и все больше становятся динамическими.

Представим обычную HTML-страницу, содержащую информацию о пользователе. Вот пример того, как это может выглядеть:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Профиль пользователя</title>
  </head>
  <body>
    <h1>Профиль пользователя</h1>
    <p>Имя: Иван</p>
    <p>Фамилия: Иванов</p>
    <p>Возраст: 25</p>
  </body>
</html>
```

Теперь предположим, что у нас есть миллионы пользователей, и для каждого из них необходимо создать отдельную страницу.

Создавать миллионы HTML-страниц вручную было бы неэффективно, поскольку код придется дублировать снова и снова.

Нам нужен инструмент, который позволит генерировать страницы динамически с использованием шаблонов.

## Что такое шаблон?

**Шаблон** — это файл, содержащий статический текст, который используется для создания динамического содержимого.

Проще говоря, **шаблон** — это файл с заполнителями, которые при генерации страницы заменяются на реальные данные.

Примером могут служить документы из реальной жизни, такие как письма, которые содержат стандартные элементы, например, адрес отправителя, адрес получателя и т. д., а каждый пользователь заполняет соответствующие поля своими данными.

Таким образом, эти данные являются динамическим контентом, который вставляется в шаблон.

### Шаблоны в веб-разработке

Как и в реальной жизни, **шаблоны в веб-разработке** представляют собой страницы, содержащие статический текст и заполнители (динамические данные), которые заменяются реальными значениями при генерации контента.

В веб-разработке шаблоны используются для создания динамических веб-страниц, которые могут быть адаптированы под различные условия.

Например, есть шаблон страницы, содержащий информацию о пользователе. При генерации страницы заполнители в шаблоне заменяются на реальные данные пользователя. Шаблон один, а сгенерированных страниц — миллионы.

## PHP-шаблоны

Когда вы изучали PHP, для создания динамических страниц вы использовали PHP-код. Например, чтобы вывести данные о пользователи на странице, вы могли написать следующий код:

```php
<h1>Профиль пользователя</h1>
<p>Имя: <?php echo $name; ?></p>
<p>Фамилия: <?php echo $surname; ?></p>
<p>Возраст: <?php echo $age; ?></p>
```

Или, чтобы вывести данные из массива с помощью цикла:

```php
<?php foreach ($tasks as $task): ?>
    <h2><?php echo $task['title']; ?></h2>
    <p><?php echo $task['description']; ?></p>
<?php endforeach; ?>
```

Ранее это был стандартный способ создания динамических страниц на PHP.

Однако такой подход имеет свои недостатки, включая сложность поддержки и ухудшение читаемости кода.

HTML смешивается с PHP-кодом, что делает код менее понятным и сложным в поддержке.

Кроме того, если вам нужно создать общую структуру страницы (**layout**), это потребует сложных решений, включая необходимость отображать большие HTML-блоки внутри PHP-кода.

Со временем этот подход начал устаревать, и появились новые инструменты, такие как **шаблонизаторы**, которые помогают не только отделить логику приложения  от представления (например, HTML), но и упростить процесс создания динамических страниц.

## Что такое шаблонизатор?

**Шаблонизатор** (**движок шаблонов**) — это инструмент, который позволяет создавать динамические страницы на основе шаблонов.

В веб-разработке шаблонизаторы чаще всего используются для генерации HTML-страниц, используя шаблоны, содержащие статический текст и заполнители, которые заменяются на динамические данные.

Например,

HTML-шаблон:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Профиль пользователя</title>
  </head>
  <body>
    <h1>Профиль пользователя</h1>
    <p>Имя: {{ name }}</p>
    <p>Фамилия: {{ surname }}</p>
    <p>Возраст: {{ age }}</p>
  </body>
</html>
```

Данные:

```
name=Иван
surname=Иванов
age=25
```

При генерации страницы шаблонизатор заменит заполнители на реальные значения:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Профиль пользователя</title>
  </head>
  <body>
    <h1>Профиль пользователя</h1>
    <p>Имя: Иван</p>
    <p>Фамилия: Иванов</p>
    <p>Возраст: 25</p>
  </body>
</html>
```

Теперь вместо использования PHP-кода в HTML-шаблоне вы применяете специальные синтаксические конструкции, например, такие как `{{ name }}`, которые заменяются реальными данными при генерации страницы. Это делает код более читаемым и понятным. При рендеринге страницы вы просто передаете данные шаблонизатору, **отделяя логику от представления.**

Таким образом, шаблонизатор позволяет создавать динамические страницы, используя шаблоны и данные.