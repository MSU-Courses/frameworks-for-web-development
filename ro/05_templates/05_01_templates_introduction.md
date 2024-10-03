# Introducere în șablonare și motoare de șabloane

Din ce în ce mai puține aplicații rămân statice în prezent, iar tot mai multe devin dinamice.

Să ne imaginăm o pagină HTML obișnuită care conține informații despre un utilizator. Iată un exemplu de cum ar putea arăta:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Profil utilizator</title>
  </head>
  <body>
    <h1>Profil utilizator</h1>
    <p>Nume: Ivan</p>
    <p>Prenume: Ivanov</p>
    <p>Vârstă: 25</p>
  </body>
</html>
```

Acum să presupunem că avem milioane de utilizatori și trebuie să creăm o pagină separată pentru fiecare dintre ei.

Crearea manuală a milioane de pagini HTML ar fi ineficientă, deoarece codul ar trebui duplicat din nou și din nou.

Avem nevoie de un instrument care să ne permită să generăm pagini dinamic folosind șabloane.

## Ce este un șablon?

Un **șablon** este un fișier care conține text static, folosit pentru a crea conținut dinamic.

Mai simplu spus, un **șablon** este un fișier cu locuri rezervate (placeholders), care sunt înlocuite cu date reale în momentul generării paginii.

Un exemplu ar fi documentele din viața reală, cum ar fi scrisorile, care conțin elemente standard, cum ar fi adresa expeditorului, adresa destinatarului etc., iar fiecare utilizator completează câmpurile respective cu datele sale.

Astfel, aceste date reprezintă conținutul dinamic care este introdus în șablon.

### Șabloane în dezvoltarea web

La fel ca în viața reală, **șabloanele în dezvoltarea web** sunt pagini care conțin text static și locuri rezervate (date dinamice), care sunt înlocuite cu valori reale la generarea conținutului.

În dezvoltarea web, șabloanele sunt utilizate pentru a crea pagini web dinamice, care pot fi adaptate în funcție de diverse condiții.

De exemplu, există un șablon de pagină care conține informații despre un utilizator. La generarea paginii, locurile rezervate din șablon sunt înlocuite cu datele reale ale utilizatorului. Șablonul este unic, dar paginile generate pot fi milioane.

## Șabloane PHP

Când ați studiat PHP, pentru a crea pagini dinamice, ați folosit cod PHP. De exemplu, pentru a afișa datele despre un utilizator pe o pagină, ați putea scrie următorul cod:

```php
<h1>Profil utilizator</h1>
<p>Nume: <?php echo $name; ?></p>
<p>Prenume: <?php echo $surname; ?></p>
<p>Vârstă: <?php echo $age; ?></p>
```

Sau, pentru a afișa date dintr-un array folosind un ciclu:

```php
<?php foreach ($tasks as $task): ?>
    <h2><?php echo $task['title']; ?></h2>
    <p><?php echo $task['description']; ?></p>
<?php endforeach; ?>
```

Anterior, acesta era modul standard de a crea pagini dinamice în PHP.

Totuși, acest mod are dezavantajele sale, inclusiv dificultatea de întreținere și scăderea lizibilității codului.

HTML-ul se amestecă cu codul PHP, ceea ce face codul mai greu de înțeles și de întreținut.

În plus, dacă aveți nevoie să creați o structură generală a paginii (**layout**), aceasta ar necesita soluții complexe, inclusiv necesitatea de a afișa blocuri mari de HTML în interiorul codului PHP.

Cu timpul, această abordare a început să devină învechită și au apărut noi instrumente, cum ar fi **motoarele de șabloane**, care ajută nu doar la separarea logicii aplicației de prezentare (cum ar fi HTML-ul), ci și la simplificarea procesului de creare a paginilor dinamice.

## Ce este un motor de șabloane?

Un **motor de șabloane** este un instrument care permite crearea de pagini dinamice pe baza șabloanelor.

În dezvoltarea web, motoarele de șabloane sunt cel mai frecvent utilizate pentru a genera pagini HTML, folosind șabloane care conțin text static și locuri rezervate, care sunt înlocuite cu date dinamice.

De exemplu,

Șablon HTML:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Profil utilizator</title>
  </head>
  <body>
    <h1>Profil utilizator</h1>
    <p>Nume: {{ name }}</p>
    <p>Prenume: {{ surname }}</p>
    <p>Vârstă: {{ age }}</p>
  </body>
</html>
```

Datele:

```
name=Ivan
surname=Ivanov
age=25
```

La generarea paginii, motorul de șabloane va înlocui locurile rezervate cu valorile reale:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Profil utilizator</title>
  </head>
  <body>
    <h1>Profil utilizator</h1>
    <p>Nume: Ivan</p>
    <p>Prenume: Ivanov</p>
    <p>Vârstă: 25</p>
  </body>
</html>
```

Acum, în loc să folosiți cod PHP în șablonul HTML, utilizați construcții sintactice speciale, cum ar fi `{{ name }}`, care sunt înlocuite cu date reale în momentul generării paginii. Acest lucru face codul mai lizibil și mai ușor de înțeles. La randarea paginii, pur și simplu transmiteți datele motorului de șabloane, **separând logica de prezentare**.

Astfel, un motor de șabloane permite crearea de pagini dinamice folosind șabloane și date.