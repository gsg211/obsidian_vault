
[[LFT_Lab_all_in_one.pdf#page=17&selection=9,2,9,9|LFT_Lab_all_in_one, page 17]]

### Descriere


Structurile lingvistice reprezintă descrieri detaliate/complete ale unor informaţii prin intermediul unui limbaj. 

Structurile informaţionale reprezintă colecţii de date şi/sau metode implementate de către limbaje (tablouri, înregistrări, uniuni, variant, proprietăţi, obiecte) sau de către utilizatori (vectori, matrice, tensori, mulţimi, stive, cozi, liste, dicţionare, arbori) în vederea descrierii şi procesării unitare a informaţiilor cu caracteristici similare în cadrul unor suprastructuri numite programe.

### Tipuri de structuri

alea de baza: [[LFT_Lab_all_in_one.pdf#page=17&selection=56,74,122,62|LFT_Lab_all_in_one, page 17]]

- Structurile de tip înregistrare/articol (record, tuple, struct) reprezintă colecţii de date (numite câmpuri/componente/membri) de tipuri diferite (agregate/neomogene) sau de acelaşi tip, identificate prin nume

- de tip variant (tagged union, discriminated union, disjoint union) reprezi
- 
- ntă colecţii de date diferite identificabile distinct în mod dinamic, la momente diferite, gestiunea acestora în cadrul limbajelor presupunând ataşarea acestora a unor extra informaţii precum identificatorul de tip etc. 

explicatie :
Spre deosebire de o înregistrare normală (`struct`), unde toate câmpurile există în memorie în același timp, o structură `variant` folosește o zonă de memorie partajată. Ea "își schimbă forma" în mod dinamic.

Diferența cheie dintre un **Variant** și un **Union** este **siguranța în utilizare** (sau _Type Safety_).

Un **Union** este o structură „orbă”, la nivel de hardware, în timp ce un **Variant** este o structură inteligentă, controlată de translator (compilator), care știe în orice moment ce tip de date conține.


$$\text{Variant} = \text{Union} + \text{Tag (Indicator de Tip)} + \text{Mecanisme de Protecție}$$
basically union pe steroizi

- de tip proprietate (property) reprezintă tipul de dată cu gestiune distinctă funcţie de tipul de accesare (citire/scriere), fiecare tip de accesare este intermediat de câte o metodă (get/set). -> vezi getters setters in c#
- Structura de tip obiect (object, class) reprezintă colecţii de date şi metode, cu diferite niveluri de accesibilitate şi moştenire