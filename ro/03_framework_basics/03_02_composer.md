# Composer - un manager de dependențe pentru PHP

Înainte de a începe să studiați framework-ul Laravel, este important să înțelegeți ce este **Composer**.

Să ne imaginăm o situație în care doriți să utilizați o bibliotecă externă în proiectul dumneavoastră, de exemplu, pentru a lucra cu o bază de date. Cel mai probabil, mulți dezvoltatori au creat deja o astfel de bibliotecă, așa că nu trebuie să o construiți de la zero. Tot ce trebuie să faceți este să conectați această bibliotecă la proiectul dumneavoastră.

În loc să descărcați biblioteca de pe GitHub, să extrageți arhiva și să copiați fișierele în proiect, puteți utiliza un manager de pachete (sau manager de dependențe) numit **Composer**[^1].

> [!NOTE]  
> **Managerul de dependențe** este un instrument care descarcă și instalează automat biblioteci externe în proiectul dumneavoastră.

## Ce este Composer?

**Composer** este un manager de dependențe pentru limbajul de programare PHP. Acesta permite gestionarea dependențelor în proiectele PHP, precum și instalarea și actualizarea bibliotecilor externe[^2].

**Composer** oferă următoarele funcționalități:

1. Permite gestionarea dependențelor în proiectele PHP.
2. Permite instalarea și actualizarea bibliotecilor externe.
3. Permite crearea și întreținerea fișierului `composer.json`, în care sunt specificate dependențele proiectului.
4. Permite descărcarea și instalarea automată a bibliotecilor din [Packagist](https://packagist.org/), depozitul oficial de pachete pentru PHP.

> [!NOTE]  
> În acest ghid, termenul **dependență** va fi menționat frecvent.  
> **Dependența** reprezintă o bibliotecă sau un pachet utilizat în proiectul dumneavoastră. De exemplu, o bibliotecă pentru gestionarea unei baze de date sau un pachet pentru interfața cu utilizatorul. Se numește dependență deoarece proiectul depinde de aceasta pentru a funcționa corect.

## `composer.json`

Fișierul principal al proiectului care utilizează **Composer** este `composer.json`.

Acest fișier conține o listă a tuturor dependențelor proiectului - biblioteci și pachete necesare pentru funcționarea acestuia, precum și alte setări importante.

În `composer.json` puteți specifica:

1. Versiunea PHP
2. Cerințele minime pentru mediul de dezvoltare
3. Numele proiectului
4. Informații despre autori
5. Alte metadate...

De ce este necesar acest fișier? Pe scurt, ajută la gestionarea dependențelor proiectului. În loc să căutați și să conectați manual bibliotecile, Composer face acest lucru pentru dumneavoastră. Fișierul `composer.json` funcționează ca o „listă de cumpărături” pentru proiectul dumneavoastră, în care sunt enumerate toate instrumentele și bibliotecile utilizate.

Exemplu de fișier `composer.json`:

```json
{
    "name": "my-project",
    "description": "Acesta este proiectul meu",
    "require": {
        "monolog/monolog": "^2.0",
        "laravel/framework": "^9.0",
        "laravel/ui": "^3.0"
    }
}
```

În acest exemplu, sunt specificate trei dependențe pentru proiect:

1. `monolog/monolog` — o bibliotecă pentru logare.
2. `laravel/framework` — framework-ul Laravel.
3. `laravel/ui` — un pachet pentru lucrul cu interfața în Laravel.

## Instalarea Composer

Pentru a instala **Composer** pe computerul dumneavoastră, accesați [site-ul oficial Composer](https://getcomposer.org/) și urmați instrucțiunile de instalare.

## Utilizarea Composer

După instalarea Composer, îl puteți utiliza în linia de comandă pentru a gestiona dependențele proiectelor dumneavoastră.

1. Instalarea unei dependențe:

```bash
composer require monolog/monolog
```

2. Eliminarea unei dependențe:

```bash
composer remove monolog/monolog
```

3. Instalarea dependențelor din fișierul `composer.json` (trebuie să aveți un fișier `composer.json` în directorul principal al proiectului cu descrierea dependențelor):

```bash
composer install
```

4. Actualizarea dependențelor (instalează cele mai recente versiuni ale dependențelor specificate în fișierul `composer.json`):

```bash
composer update
```

5. Inițializarea unui proiect nou:

```bash
composer init
```

6. Vizualizarea listei de dependențe instalate:

```bash
composer show
```

7. Vizualizarea listei de comenzi disponibile:

```bash
composer
```

> [!TIP]  
> Pentru a utiliza Composer în linia de comandă, trebuie să vă aflați în directorul principal al proiectului dumneavoastră.

[^1]: Composer, getcomposer.org [online]. URL: https://getcomposer.org/  
[^2]: What is Composer? webjump.ai [online]. URL: https://webjump.ai/en/composer-what-it-is-how-to-use-it-and-how-to-install-it/