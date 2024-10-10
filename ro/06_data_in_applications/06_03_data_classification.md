# Clasificarea datelor într-o aplicație web

Înainte de a lucra cu datele, este esențial să le clasificăm pentru a înțelege ce tipuri de date vor fi utilizate în aplicație.

Clasificarea datelor ajută la determinarea nivelurilor de securitate, acces și reglementări necesare pentru fiecare categorie de date.

## Clasificarea datelor

| **Clasificare**               | **Descriere**                                                                                         | **Exemplu**                                                                  |
|-------------------------------|------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| **După nivelul de confidențialitate** |
| Date publice                   | Date disponibile pentru toți utilizatorii fără restricții.                                            | Conținut de pe pagina principală a unui site.                                 |
| Date interne                   | Date accesibile doar utilizatorilor autentificați ai sistemului.                                      | Documente interne ale companiei, conturile personale ale utilizatorilor.      |
| Date confidențiale             | Date care necesită acces restricționat din cauza riscurilor potențiale.                              | Strategii de afaceri, rapoarte financiare.                                    |
| Date sensibile                 | Date care necesită cel mai înalt nivel de protecție, cum ar fi datele medicale sau financiare.        | Dosare medicale, datele cardurilor de credit.                                 |
| Date personale                 | Date care pot identifica un utilizator specific, cum ar fi numele sau adresa de e-mail.               | Nume, prenume, adresă de e-mail, număr de telefon.                            |
| Date proprietare               | Date deținute de o companie, care îi conferă un avantaj competitiv.                                  | Brevete, secrete comerciale, date de cercetare și dezvoltare (R&D).           |
| **După sursa datelor**         |                                                                                                      |                                                                               |
| Date ale utilizatorilor        | Informații introduse de utilizatori prin formulare sau cereri API.                                   | Datele dintr-un formular de înregistrare sau trimise prin cereri API.         |
| Date de sistem                 | Date generate automat de sistem, cum ar fi jurnale sau date despre sesiuni.                         | Jurnalele de erori, sesiunile utilizatorilor.                                 |
| Date externe                   | Date obținute din surse externe, cum ar fi API-urile altor sisteme.                                  | Date de la servicii terțe, cum ar fi prognoza meteo sau cursurile valutare.   |
| **După tipul datelor**         |                                                                                                      |                                                                               |
| Date structurate               | Date organizate într-o structură clară, de exemplu, în tabelele unei baze de date.                   | Tabelele unei baze de date.                                                   |
| Date semistructurate           | Date care conțin o structură parțială, cum ar fi fișierele JSON sau XML.                            | Fișiere JSON, documente XML.                                                  |
| Date nestructurate             | Date fără o structură clară, cum ar fi documentele text sau imaginile.                              | Fișiere text, imagini, videoclipuri.                                          |
| **După scopul utilizării**     |                                                                                                      |                                                                               |
| Date operaționale              | Date utilizate pentru desfășurarea operațiunilor curente ale aplicației.                            | Informații despre tranzacții, comenzi, conturi de utilizator.                 |
| Date analitice                 | Date utilizate pentru analiză și luarea deciziilor.                                                  | Datele de analiză ale site-ului, rapoarte despre trafic.                      |
| **După ciclul de viață**       |                                                                                                      |                                                                               |
| Date active                    | Date utilizate activ în prezent.                                                                    | Comenzi curente, sesiuni active ale utilizatorilor.                           |
| Date de arhivă                 | Date care nu sunt utilizate temporar, dar ar putea fi necesare în viitor.                           | Proiecte vechi care pot fi restaurate la cerere.                              |
| Date depășite                  | Date care nu mai sunt necesare și pot fi șterse.                                                     | Rapoarte neactualizate, campanii de marketing învechite.                      |
| Date temporare                 | Date care există doar temporar și sunt șterse după utilizare.                                        | Cache-ul browserului, fișiere temporare ale sesiunilor.                       |

## Diferența dintre date confidențiale și date sensibile

**Datele confidențiale** se referă la interesele organizației și pot include planuri interne sau secrete comerciale. Scurgerea acestor date poate afecta competitivitatea companiei.

**Datele sensibile** sunt mai degrabă legate de informațiile personale ale utilizatorilor și necesită protecție riguroasă, deoarece scurgerea acestora poate provoca daune serioase persoanelor fizice, inclusiv furtul de identitate sau pierderi financiare​.

[^1]: 7 Types of Data Classification, datamation [online]. URL: https://www.datamation.com/big-data/types-of-data-classification/