# Directorul `storage` și `public` în Laravel

## Directorul `storage`

Folderul `storage` este un element esențial în proiectele Laravel, destinat stocării *temporare* și *permanente* a datelor. Acesta este organizat în mai multe subfoldere, fiecare având un rol specific:

- **`app`**: pentru datele asociate aplicației (de exemplu, fișiere temporare sau încărcările utilizatorilor).  
- **`framework`**: pentru fișiere necesare framework-ului (cache, sesiuni, fișiere temporare).  
- **`logs`**: pentru logurile aplicației, facilitând diagnosticarea erorilor.  

Utilizarea folderului `storage` asigură organizarea fișierelor într-un mod care le face inaccesibile direct din internet. Acest aspect crește securitatea aplicației, protejând datele utilizatorilor, logurile și fișierele temporare.

### De ce este important folderul `storage`?

Directorul `storage` joacă un rol esențial în Laravel datorită următoarelor avantaje:

- **Securitate**: Fișierele din `storage` nu sunt accesibile direct prin browser, prevenind accesul neautorizat.  
- **Organizare**: Laravel separă diferitele tipuri de date în subfoldere specifice, ceea ce facilitează gestionarea fișierelor.  
- **Utilitate**: Cu ajutorul clasei `Storage`, fișierele pot fi scrise, citite sau șterse ușor, fără a te ocupa de operațiuni de nivel scăzut.

## Directorul `public`

Folderul `public` din Laravel reprezintă singura zonă accesibilă utilizatorilor prin intermediul browserului web. Aici ar trebui să fie plasate toate fișierele publice, cum ar fi imagini, fișiere CSS, JavaScript și alte resurse statice.

La configurarea serverului web, directorul `public` este indicat ca rădăcina aplicației web.

> [!NOTE]  
> Laravel folosește acest model pentru a **crește securitatea**: fișierele și codul aplicației care sunt situate în afara folderului `public` nu sunt accesibile direct prin browser, prevenind astfel accesul neautorizat la date sensibile precum fișierele de configurare sau baza de date.

## Accesul la fișierele din `storage` prin `public`

Directorul `storage` este destinat stocării fișierelor care nu ar trebui să fie accesibile direct din browser. Totuși, uneori este necesar să oferi acces la anumite fișiere, cum ar fi imaginile încărcate de utilizatori sau documentele ce trebuie afișate pe site. Pentru aceasta, Laravel permite crearea unui **link simbolic** între folderul `public/storage` și `storage/app/public`.

Acest link simbolic face posibil accesul la fișierele din `storage/app/public` printr-un URL, fără a expune întreg folderul `storage`.

### Crearea unui link simbolic

Pentru a crea link-ul simbolic, folosește comanda:

```bash
php artisan storage:link
```

După executarea comenzii, fișierele din `storage/app/public` vor fi accesibile prin URL-ul `/storage`, de exemplu:

```html
<!-- Fișierul este localizat în /storage/app/public/image.jpg -->
<img src="/storage/image.jpg" alt="Imagine">
```

În lecțiile următoare, vom lucra intens cu folderul `storage` și vom utiliza clasa `Storage` pentru a gestiona fișierele în Laravel.