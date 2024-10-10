# Gestionarea datelor și impactul asupra performanței

**Gestionarea eficientă a datelor** este unul dintre aspectele esențiale care influențează în mod direct performanța aplicațiilor web. Optimizarea modului în care datele sunt gestionate poate îmbunătăți semnificativ timpul de răspuns al sistemului, de multe ori crescând performanța de zeci sau chiar sute de ori.

## Cum să gestionezi eficient datele într-o aplicație?

### Citirea și scrierea datelor în baza de date

**Operațiile de citire și scriere în baza de date** sunt printre cele mai lente procese dintr-o aplicație web. Interogările frecvente către baza de date pot încetini considerabil sistemul, în special atunci când există un volum mare de cereri [^1].

Pentru a îmbunătăți performanța, este important să:

- Optimizezi interogările bazei de date.
- Utilizezi indecși pentru a accelera căutarea datelor.
- Eviți operațiunile de scriere inutile.

### Cache-ul datelor

**Cache-ul** este un mecanism puternic care permite stocarea temporară a rezultatelor interogărilor sau a calculelor, astfel încât să fie reutilizate fără a accesa din nou baza de date. Cache-ul reduce semnificativ sarcina asupra bazei de date și accelerează procesarea datelor pentru utilizatori.

Poți folosi cache-ul pentru:

- Stocarea rezultatelor interogărilor la baza de date.
- Salvarea rezultatelor intermediare ale calculelor.
- Cache-ul datelor care pot fi reutilizate în aplicație.

### Tranzacțiile în baza de date

Tranzacțiile joacă un rol esențial în asigurarea integrității datelor. Ele permit executarea mai multor operațiuni ca un întreg, garantând că datele nu vor fi pierdute sau actualizate incorect în cazul unei erori.

Deși tranzacțiile sunt importante pentru fiabilitatea datelor, ele pot încetini performanța, deci trebuie utilizate cu atenție.

#### Ce este o tranzacție?

**Tranzacția** este un set de operațiuni care se execută ca un singur bloc. Dacă una dintre operațiuni eșuează, toate acțiunile anterioare din tranzacție sunt anulate, iar sistemul revine la starea inițială [^3].

Exemplu: aplicație de vânzare bilete.

- Utilizatorul selectează biletele și introduce datele cardului.
- Aplicația rezervă biletele și debitează suma din cont.
- După plată, biletele sunt scăzute din stocul total.
- Informațiile despre comandă sunt salvate în baza de date.

Operațiile sunt:

- **Rezervarea biletelor**:
  ```sql
  UPDATE tickets SET reserved = true WHERE id = 1;
  ```
- **Debitarea banilor**:
  ```sql
  UPDATE users SET balance = balance - 100 WHERE id = 1;
  ```
- **Scăderea numărului de bilete disponibile**:
  ```sql
  UPDATE events SET tickets = tickets - 1 WHERE id = 1;
  ```
- **Salvarea comenzii**:
  ```sql
  INSERT INTO orders (user_id, event_id, tickets) VALUES (1, 1, 1);
  ```

Dacă baza de date se întrerupe după debitare, dar înainte de scăderea biletelor, utilizatorul ar pierde banii, dar nu ar primi biletele.

Pentru a evita astfel de situații, toate aceste operațiuni trebuie executate într-o singură tranzacție.

Exemplu de tranzacție SQL:

```sql
BEGIN TRANSACTION;

UPDATE tickets SET reserved = true WHERE id = 1;
UPDATE users SET balance = balance - 100 WHERE id = 1;
UPDATE events SET tickets = tickets - 1 WHERE id = 1;
INSERT INTO orders (user_id, event_id, tickets) VALUES (1, 1, 1);

COMMIT;
```

### Gestionarea sarcinilor de lungă durată [^4]

Sarcinile de lungă durată, cum ar fi:
- procesarea volumelor mari de date,
- trimiterea notificărilor,
- generarea de rapoarte,

pot încetini funcționarea aplicației și afecta negativ experiența utilizatorului.

De exemplu, la înregistrarea unui utilizator nou, trimiterea unui email de confirmare poate dura și poate întârzia finalizarea procesului de înregistrare.

Pentru a evita aceste întârzieri, astfel de sarcini ar trebui gestionate în fundal, separat de procesul principal al aplicației.

Acest lucru permite finalizarea rapidă a operațiunilor esențiale, în timp ce sarcinile suplimentare sunt executate în fundal, fără a afecta performanța generală a sistemului.

**De exemplu**, la înregistrarea utilizatorului, trimiterea email-ului de confirmare poate fi gestionată de un proces de fundal. **Utilizatorul va vedea imediat că înregistrarea a fost finalizată**, iar **email-ul va fi trimis ulterior, fără a necesita așteptarea utilizatorului**.

[^1]: Ramu VB. Optimizing Database Performance: Strategies for Efficient Query Execution and Resource Utilization. International Journal of Computer Trends and Technology. 2023;71(7):15-21.

[^2]: Selvaraj S. Advanced Database Techniques and Optimization. Building Real-Time Marvels with Laravel. Springer, Berkeley, CA. Published 2023. doi:10.1007/978-1-4842-9789-6_17.

[^3]: Sippu S, Soisalon-Soininen E. Transaction Processing: Management of the Logical Database and its Underlying Physical Structure. SpringerLink. Published 2013. doi:10.1007/978-0-387-39940-9_731.

[^4]: Tasks for the non-data software engineer. perfect.io [online]. URL: https://www.prefect.io/blog/background-tasks-why-they-matter-in-prefect