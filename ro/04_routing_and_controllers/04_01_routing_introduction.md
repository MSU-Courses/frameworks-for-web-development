## Rutarea în aplicațiile web

Înainte de a începe să studiem **rutarea în aplicațiile web**, să vedem ce înseamnă acest concept și de ce este important.

Ce îți vine în minte când auzi cuvântul „_rută_”? Probabil te gândești la călătorii, drumuri, trasee de transport sau direcții. De exemplu, există traseul de troleibuz nr. 22, care merge din punctul `A` în punctul `B`.

În general, într-o aplicație web, o rută este același lucru: un drum de la URL-ul utilizatorului către o anumită acțiune sau conținut din aplicație.

## Ce este rutarea?

În aplicațiile web, **rutarea** reprezintă definirea căilor pe care utilizatorul le poate urma în interiorul aplicației [^1].

În funcție de ruta aleasă de utilizator, aplicația afișează conținutul corespunzător sau execută anumite acțiuni (realizează o funcție specifică).

Să luăm un exemplu pentru o mai bună înțelegere. Imaginează-ți că ai o aplicație web cu două pagini: `Acasă` și `Despre noi`. Utilizatorul poate naviga între aceste pagini făcând clic pe linkurile respective. În acest caz, `Acasă` și `Despre noi` sunt rute.

Cum ajunge utilizatorul pe pagina `Despre noi`? El accesează ruta definită pentru această pagină. În acest caz, ruta este adresa URL a paginii `Despre noi`, de exemplu, `/about`.

## De ce este necesară rutarea?

Rutarea în aplicațiile web este esențială pentru a oferi utilizatorilor o experiență mai plăcută și mai simplă.

Datorită rutării, utilizatorul poate naviga cu ușurință în cadrul aplicației, poate trece între diverse pagini și executa acțiuni diferite.

Dacă nu ar exista rutare în aplicațiile web, ar fi mai dificil pentru utilizator să găsească informațiile necesare sau să îndeplinească anumite acțiuni. De exemplu, nu ar putea trimite un link către un articol interesant unui prieten sau să acceseze rapid pagina de contact.

## Cum funcționează rutarea?

Există numeroase mecanisme de rutare în aplicațiile web, care pot varia în funcție de framework-ul sau biblioteca utilizată.

### Algoritmul de rutare

1. **Definirea rutelor în aplicație**

    - Rutele sunt stabilite prin specificarea adreselor URL și a metodelor HTTP (`GET`, `POST`, etc.) care sunt asociate cu anumite funcții. Aceste funcții efectuează acțiunile necesare și returnează rezultatul. _De exemplu_:
        - `GET /` — pagina principală (metoda `index()`),
        - `GET /about` — pagina „Despre noi” (metoda `about()`),
        - `GET /contact` — pagina de contact (metoda `contact()`),
        - `POST /product` — creare produs nou (metoda `createProduct()`),
        - `GET /product/{id}` — pagina produsului după ID (metoda `getProduct(int $id)`).

2. **Accesarea rutei de către utilizator**

    - Utilizatorul introduce adresa URL în browser sau face clic pe un link, declanșând o cerere HTTP către server.

3. **Căutarea funcției asociate cu ruta**

    - Aplicația caută ruta corespunzătoare adresei URL și metodei HTTP specificate. De exemplu, dacă cererea a fost trimisă către `/about` cu metoda `GET`, aplicația găsește metoda `about()`.
    - Dacă ruta potrivită nu este găsită, aplicația returnează o pagină de eroare 404.

4. **Executarea acțiunii și afișarea rezultatului**
    - După ce ruta este găsită, se execută metoda corespunzătoare, iar rezultatul (de exemplu, o pagină HTML sau un răspuns JSON) este transmis utilizatorului.

> [!NOTE]
> Acest algoritm este general și poate varia în funcție de framework-ul sau biblioteca utilizată.

[^1]: What Is a Router in Web Development? oyova [online]. URL: https://www.oyova.com/blog/what-is-a-route-web-dev/