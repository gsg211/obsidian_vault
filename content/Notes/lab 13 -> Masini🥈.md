# Mașini

O mașină este un sistem compus din entități interdependente care transformă, în cadrul unui proces (tehnologic/informațional/lingvistic) reversibil sau ireversibil, un input (materie primă, mișcare, forță, energie, informație) într-un output. Mașinile distribuite (rețelele) conțin și entități de tip mașină.

O mașină abstractă (AM - Abstract Machine) de calcul sau de procesare a informației reprezintă un set de operații prin intermediul cărora un set de date abstracte/simboluri de intrare este transformat într-un set de date abstracte/simboluri de ieșire. Mașina trebuie să aibă o structurare logică și matematică self consistentă, nefiind numaidecât necesar să existe și o structurare fizică (reală) a mașinii.

# Mașina Turing

Mașina Turing (TM - Turing Machine) reprezintă un model de procesare a unui set de simboluri în acord cu un set de reguli, fiind modelul matematic de bază al sistemelor de calcul, permițând descrierea operațiilor fundamentale din orice sistem de calcul. 

O mașină Turing este așadar capabilă, în principiu, să descrie orice calcul algoritmic sau să realizeze orice tip de proces/transformare/translare (Conjectura Church-Turing).

O mașină Turing M reprezintă un model de procesare definit de 7-uplul:

M = <Q, Γ, B, Σ, δ, q0, F>

cu:

- Q: mulțimea stărilor
- Γ: alfabetul de lucru
- B ∈ Γ: simbolul vid (blanc)
- Σ ⊆ Γ - {B}: alfabetul de intrare
- δ: Q × Γ -> Q × Γ × {L, R, N}: funcția de tranziție/translare
- {L, R, N}: mulțimea deplasărilor posibile (Left, Right, None)
- q0 ∈ Q: starea inițială
- F ⊆ Q: mulțimea stărilor finale

## Proprietăți și terminologie

- Configurație / descriere instantanee: cuvântul format din αqβ, cu α, β ∈ Γ* și q ∈ Q.

- Tipuri de mașini după funcția de tranziție: Mașina poate fi deterministă (δ univocă) sau nedeterministă (δ multivocă/multifuncție).

- Echipotență: Fie că este deterministă sau nedeterministă sau dacă folosește un singur sens de deplasare sau două sau dacă folosește mai multe benzi (k) de citire/scriere simultană (δ: Q × Γ^k -> Q × (Γ × {L, R, N})^k), mașinile pot procesa aceleași limbaje, adică sunt echipotente.


## Exemple de Mașini Turing

### Exemplu de mașină Turing de substituire/rescriere/ștergere simboluri a repetitive

- Limbaj: L = {a^n | n >= 0} = {λ, a, aa, ..}

- Translare: T = M(L) = {x^n | n >= 0} = {λ, x, xx, ..}

- Mașină: M = ({A, B}, {a, x, s}, s, {A}, {δ(A, a) = (A, x, R), δ(A, s) = (B, s, N)}, A, {B})

![[Pasted image 20260609213020.png]]


|**δ**|**a**|**x**|**s**|
|---|---|---|---|
|A|(A, x, R)|∅|(B, s, N)|
|B|∅|∅|∅|


### Exemplu de mașină Turing pentru procesarea limbajului {λ, ab, aabb, ..}

- Limbaj: L = {a^n b^n | n >= 0} = {λ, ab, aabb, ..}
    
- Translare: T = {x^n y^n | n >= 0} = {λ, xy, xxyy, ..}
    
- Mașină: M = ({A, B, C, D}, {a, b, x, y, s}, s, {a, b}, {δ(A, s) = (D, s, N), δ(A, y) = (A, y, R), δ(A, a) = (B, x, R), δ(B, y) = (B, y, R), δ(B, a) = (B, a, R), δ(B, b) = (C, y, L), δ(C, y) = (C, y, L), δ(C, a) = (C, a, L), δ(C, x) = (A, x, R)}, A, {D})
    

|**δ**|**a**|**b**|**x**|**y**|**s**|
|---|---|---|---|---|---|
|A|(B, x, R)|∅|∅|(A, y, R)|(D, s, N)|
|B|(B, a, R)|(C, y, L)|∅|(B, y, R)|∅|
|C|(C, a, L)|∅|(A, x, R)|(C, y, L)|∅|

![[Pasted image 20260609213036.png]]

# Automat Liniar Mărginit (LBA)

Un automat liniar mărginit LBA (Linear Bounded Automaton) este o mașină Turing nedeterministă pentru care alfabetul de intrare conține și două simboluri speciale {L0, R0} ⊆ Σ ce indică limitele de deplasare la stânga respectiv dreapta a mașinii.

LBA = M = <Q, Γ, {L0, R0}, Σ, δ, q0, F>

Automatele LBA procesează limbaje generate de gramatici monotone: α -> β, |α| <= |β|.

## Exemplu de automat liniar mărginit

- Limbaj: L = {a^n b^n | n >= 1} = {ab, aabb, ..}

- Translare: T = {x^n y^n | n >= 1} = {xy, xxyy, ..}

- Mașină:

- LBA = ({A, B, C, D, E, F}, {a, b, x, y, \[, ]}, {\[, ]}, {a, b}, {δ(A, \[) = (B, \[, R), δ(B, a) = (C, x, R), δ(B, y) = (E, y, R), δ(C, a) = (C, a, R), δ(C, b) = (D, y, L), δ(C, y) = (C, y, R), δ(D, a) = (D, a, L), δ(D, x) = (B, x, R), δ(D, y) = (D, y, L), δ(E, y) = (E, y, R), δ(E, ]) = (F, ], R)}, A, {F})


|**δ**|**[**|**a**|**b**|**x**|**y**|**]**|
|---|---|---|---|---|---|---|
|A|(B, [, R)|∅|∅|∅|∅|∅|
|B|∅|(C, x, R)|∅|∅|(E, y, R)|∅|
|C|∅|(C, a, R)|(D, y, L)|∅|(C, y, R)|∅|
|D|∅|(D, a, L)|∅|(B, x, R)|(D, y, L)|∅|
|E|∅|∅|∅|∅|(E, y, R)|(F, ], R)|

# Mașini Moore și Mealy

- Mașinile de tip Moore stabilesc output-ul exclusiv prin starea în care se află.

- În cazul unor mașini de tip Mealy ieșirile depind atât de starea curentă cât și de input-ul curent.
