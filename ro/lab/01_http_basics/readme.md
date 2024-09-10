# Lucrare de laborator nr. 1. Bazele HTTP

## Scop

Scopul acestei lucrări de laborator este studierea principiilor de bază ale protocolului HTTP.

## Condiție

### Sarcina nr. 1. Analiza cererilor HTTP

1. Accesați site-ul [http://sandbox.usm.md/login](http://sandbox.usm.md/login).
2. Deschideți fila `Network` în instrumentele pentru dezvoltatori ale browserului.
3. Introduceți date incorecte pentru autentificare (de exemplu, `username: student`, `password: studentpass`).
4. Analizați cererile care au fost trimise către server.
5. Răspundeți la următoarele întrebări:
   - Ce metodă HTTP a fost utilizată pentru a trimite cererea?
   - Ce anteturi au fost trimise în cerere?
   - Ce parametri au fost trimiși în cerere?
   - Ce cod de stare a fost returnat de server?
   - Ce anteturi au fost trimise în răspuns?
6. Repetați pașii 3-5, introducând date corecte pentru autentificare (`username: admin`, `password: password`).

### Sarcina nr. 2. Crearea cererilor HTTP

1. Scrieți o cerere de tip `GET` către server la adresa `http://sandbox.com`, indicând în antetul `User-Agent` numele și prenumele dvs.
2. Scrieți o cerere de tip `POST` către server la adresa `http://sandbox.com/cars`, indicând în corpul cererii următorii parametri:
   - `make: Toyota`
   - `model: Corolla`
   - `year: 2020`
3. Scrieți o cerere de tip `PUT` către server la adresa `http://sandbox.com/cars/1`, indicând în antetul `User-Agent` numele și prenumele dvs., în antetul `Content-Type` valoarea `application/json`, iar în corpul cererii următorii parametri:
   ```json
   {
       "make": "Toyota",
       "model": "Corolla",
       "year": 2021
   }
   ```
4. Scrieți unul dintre posibilele răspunsuri ale serverului la cererea anterioară.
   ```http
   POST /cars HTTP/1.1
   Host: sandbox.com
   Content-Type: application/json
   User-Agent: John Doe
   model=Corolla&make=Toyota&year=2020
   ```
   Presupuneți situațiile în care serverul poate returna codurile de stare HTTP `200`, `201`, `400`, `401`, `403`, `404`, `500`.

### Sarcina nr. 3. Sarcina suplimentară. HTTP_Quest

> [!TIP]
> Parcurgeți quest-ul trimițând cereri către server.

1. Trimiteți o cerere de tip `POST` către server la adresa `http://sandbox.usm.md/quest`, indicând în antetul `User-Agent` numele și prenumele dvs. (De exemplu, `User-Agent: John Doe`).
   ```http
   POST /quest HTTP/1.1
   Host: sandbox.usm.md
   User-Agent: John Doe
   ```
   **curl**:
   ```bash
   curl -X POST http://sandbox.usm.md/quest -H "User-Agent: John Doe"
   ```
2. Urmați instrucțiunile de pe server, îndeplinindu-le în ordine.
3. La finalul quest-ului, vi se va afișa un cuvânt secret, pe care va trebui să-l includeți în raport.

*Notă pentru sarcina nr. 3*:
1. Folosiți instrumentul `curl`, `postman` sau orice alt instrument pentru a trimite cererile.
2. Puteți relua quest-ul executând din nou primul pas.

## Raport

1. Raportul lucrării de laborator trebuie să corespundă [Ghidului pentru redactarea rapoartelor de laborator](../lab_guidelines.md).
2. Raportul trebuie să fie redactat în format `Markdown` și încărcat în depozitul de cod sursă pe Git.