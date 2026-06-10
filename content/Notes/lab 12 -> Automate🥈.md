[[LFT_Lab_all_in_one.pdf#page=23&selection=9,0,9,5|LFT_Lab_all_in_one, page 23]]
# Automate

Automatele sunt sisteme care analizeaza/valideaza cereri/inputuri/informatii/limbaje. 

Un automat A se descrie prin intermediul unei colectii formata din multimi si reguli de transformari succesive ale unor stari, 

de la o stare initiala spre stari finale, in corelatie cu un input, adica este definit de 5-uplul:

A = <Q, F q0, Σ, δ,>

cu:

- Q: multimea starilor
- Σ: alfabetul de intrare
- δ: Q × Σ -> Q: functia de tranzitie a starilor δ(q, a) = δ_a(q)
- q0 ∈ Q: starea initiala
- F ⊆ Q: multimea starilor finale


## Tipuri de automate după funcția de tranziție

- Automat determinist: daca δ este functie univoca (realizeaza tranzitii unice, adica δ(q, a) = x ∈ Q)

- Automat nedeterminist: daca δ este functie multivoca/multifunctie (exista tranzitii multiple/alternative, adica ∃δ(q, a) = {x, y, ...} ⊆ Q)

- Automat finit: daca poate avea un numar finit de stari (Q este finita)


## Proprietăți și reprezentare

- Limbajul acceptat de automatul A se defineste prin L(A) = {w | w ∈ Σ*, δ(q0, w) ∈ F}.

- Doua automate sunt echivalente (A1 <=> A2) daca accepta acelasi limbaj (L(A1) = L(A2)).

- Tabelele de stare/tranzitie: reprezinta modalitatea tabelara de a descrie tranzitiile si caracteristicile starilor. Pe linii se afla informatii ale cate unei stari, iar pe coloane se afla tranzitiile sau proprietatile starii in raport cu elementele alfabetului de intrare, tipul starii (intrare, iesire) etc.

- Graful de stare/tranzitie: reprezinta un graf orientat cu noduri si arce etichetate cu stari respectiv tranzitii. Starea initiala este marcata cu o sageata, nodurile asociate starilor finale sunt incercuite cu linii duble sau cu sageti emergente.

### Exemplu de automat determinist

A = ({x, y, z}, {a, b}, {δ(x, a)=x, δ(x, b)=y, δ(y, a)=x, δ(y, b)=z, δ(z, a)=y, δ(z, b)=x}, x, {y, z})

L(A) = {b, ab, bb, aab, abb, bba, bab, aaab, aabb, abba, abab, baab, babb, bbab, bbbb, ..}

![[Pasted image 20260609210851.png]]

| **δ** | **a** | **b** | **In/Out** |
| ----- | ----- | ----- | ---------- |
| x     | x     | y     | In         |
| y     | x     | z     | Out        |
| z     | y     | x     | Out        |

### Exemplu de automat nedeterminist

A = ({x, y, z}, {a, b}, {δ(x, a)=x, δ(x, b)=y, δ(y, a)=x, δ(y, b)={x, z}, δ(z, a)=y, δ(z, b)=∅}, x, {y, z})

L(A) = {b, ab, bb, aab, abb, bba, bab, bbb, aaab, aabb, abba, abab, abbb, baab, babb, bbab, bbbb, ..}

![[Pasted image 20260609210930.png]]

|**δ**|**a**|**b**|**In/Out**|
|---|---|---|---|
|x|x|y|In|
|y|x|x, z|Out|
|z|y|∅|Out|

# Automat Pushdown

Un automat pushdown A reprezinta un automat cu memorie gestionata ca o stiva (LIFO Last In-First Out), adica este definit prin 7-uplul:

A = <Q, F q0, z0, Γ, Σ, δ,>

cu:

- Q: multimea starilor

- Σ: alfabetul de intrare

- Γ: alfabetul memoriei/stivei

- δ: Q × (Σ ∪ {λ}) × Γ -> Q × Γ*: functia de tranzitie a starilor

- q0 ∈ Q: starea initiala

- z0 ∈ Γ: simbolul de start din memorie/stiva

- F ⊆ Q: multimea starilor finale


Orice 3-uplu (q, a, z) ∈ Q × Σ* × Γ se numeste stare instantanee sau configuratie a automatului.

## Exemplu de automat pushdown

- G = ({S}, {a, b}, {S -> aSb|λ}, S) este gramatica care genereaza limbajul
    
- L = {a^n b^n | n >= 0} = {λ, ab, aabb, ..} recunoscut/acceptat de automatul
    
- A = <Q, F q0, z0, Γ, Σ, δ,> ce este format din componentele:
    
    Q = {p, q, r}, Σ = {a, b}, Γ = {X, Y}, q0 = p, z0 = Y, F = {r}
    
    δ = {δ(p, a, Y) = (p, XY), δ(p, a, X) = (p, XX), δ(p, λ, Y) = (q, Y), δ(p, λ, X) = (q, X), δ(q, b, X) = (q, λ), δ(q, λ, Y) = (r, Y)}
    


![[Pasted image 20260609210946.png]]

|**δ**|**λ**|**a**|**b**|
|---|---|---|---|
|(p, Y)|(q, Y)|(p, XY)|∅|
|(p, X)|(q, X)|(p, XX)|∅|
|(q, X)|∅|∅|(q, λ)|
|(q, Y)|(r, Y)|∅|∅|