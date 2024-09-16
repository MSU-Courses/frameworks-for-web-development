# Introducere în Laravel

## Ce este Laravel? [^1]

**Laravel** este un framework gratuit pentru dezvoltarea web în limbajul de programare PHP.

A fost creat de **Taylor Otwell** și lansat pentru prima dată în _2011_.

Laravel oferă o sintaxă simplă și intuitivă, care permite dezvoltatorilor să creeze aplicații web rapid și eficient.

Laravel este un **framework full-stack**. Asta înseamnă că oferă instrumente pentru dezvoltarea atât a părții serverului, cât și a părții clientului pentru o aplicație web.

> [!NOTE]  
> Laravel este bazat pe framework-ul **Symfony** și folosește multe dintre componentele acestuia.

În prezent, cea mai recentă versiune de Laravel este **11.x**.

Cu toate acestea, multe proiecte încă utilizează versiunea **10.x**, deoarece **11.x** a fost lansată recent și a adus schimbări majore în funcționarea framework-ului.

> [!IMPORTANT]  
> În cadrul acestui curs, vom lucra cu versiunea **10.x** și vom face ocazional comparații cu versiunea **11.x**, deoarece majoritatea proiectelor folosesc în prezent versiunea 10.

## De ce Laravel?

- **Simplitate** — Laravel oferă o sintaxă simplă și intuitivă, permițând dezvoltatorilor să creeze aplicații web rapid și eficient.
- **Comunitate** — Laravel are o comunitate activă de dezvoltatori care creează și întrețin numeroase pachete și extensii pentru framework.
- **Securitate** — Laravel oferă metode testate pentru protecția datelor și prevenirea vulnerabilităților.
- **Scalabilitate** — Laravel are o arhitectură flexibilă, permițând dezvoltarea atât a aplicațiilor web mici, cât și a proiectelor mari.
- **Progresivitate** — Laravel este actualizat constant, adăugând noi funcții și îmbunătățiri.

## Cerințe de sistem pentru instalarea Laravel 10.x

- PHP >= 8.1
- Composer
- Node.js (>= 14.x) & NPM
- Extensii PHP: CType, cURL, DOM, Fileinfo, Filter, Hash, Mbstring, OpenSSL, PCRE, PDO, Session, Tokenizer, XML

## Crearea unui proiect Laravel [^2]

### Cu ajutorul Composer

1. Instalați Composer.
2. Creați un proiect cu ajutorul Composer:
   ```sh
   composer create-project laravel/laravel:^10.0 app_name
   ```
   Această comandă va crea un director `app_name` și va instala un nou proiect Laravel. Pentru a crea proiectul în directorul curent, înlocuiți `app_name` cu `.`.
3. Rulați serverul integrat Laravel:
   ```sh
   cd app_name 

   php artisan serve
   ```
4. După pornirea serverului, deschideți browserul și accesați `http://localhost:8000`.

### Cu ajutorul Laravel Installer

Laravel oferă un utilitar de linie de comandă pentru crearea de noi proiecte — **Laravel Installer**.

1. Instalați **Laravel Installer**:
   ```sh
   composer global require laravel/installer
   ```
2. Creați un proiect cu ajutorul Laravel Installer:
   ```sh
   laravel new app_name
   ```
3. Rulați serverul integrat Laravel:
   ```sh
   cd app_name 

   php artisan serve
   ```

## Utilitarul Artisan

Așa cum ați observat mai sus, pentru a rula proiectul, am folosit comanda `php artisan serve`.

`artisan` este un utilitar de linie de comandă scris în PHP, care oferă dezvoltatorilor multe comenzi utile pentru gestionarea proiectului [^3].

Dezvoltatorii Laravel au creat un set amplu de comenzi care pot fi utile în activitatea zilnică. Cu ajutorul acestora, puteți, de exemplu, să creați diferite clase utilizate în proiect sau să rulați teste.

Pentru a rula o comandă Artisan, folosiți următoarea sintaxă:
```sh
php artisan command_name
```

`command_name` reprezintă numele comenzii pe care doriți să o executați.

Pe măsură ce avansăm, vom folosi diverse comenzi Artisan în proiectele noastre.

> [!TIP]  
> Pentru a vizualiza toate comenzile disponibile în Artisan, executați comanda `php artisan list`.

[^1]: Meet Laravel, laravel.com [online]. URL: https://laravel.com/docs/11.x  
[^2]: Creating a Laravel project, laravel.com [online]. URL: https://laravel.com/docs/10.x/installation#creating-a-laravel-project  
[^3]: Consola Artisan, laravel.com [online]. URL: https://laravel.com/docs/10.x/artisan
