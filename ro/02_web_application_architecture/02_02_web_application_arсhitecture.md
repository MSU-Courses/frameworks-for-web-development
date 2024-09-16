# Arhitectura aplicațiilor web

Înainte de a începe dezvoltarea unei aplicații web, este important să înțelegeți arhitectura acesteia, adică structura și interconectarea componentelor aplicației.

**Arhitectura aplicației web** reprezintă structura care definește organizarea logică și fizică a componentelor, modul lor de interacțiune și metodele de comunicare pentru implementarea unei aplicații web[^1].

Imaginați-vă că trebuie să construiți o casă. Înainte de a începe construcția, trebuie să creați un plan, să determinați numărul de camere, poziționarea acestora, materialele utilizate etc. La fel este și cu o aplicație web. Înainte de a începe dezvoltarea, trebuie să stabiliți structura aplicației, componentele sale și relațiile dintre acestea.

Înainte de a crea o aplicație web, trebuie să răspundeți la următoarele întrebări:
- **Ce componente vor face parte din aplicație?** De exemplu, poate include partea de client (Frontend), partea de server (Backend), baza de date etc.
- **Cum vor interacționa componentele între ele?** De exemplu, partea de client va interacționa cu partea de server, partea de server cu baza de date etc.
- **Ce tehnologii vor fi utilizate pentru realizarea aplicației?** De exemplu, pentru partea de client se pot folosi HTML, CSS, JavaScript, pentru partea de server – PHP, pentru baza de date – MySQL etc.
- **Ce principii și pattern-uri de proiectare vor fi utilizate?** De exemplu, poate fi pattern-ul MVC (Model-View-Controller), pattern-ul REST (Representational State Transfer) etc.

Deși unele cuvinte vi se pot părea complicate și neclare, nu vă faceți griji, le vom analiza în detaliu mai târziu.

## Tipuri de arhitecturi ale aplicațiilor web

În prezent, există multe tipuri diferite de arhitecturi pentru aplicații web. Mai jos sunt prezentate cele mai populare:
- **Arhitectura monolitică**
- **Arhitectura orientată pe servicii (SOA)**
- **Arhitectura bazată pe microservicii**

### Arhitectura monolitică

**Arhitectura monolitică** este un tip de arhitectură în care toate componentele aplicației sunt localizate într-un singur loc[^2].

Toate cererile de la client sunt procesate pe server, care răspunde la acestea. Componentele aplicației sunt interconectate direct și funcționează ca un întreg unitar.

Acesta este cel mai simplu tip de arhitectură, ideal pentru proiectele mici.

Cel mai probabil, până acum ați lucrat cu _arhitectura monolitică_, deoarece este cea mai simplă și mai ușor de înțeles. În acest tip de arhitectură se utilizează o singură bază de cod, un singur limbaj de programare, o singură bază de date etc. Este formatul clasic de aplicații web cu care ați lucrat anterior.

<img src="https://img001.prntscr.com/file/img001/WyWASa3tQ46jRpygDvulhw.png" width="800" alt="Arhitectura monolitică" />

> [!NOTE]
> În acest curs, vom folosi _arhitectura monolitică_ pentru dezvoltarea aplicațiilor web.

| Avantaje | Dezavantaje |
|----------|-------------|
| Simplitatea dezvoltării și a implementării | Dificultăți în scalare |
| O singură bază de cod și un singur stack tehnologic | Lipsă de flexibilitate și adaptabilitate |
| Mai puține probleme de performanță în faza inițială | Dificultăți în efectuarea modificărilor și testarea |
| Gestionarea centralizată a datelor și configurațiilor | Creșterea timpului de implementare odată cu extinderea aplicației |
| Ușurința monitorizării și depanării | Risc ridicat de eșec total în cazul unei erori într-un component |

### Arhitectura orientată pe servicii (Service-Oriented Architecture, SOA)

Luând în considerare dezavantajele _arhitecturii monolitice_, dezvoltatorii au început să caute noi abordări pentru a rezolva problemele de scalabilitate, flexibilitate și adaptabilitate. Una dintre soluții a fost _arhitectura orientată pe servicii (SOA)_.

**Arhitectura orientată pe servicii (SOA)** este un tip de arhitectură în care aplicația este împărțită în servicii independente, fiecare realizând o funcție specifică[^5].

Pe scurt, există mai multe servicii independente care interacționează între ele prin intermediul rețelei. Fiecare serviciu îndeplinește o funcție distinctă și poate fi implementat pe un server separat.

<img src="https://img001.prntscr.com/file/img001/Wbf9VWkvS4Oc8XvH2rBaJQ.png" width="800" alt="Arhitectura orientată pe servicii" />

| Avantaje | Dezavantaje |
|----------|-------------|
| Flexibilitate și adaptabilitate | Dificultăți în implementarea și gestionarea multiplelor servicii |
| Scalabilitatea componentelor individuale | Complexitate în monitorizare și depanare |
| Independența în dezvoltare și implementare | Creșterea complexității infrastructurii |
| Reziliență și toleranță la erori crescută | Probleme potențiale de performanță din cauza interacțiunilor dintre servicii |
| Posibilitatea de a folosi tehnologii și limbaje diferite pentru servicii distincte | Coordonarea dificilă între echipele de dezvoltare |

Cu toate acestea, odată cu creșterea numărului de utilizatori, cererilor și volumului de date, _arhitectura orientată pe servicii_ a început să prezinte, la rândul ei, dezavantaje. Astfel, s-a dezvoltat o nouă abordare – _arhitectura bazată pe microservicii_.

### Arhitectura bazată pe microservicii

**Arhitectura bazată pe microservicii** este un tip de arhitectură în care aplicația este împărțită în mici servicii independente, fiecare având o funcție specifică[^6].

Mulți ar putea crede că _arhitectura bazată pe microservicii_ este similară cu _arhitectura orientată pe servicii_ (SOA), însă există o diferență importantă: dimensiunea serviciilor. În _arhitectura microserviciilor_, serviciile trebuie să fie cât mai mici și independente.

În această arhitectură, fiecare microserviciu este proiectat pentru a îndeplini o singură sarcină sau funcție, ceea ce permite o scalare mai ușoară și o independență clară. În timp ce serviciile tradiționale din SOA pot îngloba mai multe funcționalități și nu urmează strict principiul separării responsabilităților, microserviciile se concentrează pe această separare.

De exemplu, într-o arhitectură SOA, un serviciu de „Gestionare a utilizatorilor” poate include funcții de înregistrare, autentificare și gestionare a profilurilor într-un singur serviciu. În schimb, într-o arhitectură bazată pe microservicii, un microserviciu separat va fi responsabil doar pentru autentificare, iar alte microservicii se vor ocupa de înregistrare și gestionarea profilului.

Ca și alte tipuri de arhitecturi, _arhitectura bazată pe microservicii_ are atât avantaje, cât și dezavantaje.

| Avantaje | Dezavantaje |
|----------|-------------|
| Flexibilitate și adaptabilitate | Complexitatea implementării și gestionării multiplelor microservicii |
| Implementare independentă | Creșterea complexității testării |
| Scalare independentă | Întârzieri în rețea din cauza interacțiunilor dintre microservicii |
| Creșterea rezilienței | Dificultăți în menținerea coerenței datelor |
| Echipe mici și specializate | Necesitatea unei infrastructuri mai complexe |
| Utilizarea tehnologiilor diferite pentru fiecare serviciu | Cerințe crescute pentru monitorizare și logare |
| Îmbunătățirea suportului și actualizării | Posibilă duplicare a datelor |
| Implementare rapidă a noilor funcții | Necesitatea gestionării dependențelor între microservicii |
| Gestionare simplificată a codului | Costuri mai mari pentru dezvoltare și întreținere |

## Interacțiunea între servicii

Probabil vă întrebați cum interacționează serviciile între ele în arhitectura _orientată pe servicii_ și în _arhitectura bazată pe microservicii_. Cum este posibil ca două aplicații independente, care pot fi scrise în limbaje de programare diferite, să comunice între ele?

În acest capitol nu vom analiza în detaliu interacțiunea între servicii, deoarece este un subiect complex pe care îl vom trata mai târziu.

Pentru a facilita interacțiunea între servicii, se folosește API-ul (Application Programming Interface), care permite unei aplicații să comunice cu alta.

**API-ul** definește un set de reguli și standarde care stabilesc modul în care o aplicație poate interacționa cu alta.

Cum credeți că serviciile pot comunica între ele?

Un exemplu simplu de interacțiune ar putea fi schimbul de informații prin intermediul unui fișier text. Un serviciu scrie datele într-un fișier, iar altul le citește. De exemplu:
1. Serviciul `A` scrie datele unui utilizator într-un fișier.
2. Serviciul `B` citește aceleași date din fișier și le stochează într-o bază de date.

Acest lucru poate fi considerat o formă de API, deoarece serviciile `A` și `B` comunică între ele prin intermediul fișierului. Cu toate acestea, aceasta nu este o metodă eficientă de interacțiune și poate cauza multe probleme.

[^1]: Web Application Architecture, medium.com [online]. URL: https://medium.com/geekculture/web-application-architecture-800d3ecd8019
[^2]: Monolithic Architecture: What, Why and When, medium.com [online]. URL: https://medium.com/swlh/monolithic-architecture-what-why-and-when-986dc5d5ce03
[^3]: Что такое монолитная архитетура, iaassaaspaas [online]. URL: https://iaassaaspaas.ru/terminologiya/chto-takoe-monolitnaya-arhitetura
[^4]: Monolith vs Microservices, evergreen.team [online]. URL: https://evergreen.team/
[^5]: What Is Service-Oriented Architecture? medium.com [online]. URL: https://medium.com/@SoftwareDevelopmentCommunity/what-is-service-oriented-architecture-fa894d11a7ec
[^6]: Microservices Architecture From A to Z? medium.com [online]. URL: https://medium.com/swlh/microservices-architecture-from-a-to-z-7287da1c5d28