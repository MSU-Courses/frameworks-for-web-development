# Stocarea datelor în aplicațiile web

În capitolul anterior am discutat în linii mari procesul de stocare a datelor în aplicațiile web. Acum vom explora diferitele tipuri de soluții de stocare a datelor utilizate în aceste aplicații.

## Tipuri de stocare a datelor

Tipurile de date și cerințele de procesare influențează modul în care acestea sunt stocate. În aplicațiile web, se folosesc mai multe tipuri de stocare, fiecare fiind potrivit pentru anumite tipuri de date și sarcini.

### 1. **Stocare de fișiere**

**Stocarea de fișiere** este utilizată pentru a salva date sub formă de fișiere pe discul serverului sau în cloud. Este una dintre cele mai simple metode de stocare, ideală pentru date mari și nestructurate.

- **Tipuri de date care pot fi stocate**:
   - **Fișiere media**: imagini, video, audio (de exemplu, avatarele utilizatorilor, fișiere video).
   - **Documente**: fișiere PDF, documente text (de exemplu, rapoarte, contracte).
   - **Jurnale și alte fișiere text**: date de log, fișiere de configurare.
   - **Fișiere binare**: arhive, programe.

Acest tip de stocare este utilizat în principal pentru conținut static.

### 2. **Baza de date**

**Baza de date** este folosită pentru stocarea datelor structurate care necesită procesare complexă, filtrare și căutare. Este soluția principală de stocare pentru majoritatea aplicațiilor web.

- **Tipuri de date care pot fi stocate**:
   - **Date structurate**: date despre utilizatori (nume, email, data înregistrării), comenzi, inventar.
   - **Date tabelare**: înregistrări organizate în rânduri și coloane (de exemplu, tabelul cu comenzi sau utilizatori).
   
- **Tipuri de baze de date**:
   - **Baze de date relaționale (SQL)**: MySQL, PostgreSQL, SQL Server. Acestea sunt ideale pentru date care pot fi organizate în tabele cu relații clare (de exemplu, utilizatorii și comenzile lor).
   - **Baze de date NoSQL**: MongoDB, Cassandra. Acestea sunt potrivite pentru stocarea unor volume mari de date nestructurate sau care se schimbă frecvent (de exemplu, fluxuri de știri, date IoT).
   - **NewSQL**: baze de date moderne, precum CockroachDB, care combină scalabilitatea NoSQL cu integritatea datelor din SQL.

### 3. **Cache-ul**

**Cache-ul** este un depozit temporar de date folosit pentru accelerarea accesului la informații. Cache-ul stochează date pentru acces rapid, evitând accesul repetat la baza de date sau la stocarea de fișiere.

- **Tipuri de date care pot fi stocate**:
   - **Date accesate frecvent**: rezultatele interogărilor SQL, liste de produse, cursuri valutare.
   - **Date de sesiune**: informații despre sesiunea curentă a utilizatorului (de exemplu, starea de autentificare).
   - **Date temporare mici**: cache-ul rezultatelor API-urilor, preferințele utilizatorilor.

Cache-ul îmbunătățește viteza accesului la date și reduce sarcina asupra principalelor sisteme de stocare. Este recomandat să se evite stocarea unor volume mari de date în cache din cauza limitărilor de memorie.

### 4. **Cloud**

**Stocarea în cloud** este un depozit de date la distanță, oferind stocare în centre de date distribuite global. Cloud-ul oferă scalabilitate, accesibilitate și fiabilitate, fiind o alegere ideală pentru proiectele mari.

- **Tipuri de date care pot fi stocate**:
   - **Volume mari de date**: fișiere media, backup-uri ale bazelor de date, arhive de date.
   - **Baze de date**: datele pot fi stocate în baze de date din cloud (de exemplu, Amazon RDS, Google Cloud SQL).
   - **Stocare de fișiere**: platformele cloud oferă servicii de stocare a fișierelor mari (de exemplu, Amazon S3, Google Cloud Storage).
   - **Backup-uri**: copii ale datelor și configurațiilor sistemului necesare pentru restaurare.

## Cum să alegi soluția de stocare a datelor în aplicațiile web?

Este esențial să înțelegem că, în aplicațiile web, adesea se utilizează o combinație de soluții de stocare pentru diferite tipuri de date. Alegerea unei soluții depinde de caracteristicile datelor care trebuie stocate [^1].

1. **Stocare de fișiere**
   - **Când să o utilizezi?**
     - Pentru stocarea de conținut static, cum ar fi imagini, video, audio.
     - Pentru fișiere care nu necesită procesare complexă, căutare sau modificări frecvente (de exemplu, avatarele utilizatorilor, documente PDF).
   - **Când să nu o utilizezi?**
     - Pentru date structurate care necesită interogări complexe sau modificări frecvente.
     - Pentru date care necesită acces complicat sau frecvent.

2. **Baza de date**
   - **Când să o utilizezi?**
     - Pentru stocarea datelor structurate care necesită procesare și căutare complexă (de exemplu, date despre utilizatori, comenzi, tranzacții).
     - Pentru date care trebuie organizate în tabele și legate între ele (de exemplu, relațiile dintre utilizatori și comenzile lor).
   - **Când să nu o utilizezi?**
     - Pentru volume mari de date nestructurate (de exemplu, fișiere media) care nu necesită procesare complexă și care pot fi stocate mai eficient în alte soluții.
     - Dacă este necesar să stochezi și să procesezi fișiere (de exemplu, imagini) care nu sunt potrivite pentru bazele de date.

3. **Cache**
   - **Când să îl utilizezi?**
     - Pentru a accelera accesul la date frecvent utilizate (de exemplu, rezultatele unor interogări complexe sau datele de sesiune).
     - Pentru date temporare care sunt importante doar pentru o perioadă scurtă de timp (de exemplu, coșul de cumpărături al utilizatorului).
   - **Când să nu îl utilizezi?**
     - Pentru volume mari de date care sunt accesate rar sau nu necesită acces rapid.
     - Pentru date care necesită o precizie ridicată și sunt actualizate frecvent (de exemplu, date financiare care se schimbă constant).

4. **Cloud**
   - **Când să îl utilizezi?**
     - Pentru stocarea unor volume mari de date care necesită scalabilitate, disponibilitate ridicată și acces la distanță (de exemplu, fișiere media, backup-uri).
     - Pentru date care trebuie să fie disponibile la nivel global, cu costuri minime de infrastructură locală.
     - Pentru crearea de backup-uri și arhivarea datelor pentru recuperare în caz de nevoie.
   - **Când să nu îl utilizezi?**
     - Când aplicația ta este mică și nu necesită volume mari de stocare sau o infrastructură complexă.
     - Când este necesar control complet asupra datelor și locației lor fizice (de exemplu, în cazul unor cerințe stricte de securitate).
     - Când este necesară o viteză de acces extrem de rapidă, pe care stocarea locală o poate oferi mai bine decât cloud-ul.

> [!IMPORTANT]  
> Proiectarea soluției de stocare trebuie să înceapă cu analiza datelor care trebuie stocate, nu cu alegerea tehnologiilor. Cu alte cuvinte, datele nu trebuie să depindă de tehnologie, ci tehnologia trebuie aleasă în funcție de caracteristicile datelor.

[^1]: Niu Z, He B. Data Management in Cloud Environments: NoSQL and NewSQL Data Stores. Journal of​ SpringerOpen 2020. doi:10.1186/s13677-020-00200-2.