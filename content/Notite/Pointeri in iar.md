
Un pointer trebuie să conţină informaţia necesară pentru a specifica locaţia unui anumit tip de memorie. Acest fapt denotă că dimensiunile pointerilor sunt diferite pentru diferite tipuri de memorie. În IAR C/C++ Compiler For AVR este interzisă conversia pointerilor de tipuri diferite fără utilizarea unui cast explicit.

### Pointeri la functii

16 sau 24 de biţi, iar aceștia pot adresa întreaga memorie


Reprezentarea internă a unui pointer la funcţie este adresa de la care începe funcţia împărţită la 2

- Deoarece o instrucțiune nu poate începe niciodată la o adresă impară (1, 3, 5...), bitul cel mai puțin semnificativ (LSB) al adresei de memorie Flash ar fi întotdeauna 0

- Dacă bitul este întotdeauna 0, este redundant (inutil) să îl stocăm. Prin eliminarea lui (ceea ce matematic înseamnă deplasare la dreapta cu un bit sau împărțire la 2), transformăm "adresa de octet" în adresă de cuvânt" (word address)

![[Pasted image 20260130003853.png]]
![[Pasted image 20260130003902.png]]

