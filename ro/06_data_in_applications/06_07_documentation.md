# Documentația în aplicații

Când discutăm despre date, ne gândim adesea la cele stocate în baze de date sau fișiere. Totuși, în aplicații există și alte tipuri de date care nu sunt stocate în aceste forme tradiționale. Un exemplu important este **documentația**.

**Documentația** reprezintă informațiile care descriu cum funcționează o aplicație, ce funcționalități oferă și cum să le utilizăm. Nu este doar un set de instrucțiuni, ci un instrument crucial care ajută utilizatorii și dezvoltatorii să interacționeze eficient cu sistemul.

Documentația poate fi clasificată în funcție de diverse criterii, cum ar fi scopul, formatul sau publicul țintă. De asemenea, **documentația nu este limitată doar la fișiere text**, ci include și secțiuni de ajutor integrate, documentația API, comentariile din cod și multe altele.

## Formate de documentație

Documentația poate fi prezentată în diferite formate, în funcție de scopul și audiența țintă:
- **Document text** — de exemplu, un fișier `README.md`, care oferă informații sumare despre proiect, instalare și utilizare.
- **Documentație integrată** — secțiuni de ajutor incluse în aplicație, la care utilizatorii pot accesa pentru asistență, frecvent întâlnite sub forma secțiunilor „Ajutor” sau FAQ.
- **Documentația API** — pentru aplicațiile care interacționează cu servicii externe, este un element esențial. Aceasta descrie metodele disponibile, parametrii și răspunsurile API.
- **Documentația codului** — comentarii în fișierele sursă, care explică modul în care funcționează clasele, metodele și funcțiile.

## Ce include documentația?

Documentația poate conține o gamă largă de informații, în funcție de complexitatea aplicației. Câteva elemente esențiale sunt:
- **Descrierea aplicației** — informații generale despre scopul și funcționalitățile aplicației.
- **Descrierea funcționalităților** — detalii despre fiecare funcție a aplicației și cum poate fi utilizată.
- **Descrierea interfeței** — explicații despre interfața utilizatorului, astfel încât utilizatorii să știe cum să interacționeze cu aplicația.
- **Structura directoarelor și fișierelor** — utilă pentru dezvoltatori, pentru a înțelege organizarea proiectului.
- **Exemple de utilizare** — scenarii concrete care arată cum poate fi folosită aplicația.
- **Ghiduri de instalare și configurare** — instrucțiuni pentru instalarea corectă și configurarea aplicației în diferite medii.

## De ce este importantă documentația? [^2]

Documentația este crucială atât pentru utilizatori, cât și pentru dezvoltatori. Aceasta ajută:
- La înțelegerea rapidă a funcționalităților și structurii aplicației.
- La întreținerea și extinderea aplicației în viitor.
- La oferirea suportului pentru utilizatori și la rezolvarea problemelor frecvente.
- La reducerea timpului necesar pentru instruirea noilor membri ai echipei.

Fără documentație, aplicația poate deveni dificil de înțeles și întreținut, ceea ce va complica utilizarea și dezvoltarea ei pe termen lung.

## Unde să stochezi documentația?

**Stocarea corectă a documentației** este esențială pentru accesibilitatea și actualitatea acesteia. Există mai multe metode de stocare, fiecare cu avantajele sale, în funcție de dimensiunea proiectului și nevoile echipei [^1].

### Stocarea documentației în depozitul Git

Stocarea documentației direct în depozitul Git este o metodă convenabilă și populară pentru majoritatea proiectelor, având mai multe avantaje:

- **Versionare** — modificările documentației sunt urmărite la fel ca și modificările codului. Acest lucru permite vizualizarea istoriei și revenirea la versiunile anterioare dacă este necesar.
- **Sincronizare cu codul** — documentația și codul sunt stocate în același loc, ceea ce facilitează actualizarea simultană a acestora.
- **Accesibilitate** — toți dezvoltatorii care au acces la depozit pot obține imediat versiunea actualizată a documentației.

**Exemple de stocare în Git**:
- Fișierul `README.md` în rădăcina depozitului, pentru descrierea generală a proiectului.
- Directorul `docs/` pentru documentație mai detaliată, ghiduri și manuale.

### Servicii externe de stocare a documentației

În unele cazuri, utilizarea serviciilor externe pentru stocarea și publicarea documentației poate fi mai eficientă. Aceste servicii oferă de obicei funcții suplimentare, cum ar fi interfețe pentru editare, navigare ușoară și implementare automată.

#### Servicii populare pentru documentație:

1. **GitHub Pages / GitLab Pages**
   - **Descriere**: GitHub și GitLab oferă posibilitatea de a publica site-uri statice, inclusiv documentație, direct din depozite.
   - **Avantaje**: Integrare ușoară cu depozitul, actualizare automată la fiecare commit.
   - **Când să folosești**: Când ai nevoie de documentație sub formă de site web cu navigare și căutare.

2. **Read the Docs**
   - **Descriere**: Un serviciu popular pentru crearea și publicarea documentației. Poate încărca și actualiza automat documentația din depozite (de exemplu, GitHub).
   - **Avantaje**: Suportă mai multe formate (Markdown, reStructuredText), oferă căutare și versionare.
   - **Când să folosești**: Pentru proiecte mari cu documentație complexă, unde este necesară o structură mai avansată.

3. **Confluence**
   - **Descriere**: O platformă corporativă pentru documentare și colaborare, care permite organizarea documentației în pagini.
   - **Avantaje**: Ideal pentru colaborare în echipă, suportă comentarii, revizii și integrări cu alte produse Atlassian (de exemplu, Jira).
   - **Când să folosești**: Pentru organizații mari, unde este importantă colaborarea asupra documentației și integrarea cu alte instrumente.

4. **Notion**
   - **Descriere**: Un instrument modern pentru documentare și organizarea informațiilor, cu suport pentru structuri flexibile (baze de date, wiki, liste de sarcini etc.).
   - **Avantaje**: Interfață ușor de utilizat, suport pentru colaborare și personalizare a structurii.
   - **Când să folosești**: Pentru echipe sau proiecte mai mici, unde flexibilitatea și accesibilitatea sunt importante.

### Cum să alegi metoda potrivită?

Alegerea locului de stocare depinde de nevoile proiectului:

- **Proiecte mici**: Stocarea în depozit (de exemplu, fișierul `README.md`) poate fi suficientă.
- **Proiecte medii**: O soluție mai complexă, cum ar fi un director `docs/` sau un serviciu precum **Notion**, poate fi mai potrivită.
- **Proiecte mari**: Pentru aplicații complexe, serviciile dedicate, precum GitHub Pages, Read the Docs sau Confluence, oferă navigare și structurare avansată a documentației.

## Documentația ca parte a dezvoltării

**Documentația trebuie considerată o parte integrantă a procesului de dezvoltare**. Ar trebui creată simultan cu codul și menținută actualizată pe măsură ce aplicația evoluează. Instrumentele automate, precum generatoarele de documentație pentru API sau pluginurile IDE, pot facilita acest proces.

**Documentația** este mai mult decât un supliment util — este un element esențial al oricărui software de calitate. Cu cât documentația este mai completă și bine structurată, cu atât mai ușor este pentru utilizatori și dezvoltatori să utilizeze, întrețină și extindă aplicația.

[^1]: Tools and techniques for effective code documentation. github.com [online]. URL: https://github.com/resources/articles/software-development/tools-and-techniques-for-effective-code-documentation

[^2]: The Types, Roles, and Practices of Documentation in Data Analytics Open Source Software Libraries. Computer Supported Cooperative Work (CSCW) [online]. URL: https://link.springer.com/article/10.1007/s10606-018-9333-1