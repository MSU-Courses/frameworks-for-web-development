# Securitatea în aplicațiile web

**Securitatea în aplicațiile web** reprezintă un set de măsuri menite să protejeze datele, utilizatorii și sistemele împotriva atacurilor rău intenționate. Aplicațiile web moderne procesează date sensibile, precum parole, informații financiare și date personale, iar compromiterea acestora poate avea consecințe grave: pierderi financiare, afectarea reputației și sancțiuni legale.

> [!NOTE]
> Există un curs dedicat securității aplicațiilor web, care acoperă toate aspectele esențiale: de la analiza amenințărilor și vulnerabilităților până la dezvoltarea metodelor de prevenire a acestora. În acest capitol ne vom concentra asupra principalelor aspecte de securitate în cadrul Laravel.

**Aspectele cheie ale protecției datelor**:

- **Confidențialitate**: prevenirea accesului neautorizat la date.
- **Integritate**: protecția datelor împotriva modificării sau distrugerii.
- **Disponibilitate**: asigurarea funcționării corecte a aplicației chiar și în cazul atacurilor.

Lista completă a vulnerabilităților și atacurilor poate fi găsită în [OWASP Top 10](https://owasp.org/www-project-top-ten/). Aceasta este o resursă web care include cele mai frecvente vulnerabilități din aplicațiile web și metodele de prevenire a acestora.

**Cele mai comune amenințări** includ:

- **Injecțiile SQL**, care permit atacatorilor să acceseze baza de date.
- **Scriptarea inter-site (XSS)**, care exploatează lipsa validării corecte a datelor de intrare pentru a executa cod malițios.
- **Atacurile asupra sesiunilor**, precum interceptarea token-urilor de sesiune.
- **Accesul neautorizat la date**, prin care atacatorii obțin acces la informații sensibile.

*Securitatea în aplicațiile web poate fi comparată cu securitatea din viața reală*: dacă ușile nu sunt încuiate, ferestrele sunt deschise, iar alarma nu funcționează, casa devine vulnerabilă la spargeri. În același mod, dacă o aplicație web nu este protejată, aceasta devine o țintă ușoară pentru infractorii cibernetici.