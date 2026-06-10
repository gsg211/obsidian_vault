# Lab 11 - Gramatici Formale și Ierarhia Chomsky

[[LFT_Lab_all_in_one.pdf#page=21&selection=9,0,9,8|LFT_Lab_all_in_one, page 21]]

[[Lab 11 explicat]]

![[Pasted image 20260609202642.png]]

## Descriere

O gramatică G reprezintă o colecţie formată din mulţimi de simboluri şi reguli de combinare ale acestora aferente descrierii sau transmiterii unor informaţii, adică este definită de cvadruplul (4-uplul):

$$
G = < N, Σ, P, S>
$$

N={A, B, C, ..} -> alfabetul simbolurilor neterminale
Σ={a, b, c, ..} ->  alfabetul simbolurilor terminale
A= N∪Σ={U, V, X, Y, ..} alfabetul gramaticii G
λ sau ε şirul vid (empty string)
S simbolul de start

Σ*={u, v, x, ..} mulţimea tuturor combinaţiilor simbolurilor terminale Σ
A*={α, β, γ, δ, ..} mulţimea tuturor cuvintelor

α → β sau (α, β) o regulă/producţie
P={α → β | α, β ∈A* } mulţimea producţiilor

α=>β regulă de derivare generala: β generat conform regulilor P plecând de la şirul α.

Limbajul generat de gramatica G se notează L(G)={x | S ⇒ x}
cu x şir derivat din simbolul de start S (formă propoziţională)

> Explicatie: O gramatică este practic un set de reguli prin care poți construi cuvinte/propoziții valide. Gândește-te la ea ca la o rețetă: pleci de la simbolul de start S și aplici regulile din P până ajungi la un șir format doar din simboluri terminale (litere concrete, fără majuscule). Tot ce poți genera astfel formează limbajul L(G).
> 
> Simbolurile neterminale (N) sunt "variabile" intermediare — ele se mai pot transforma. Simbolurile terminale (Σ) sunt "finale" — nu se mai transformă în altceva. Σ* înseamnă toate combinațiile posibile de terminale, inclusiv șirul vid.

---

## Ierarhia Chomsky

Clasificarea/Ierarhia Chomsky stabileşte tipurile de gramatici relevante, după cum urmează:

- Gramatici de tip 0 – $G^0$ nerestricţionate / generale (recursiv enumerabile): α → β

- Gramatici de tip 1 – $G^1$ dependente de context:
  αAβ → αγβ, A transcris cu γ ≠ λ în contextul α .. β (monotone |A| ≤ |γ|) sau S → λ dacă S ⊄ αγβ.

- Gramatici de tip 2 – $G^2$ independente de context (necontextuale):
  A → γ, A transcris cu γ

- Gramatici de tip 3 – $G^3$ regulate/liniare:
  A → a sau A → aB (dreapta) / A → Ba (stânga)

În cadrul acestei clasificări, între gramatici există relaţia $G^3 ⊂ G^2 ⊂ G^1 ⊂ G^0$.

Fiecare tip de gramatică generează corespunzător un tip de limbaj.

> Explicatie: Ierarhia Chomsky este o clasificare a gramaticilor de la cele mai restrictive (tip 3) la cele mai generale (tip 0). Cu cât tipul e mai mic, cu atât gramatica e mai puternică — poate descrie limbaje mai complexe, dar e și mai greu de procesat de un calculator.
>
> Tip 3 (regulate): cele mai simple. Regulile au mereu o formă fixă: un neterminal produce un terminal urmat opțional de un neterminal. Acestea sunt recunoscute de automate finite (de ex. expresii regulate).
>
> Tip 2 (independente de context): un singur neterminal se poate transforma în orice șir. Sunt folosite pentru limbajele de programare. Recunoscute de automate cu stivă.
>
> Tip 1 (dependente de context): transformarea unui simbol depinde de vecinii săi (context). Mai puternice, dar mai rare în practică.
>
> Tip 0 (nerestricționate): orice regulă e permisă. Echivalente cu Mașina Turing — pot descrie orice calcul computabil.

---

### Limbaje

- Limbaje de tip 1 – L1=L(G1) dependente de context, descrise de maşini Turing nedeterministe

- Limbaje de tip 2 – L2=L(G2), independente de context nedeterministe (limbaje de programare), descrise de automate cu stivă (pushdown)

- Limbaje de tip 3 – L3=L(G3), regulate/liniare, descrise de automate finite

Între aceste categorii de limbaje există evident relaţia: $L^3 ⊂ L^2 ⊂ L^1 ⊂ L^0$

> Explicatie: Fiecare tip de gramatică corespunde unui tip de limbaj și unui tip de "mașinărie" care îl recunoaște. Această relație de incluziune înseamnă că orice limbaj regulat (tip 3) este și independent de context (tip 2), dar nu și invers. De exemplu, limbajul {anbn} (n de 'a' urmat de n de 'b') nu poate fi descris de o gramatică de tip 3, dar poate fi descris de una de tip 2.

---

### Exemple

#### Exemplu de gramatică de tip 0:

G = 〈{A, B}, {a, b, c}, {A→aAB|abc, bB→bbc, cB→Bc}, A〉

în care
- N = {A, B}
- Σ = {a, b, c}
- P = {A→aAB|abc, bB→bbc, cB→Bc}
- S = A

şi care generează limbajul L = {anbncn| n≥1} = {abc, aabbcc, ..}

![[Pasted image 20260609203357.png]]

> Explicatie: Aceasta gramatică de tip 0 generează șiruri cu același număr de 'a', 'b' și 'c'. Regulile nu au restricții de formă — de exemplu bB→bbc înlocuiește doi simboli cu trei, ceea ce nu ar fi permis în tipurile mai restrictive. Tipul 0 e cel mai liber, dar și cel mai greu de analizat automat.

---

#### Exemplu de gramatică de tip 1:

G = 〈{A, B, C, D, E}, {a, b, c},
{A→aAB|abC, CB→DB, DB→DE, DE→BE, BE→BC, bB→bbc, C→c}, A〉

care generează de asemenea limbajul
L = {anbncn| n≥1} = {abc, aabbcc, ..}

> Explicatie: Spre deosebire de exemplul tip 0, regulile de tip 1 sunt "monotone" — lungimea șirului nu scade niciodată în urma aplicării unei reguli (|stânga| ≤ |dreapta|). Transformarea unui simbol depinde de ce îl înconjoară (context). Observă că același limbaj {anbncn} se poate descrie și cu tip 0 și cu tip 1 — tipul 1 e însă mai controlat.

---

#### Exemplu de gramatică de tip 2:

G = 〈{S}, {a, b}, {S→aSb|ab}, S〉

care generează limbajul L = {anbn| n≥1} = {ab, aabb, aaabbb, ..}

> Explicatie: Aceasta este o gramatică clasică independentă de context. Regula S→aSb spune: pune un 'a' în față și un 'b' în spate, recursiv. Regula S→ab oprește recursivitatea. Rezultatul sunt șiruri cu n litere 'a' urmate exact de n litere 'b'. Stiva unui automat pushdown "ține minte" câte 'a'-uri s-au văzut, ca să verifice că sunt tot atâtea 'b'-uri.

---

#### Exemplu de gramatică de tip 3:

G = 〈{S, A, B}, {a, b}, {S→aA, A→aA| bB, B→bB|λ}, S〉

care generează limbajul L = {ambn| m, n ≥1} = {ab, aab, abb, aabb, ..}

> Explicatie: Gramatica de tip 3 (regulată) are reguli de forma A→aB sau A→a — mereu un terminal urmat de cel mult un neterminal, toate "spre dreapta". Aceasta corespunde direct unei expresii regulate: a+b+ (unul sau mai mulți de 'a', urmat de unul sau mai mulți de 'b'). Simbolul λ (șirul vid) din B→bB|λ înseamnă că B poate să dispară, terminând derivarea.

---

## Forma normala (pentru tip 2)

O gramatică de tip 2 (independentă de context) este în formă normală de mai multe tipuri:

#### Chomsky

Dacă regulile sale sunt de tipul: A→BC, A→a, S→λ.

Caracteristica este utilă în etapa de preprocesare (procesare textuală) de tip bottom-up (bottom-up/LR parsing), deoarece permite coborârea mai întâi până la nivelul fundamental/bottom (caracter / terminal) după care se procesează extinderi (up) ale structurilor mai complexe (incluzive).

> Explicatie: Forma normală Chomsky (CNF) impune că fiecare regulă produce exact doi neterminali (A→BC) sau un singur terminal (A→a). Orice gramatică de tip 2 poate fi convertită în CNF fără a schimba limbajul generat. Este utilă pentru algoritmul CYK de parsing, care verifică eficient dacă un șir aparține unui limbaj.

---

#### Greibach

Dacă regulile sale încep cu un simbol terminal, adică sunt de tipul A→aα, S→λ.

Regulile gramaticii de tip Greibach nu sunt stâng recursive.

Caracteristica este utilă în etapa de procesare de tip top-down (top-down/LL parsing) asigurând oprirea după maxim n (G-stringuri) paşi.

> Explicatie: Forma normală Greibach (GNF) garantează că fiecare regulă începe cu un terminal. Asta elimină recursivitatea la stânga (A→Aα), care ar cauza bucle infinite în parsere top-down. Un parser LL citește intrarea de la stânga la dreapta și construiește arborele sintactic de sus în jos — GNF face asta posibil și eficient.

---

#### Operator

Dacă regulile sale nu conţin consecutiv două simboluri neterminale, adică sunt de tipul A→..BaC...

Regulile gramaticii în forma normală Operator permit ancorarea expresiilor (prin intermediul simbolurilor terminale separatoare de tip a) corespunzând gramaticilor expresiilor (aritmetice) sau de precedenţă.

> Explicatie: Gramaticile în formă normală Operator sunt folosite pentru a descrie expresii aritmetice unde operatorii (+, -, *, /) separă întotdeauna operanzii. Regula că nu pot exista doi neterminali consecutivi reflectă tocmai faptul că între doi operanzi există mereu un operator. Sunt la baza analizei expresiilor cu precedență (ex: 3 + 4 * 2).

---

#### Kuroda

Dacă regulile sale sunt de tipul: AB→CD, A→BC|B|a/λ.

Regulile gramaticii în forma normală Kuroda corespund gramaticilor monotone (monotonic/linear bounded/noncontracting grammar).

Dacă C≡A gramatica devine de tip 1 (dependentă de context), numită şi formă normală Penttonen.

> Explicatie: Forma normală Kuroda generalizează CNF pentru gramatici de tip 1 (dependente de context). Permite și reguli care transformă perechi de neterminali (AB→CD), nu doar unul singur. "Monotonă" înseamnă că regulile nu scurtează niciodată șirul — proprietate esențială pentru tipul 1. Forma Penttonen este un caz special mai restrictiv, echivalent cu gramaticile dependente de context clasice.