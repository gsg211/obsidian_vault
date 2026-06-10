
[[LFT_Lab_all_in_one.pdf#page=15&selection=9,1,9,2|LFT_Lab_all_in_one, page 15]]

### Descriere

Metodele reprezintă moduri/stiluri de îmbinare/combinare a unor fraze pentru a descrie mesaje, raţionamente, idei, algoritmi 

 În cazul limbajelor de programare metodelor le corespund combinaţiile de instrucţiuni şi expresii, predefinite sau definite de utilizator în scopul procesării unor date după anumite modele/reguli. Metodele transformă într-o manieră specifică datele de intrare sau modifică unele date externe acestora prin intermediul unui set de reguli în vederea obţinerii unui rezultat, îndeplinirii unui task, efectuării unor operaţii etc

Funcţie de nivelul de procesare pot fi 

- de tip substitutiv (utilizate la nivelul de preprocesare / procesare text ca macro-substituţii)
- de tip secvenţe de coduri care pot fi utilizate ca atare în anumite zone ale programelor, care pot fi împachetate în proceduri sau funcţii, care pot fi ataşate unor structuri/obiecte (operaţii) etc. 

Funcţie de limbaj sau de contextul utilizării acestora, metodele se identifică cu: secvenţele/secţiunile de cod, macro-funcţiile, procedurile, funcţiile, rutinele, subrutinele, subprogramele, programele etc

### Programare

Gestiunea metodelor în cadrul limbajelor presupune efectuarea unor transformări speciale la intrare, ieşire şi la apelul/utilizarea acestora dacă sunt încapsulate/definite şi nu sunt plasate ca atare (ad- hoc) în zona de cod în dorită

Macro-funcţiile se implementează prin stocarea asocierii dintre argumente şi conţinut, asociere ce permite la apel substituirea textuală în conţinutul acestora a argumentelor declarate cu cele de apel şi plasarea textuală a conţinutului rezultat.
### In practica -> stiva

Majoritatea metodelor utilizate în practică sunt însă proceduri şi/sau funcţii (interne),

acestea fiind implementate prin intermediul stivei aplicaţie care stochează temporar (pe durata fiecărui apel) argumentele de apel, adresa de revenire şi variabilele locale. 

La apel, conţinutul argumentelor de apel se salvează în stiva aplicaţiei, într-o anumită ordine, funcţie de tipul apelului (calling convention). 

După plasarea argumentelor în stivă, procesorul salvează în stivă adresa de revenire şi execută un salt către corpul metodei (funcţiei sau procedurii) 

la intrarea căreia se efectuează o serie de operaţii (prologue) precum salvarea unor regiştri, reţinerea nivelul stivei (sp/esp/rsp uzual salvat în bp/ebp/rbp), alocarea în stivă a spaţiului necesar variabilelor locale 

etc. La ieşire (epilogue), o parte dintre operaţiile de intrare sunt inversate după care se face saltul către instrucţiunile ce urmează apelului.

În cazul metodelor externe aplicaţiei, procedeul este similar doar că apelul se efectuează prin invocarea adresei specifice locaţiei acestora (adresa din librăria dinamică sau resursa web în cazul apelului extra sistem).