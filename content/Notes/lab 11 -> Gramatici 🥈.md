
[[LFT_Lab_all_in_one.pdf#page=21&selection=9,0,9,8|LFT_Lab_all_in_one, page 21]]

> [!important] IMPORTANT
> vezi [[Lab 11 explicat]]


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
cu x şir derivat din simbolul de start S (formă propoziţională

## Ierarhia Chomsky


Clasificarea/Ierarhia Chomsky stabileşte tipurile de gramatici relevante, după cum urmează: 

•Gramatici de tip 0 – $G^0$ nerestricţionate / generale (recursiv enumerabile): α → β 

• Gramatici de tip 1 – G^1 dependente de context: 

αAβ → αγβ, A transcris cu γ ≠ λ în contextul α .. β (monotone |A| ≤ |γ|) sau S → λ dacă S ⊄ αγβ. 

• Gramatici de tip 2 – G^2 independente de context (necontextuale): A → γ, A transcris cu γ 

• Gramatici de tip 3 – G3 regulate/liniare: 

A → a sau A → aB (dreapta) / A → Ba (stânga)

În cadrul acestei clasificări, între gramatici există relaţia G3 ⊂ G2 ⊂ G1 ⊂ G0. 

Fiecare tip de gramatică generează corespunzător un tip de limbaj

#### Limbaje

• Limbaje de tip 1 – L1=L(G1) dependente de context descrise de maşini Turing nedeterministe

• Limbaje de tip 2 – L2=L(G2)

independente de context nedeterministe (limbaje de programare) descrise de automate cu stivă (pushdown) 

• Limbaje de tip 3 – L3=L(G3) 
regulate/liniare descrise de automate finite


Între aceste categorii de limbaje existând evident relaţia
L3 ⊂ L2 ⊂ L1 ⊂ L0

### Exemple

#### Exemplu de gramatică de tip 0:

G = 〈{A, B}, {a, b, c}, {A→aAB|abc, bB→bbc, cB→Bc}, A〉 

în care 
	N = {A, B}
	Σ = {a, b, c}
	P = {A→aAB|abc, bB→bbc, cB→Bc}
	S=A,

şi care generează limbajul L = {anbncn| n≥1} = {abc, aabbcc, ..}

![[Pasted image 20260609203357.png]]

#### Exemplu de gramatică de tip 1: 

G = 〈{A, B, C, D, E}, {a, b, c}, 
{A→aAB|abC, CB→DB, DB→DE, DE→BE, BE→BC, bB→bbc, C→c}, A〉 

care generează de asemenea limbajul 
L = {anbncn| n≥1} = {abc, aabbcc, ..} 

#### Exemplu de gramatică de tip 2: 

G = 〈{S}, {a, b}, {S→aSb|ab}, S〉 

care generează limbajul L = {anbn| n≥1} = {ab, aabb, aaabbb, ..} 

#### Exemplu de gramatică de tip 3:

G = 〈{S, A, B}, {a, b}, {S→aA, A→aA| bB, B→bB|λ}, S〉 

care generează limbajul 
L = {ambn| m, n ≥1} = {ab, aab, abb, aabb, ..}

## Forma normala (pt tip2)

 gramatică de tip 2 (independentă de context) este în forma normală de tip:
#### Chomsky

Dacă regulile sale sunt de tipul: A→BC, A→a, S→λ. Caracteristica este utilă în etapa de preprocesare (procesare textuală) de tip bottom-up (bottom-up/LR parsing)

deoarece permite coborârea mai întâi până la nivelul fundamental/bottom (caracter / terminal) după care se procesează extinderi (up) ale structurilor mai complexe (incluzive).

#### Greibach

Dacă regulile sale încep cu un simbol terminal, adică sunt de tipul A→aα, S→λ.

Regulile gramaticii de tip Greibach nu sunt stâng recursive.

Caracteristica este utilă în etapa de procesare de tip top-down (top-down/LL parsing) asigurând oprirea după maxim n (G-stringuri) paşi.

#### Operator

Dacă regulile sale nu conţin consecutiv două simboluri neterminale, 

adică sunt de tipul A→..BaC...

Regulile gramaticii în forma normală Operator permit ancorarea expresiilor (prin intermediul simbolurilor terminale separatoare de tip a) corespunzând gramaticilor expresiilor (aritmetice) sau de precedenţă

#### Kuroda

dacă regulile sale sunt de tipul: AB→CD, A→BC|B |a/λ. 

Regulile gramaticii în forma normală Kuroda corespund gramaticilor monotone (monotonic/ linear bounded/noncontracting grammar). 

Dacă C≡A gramatica devine de tip 1 (dependentă de context) numită şi formă normală Penttonen

