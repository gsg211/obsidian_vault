de la cei de dinainte => atentie ca sunt atmega16

> [!danger]
> Trebuie sa stiti ce face fiecare linie cod. 
> Daca nu stiti a explica detaliile din cod, chiar daca acesta merge in totalitate, esti picat.


# 1
La problemele de determinare a frecventei/ perioadei/valoarea tipica ca watchdog-ului, 

trebuie sa stiti a lamuri fiecare tip de date:
unsigned long, 1000000L de ce se pune L (pentru a-l casta la tip long pe 4 octeti, pentru ca pe 2 octeti val maxim e 65536)

Pe scurt trebuie sa stiti range-urile la tipurile de date, de ce inmultim cu 1000000 de exemplu si ce unitate de masura se obtine rezultatul.

# 2
La problemele cu factor de umplere folosind watchdog, atentie la factorul de umplere. Daca avem factor de umplere > 50 % folosind starile, trebuie sa alegem la iesire pinii care au

<span style="background:#ff4d4f">pull-up activ extern, pentru ca la reset, acestea sa aiba valoarea 1</span>
 
Doar 2 pini satisfac acest criteriu si acestia sunt PC0 si PC1.

Daca factorul de umplere e mai mic de 50 % de obicei se pune orice alt pin, in functie de problema si de numarul de stari.

# 3 
 La problemele cu numararea perioadei de reset extern a placutei, va veni si va apasa cu viteza , si o sa dea perioada foarte mica daca nu tii cont de fenomenul de debouncing. 

> [!tip] Tip
>  
>  (Din AVR Studio trebuie setat timpul de delay la reset de vreo 200 de ms).


 La problema cu schimbarea factorului de umplere folosind timere avem 2 metode:
- folosind 1 timer
- folosind 2 timere (asa cum am facut in subiecte rezolvate)

Daca folosesti 1 timer, s-ar putea sa te intrebe care mod e mai de bun simt: 14(Top = ICR1) sau 15(TOP = OCR1A). Raspunsul e 14 cu ICR1.

---

Promovabilitate: ~15-20 % 


![[Pasted image 20260130013107.png]]