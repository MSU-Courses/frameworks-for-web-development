# Autentificare și autorizare: care este diferența și de ce sunt necesare

**Autentificarea** este procesul de verificare a identității unui utilizator. Aplicația confirmă că utilizatorul este exact persoana pe care pretinde că o reprezintă. Acesta este primul pas în protejarea datelor și prevenirea accesului neautorizat. **Exemplu**: utilizatorul introduce adresa de e-mail și parola pentru a accesa contul său personal.

**Autorizarea** este procesul de determinare a acțiunilor sau resurselor la care utilizatorul are acces după ce a fost autentificat cu succes. Autorizarea limitează accesul la diverse funcționalități ale aplicației pe baza rolurilor sau permisiunilor utilizatorului. **Exemplu**: un utilizator autorizat cu rolul „Administrator” poate edita conținutul, în timp ce un utilizator cu rolul „Vizitator” poate doar să vizualizeze.

Autentificarea și autorizarea sunt procese strâns legate. Mai întâi, aplicația verifică cine a intrat în sistem (autentificare), iar apoi stabilește ce poate face acel utilizator (autorizare).

Dacă facem o analogie cu lumea reală, autentificarea este ca verificarea unui pașaport, iar autorizarea este echivalentă cu obținerea unei autorizații de acces într-o clădire. Pașaportul confirmă identitatea dumneavoastră, în timp ce autorizația specifică ce zone ale clădirii puteți vizita.

Pe scurt, autentificarea răspunde la întrebarea "**Cine sunteți?**", iar autorizarea răspunde la întrebarea "**Ce aveți voie să faceți?**". Împreună, acestea asigură securitatea datelor și controlul accesului în aplicație.

În cadrul acestui capitol vom analiza detaliat aceste procese în contextul dezvoltării web și utilizării framework-urilor web.