cerinta + rasp -> [[LFT.pdf]]
## 1. 
Expresia C like (8 & 4 + 8 | 2 ^ 8) % 1000b este egală cu?   

1000
1100   &

1000 | 
0010

1010  ^
1000
0010

-> 2

## 2.
Descrieți în fix 5 reguli ABNF formarea expresiei: 
Nume Prenume - grupa 

grupa e de forma 1311 A

```
1. nume-prenume-grupa = nume SP prenume SP "-" SP grupa
2. nume = 1*ALPHA
3. prenume = 1*ALPHA
4. grupa = 4DIGIT SP ALPHA
5. SP = %x20 ; Spatiu

```

nu stiu daca e corect
## 3.
Automatele sunt:
- [ ] Simboluri
- [ ] Literali
- [ ] Expresii
- [ ] Fraze
- [ ] Metode
- [V] Structuri
- [ ] Informații
- [ ] Date
- [ ] Reguli
- [ ] Procese
- [v] Sisteme


## 4.
Translati octetii UTF-8 232 148 165 in reprezentarea Unicode standard


U+8525


## 5.
Gramatica 

G = <{Y, X}, {z, y, x}, {X->xXY|xyz, yY->yyz, zY->Yz}, X> este de tip:
- 0
- 1
- 2
- 3

Uită-te la fiecare regulă:

X → xXY — un singur neterminal în stânga. Tip 2.

X → xyz — un singur neterminal în stânga. Tip 2.

yY → yyz — y rămâne neatins în stânga, Y se rescrie ca yz în contextul lui y. Tip 1.

zY → Yz — aici z și Y își schimbă locurile între ele. Nu poți scrie asta ca αAβ → αγβ cu contextul neatins, pentru că z din stânga dispare din poziția lui. Exact ca exemplul cu cB → Bc discutat mai devreme.

---

Deci răspunsul e tip 0, din cauza regulii zY → Yz care nu respectă forma tip 1.

## Important

Pentru subiectele urmatoare se vor utiliza notatiile: a = 5, b = 5. Raspunsurile validabile la subiectele urmatoare nu trebuie sa contina simbolurile a si b ci valorile 5 si 5. Se va avea in vedere exactitatea, rigoarea si parsabilitatea raspunsurilor. Astfel, se vor puncta doar raspunsurile exacte/parsabile si anula cele inconsistente, incomplete, nuantate, necompacte, neriguroase, nestandard, comentate, explicate etc.

## 6.
Descrieți analitic automatul din figura de mai jos

![[Pasted image 20260610132835.png]]

inceput: A= <{ .... restu intauntru....}>

Stari:  {B,C}
Alfabet: {a,b}  => {5,5}  (a, b se inlocuiesc cu 5)
Tranzitii:  {(B,b)=C, (C,a)=B} = {(B,5)=C, (C,5)=B}
//fara delta
Init: B
Final: {C}  //trb sa fie multime

A={{BC},{5,5}, {(B,5)=C, (C,5)=B}, B, {C}}


 
## 7.
Descrieți formula generală a limbajului acceptat de automat 
(Ex: x^(2*n+1)..)

 b(ab)\^n  => 5(55)\^n 
## 8.
Descrieți analitic masina din figura de mai jos


![[Pasted image 20260610132903.png]]
u v simboluri auxiliare


- inceput: M= <{ .... restu intauntru....}>

- stari: {B,C}

- alfabet lucru: {a,b,u,v,s} 
// tot ce e rosu

=> {5,u,v, s} //e multime deci doar un 5.

terminator: s

alabetul de intrare: {a,b} => {5}

tranzitii:

(B,a)=(B,u,R)
(B,b)=(C,v,R)
(C,s)=(C,s,N)

statea initiala: B
starile finale: {C}

=>
M=<{B,C},{5,u,v,s},s,{5},{5,u,v,s},{(B,5)=(B,u,R),(B,5)=(C,v,R),(C,s)=(C,s,N)},B,{C}>

| Element Formal | Denumire componentă      | Ce conține (din graf)                         | Ce conține final (cu 5 în loc de a,b)         |
| -------------- | ------------------------ | --------------------------------------------- | --------------------------------------------- |
| Q              | Mulțimea stărilor        | {B, C}                                        | {B, C}                                        |
| Γ              | Alfabetul de lucru       | {a, b, u, v, s}                               | {5, u, v, s}                                  |
| B              | Simbolul blank           | s                                             | s                                             |
| Σ              | Alfabetul de intrare     | {a, b}                                        | {5}                                           |
| δ              | Funcția de tranziție     | {(B,a)=(B,u,R), (B,b)=(C,v,R), (C,s)=(C,s,N)} | {(B,5)=(B,u,R), (B,5)=(C,v,R), (C,s)=(C,s,N)} |
| q0             | Starea inițială          | B                                             | B                                             |
| F              | Mulțimea stărilor finale | {C}                                           | {C}                                           |

## 10.
enumerați cele mai compacte 5 translări explicite valide efectuate de masină (Ex: 12=>pq, 112=>ppq, ..)

> [!quote]+ explicatie
> Forma cerută de profesor exprimă **funcția de calcul (translarea șirului de intrare în șirul de ieșire)** realizată de mașină după oprirea în starea finală $C$.
> 
> Formatul folosit este `intrare=>ieșire`. Iată explicația pas cu pas pentru cele 5 translări:
> 
> - **Mașina acceptă doar șiruri care se termină cu caracterul din tranziția spre $C$** (adică fostul $b$, devenit $5$). Acest ultim $5$ este înlocuit întotdeauna cu **`v`**.
>     
> - **Orice număr de caractere $5$ citite anterior** pe bucla stării $B$ (fostul $a$, devenit $5$) sunt înlocuite succesiv cu **`u`**.
>     
> - Deoarece ambele caractere inițiale ($a$ și $b$) au devenit $5$, intrările sunt șiruri formate doar din cifre de $5$, iar ieșirile arată transformarea lor progresivă de la stânga la dreapta.
>     
> 
>  Analiza fiecărui caz în parte:
> 
> 1. **`5=>v`**
>     
>     - **Ce se întâmplă:** Mașina primește un singur $5$ (corespunzător lui $b$). Aplică direct tranziția spre starea finală: îl înlocuiește cu `v` și se oprește.
>         
> 2. **`55=>uv`**
>     
>     - **Ce se întâmplă:** Primul $5$ (corespunzător lui $a$) este citit pe bucla stării $B$ și devine `u`. Al doilea $5$ (corespunzător lui $b$) face saltul în starea finală și devine `v`.
>         
> 3. **`555=>uuv`**
>     
>     - **Ce se întâmplă:** Primele două cifre de $5$ sunt procesate pe bucla stării $B$, devenind `uu`. Ultima cifră de $5$ face trecerea în $C$ și devine `v`.
>         
> 4. **`5555=>uuuv`**
>     
>     - **Ce se întâmplă:** Primele trei cifre de $5$ sunt transformate în `uuu` pe buclă, iar al patrulea $5$ devine `v` la oprirea în starea finală.
>         
> 5. **`55555=>uuuuv`**
>     
>     - **Ce se întâmplă:** Primele patru cifre de $5$ sunt înlocuite cu `uuuu`, iar ultima componentă devine `v`.

## ALT EX CU MASINI

![[Pasted image 20260611000727.png]]


Iată tabelul completat pentru noua mașină Turing din imaginea 28, unde $a = 2$ și $b = 1$:

|**Element Formal**|**Denumire componentă**|**Ce conține (din graf)**|**Ce conține final (cu 2, 1 în loc de a, b)**|
|---|---|---|---|
|**Q**|Mulțimea stărilor|$\{A, B\}$|**$\{A, B\}$**|
|**Γ**|Alfabetul de lucru|$\{a, b, x, y, 1\}$|**$\{2, 1, x, y\}$**|
|**B**|Simbolul blank|$1$|**$1$**|
|**Σ**|Alfabetul de intrare|$\{a, b\}$|**$\{2, 1\}$**|
|**δ**|Funcția de tranziție|$(B, a) = (B, x, R)$<br><br>  <br><br>$(B, b) = (A, y, N)$|**$\{(B, 2) = (B, x, R),$**<br><br>  <br><br>**$(B, 1) = (A, y, N)\}$**|
|**q0**|Starea inițială|$B$|**$B$**|
|**F**|Mulțimea stărilor finale|$\{A\}$|**$\{A\}$**|

### Explicația rezultatului pentru Subiectul 29 (Cele 5 translări)

Forma cerută reprezintă modul în care un șir introdus în mașină este transformat în șirul final (`intrare=>ieșire`) în momentul în care se ajunge în starea finală de acceptare $A$.

Din graful mașinii observăm regulile de transformare:

- Orice caracter **$a$** (devenit **$2$**) citit pe bucla stării inițiale $B$ este înlocuit cu **$x$**, iar capul de citire se deplasează la dreapta ($R$).
    
- Caracterul **$b$** (devenit **$1$**) este cel care face saltul în starea finală $A$. El este înlocuit cu **$y$**, iar mașina se oprește fără să se mai deplaseze ($N$).
    

Prin urmare, ca un șir să fie validat și complet transformat, el trebuie să se termine obligatoriu cu cifra $1$ (fostul $b$). Analiza celor 5 cazuri compacte arată așa:

1. **`1=>y`**
    
    - Mașina citește direct $1$ (b). Aplică tranziția spre $A$, îl transformă în $y$ și se oprește în starea finală.
        
2. **`21=>xy`**
    
    - Primul caracter este $2$ (a). Pe bucla din $B$, acesta devine $x$. Următorul este $1$ (b), care devine $y$ la trecerea în starea finală $A$.
        
3. **`221=>xxy`**
    
    - Primele două caractere de $2$ (a) sunt procesate pe buclă și devin $xx$. Ultimul caracter, $1$ (b), devine $y$.
        
4. **`2221=>xxxy`**
    
    - Cei trei de $2$ de la început sunt transformați succesiv în $xxx$, iar $1$ de la final devine $y$.
        
5. **`22221=>xxxxy`**
    
    - Cei patru de $2$ devin $xxxx$, iar terminatorul de secvență $1$ devine $y$.