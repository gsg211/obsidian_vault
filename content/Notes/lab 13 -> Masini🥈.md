[[LFT_Lab_all_in_one.pdf#page=25&selection=11,1,11,4|LFT_Lab_all_in_one, page 25]]
# Mașini

O mașină este un sistem compus din entități interdependente care transformă, în cadrul unui proces (tehnologic/informațional/lingvistic) reversibil sau ireversibil, un input (materie primă, mișcare, forță, energie, informație) într-un output. Mașinile distribuite (rețelele) conțin și entități de tip mașină.

INPUT -> OUTPUT

 Maşină Abstractă (AM - Abstract Machine) de calcul sau de procesare a informaţiei reprezintă un set de operaţii prin intermediul cărora un set de date abstracte/simboluri de intrare este transformat în un set de date abstracte/simboluri de ieşire.
 
 Maşina trebuie să aibă o structurare logică şi matematică self consistentă, nefiind numaidecât necesar să existe şi o structurare fizică (reală) a maşinii.

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

> [!quote]+ Explicatie
> O mașină Turing e ca un automat finit dar cu o bandă infinită în loc de simplu input citit o dată. Diferența esențială față de automatele pe care le-ai văzut e că TM poate și citi și scrie pe bandă, și se poate mișca în ambele direcții. Asta îi dă putere de calcul incomparabil mai mare.
> 
> ---
> 
> Banda e infinită în ambele direcții, împărțită în celule, fiecare celulă conține exact un simbol din Γ. Capul de citire/scriere stă pe o celulă la un moment dat și la fiecare pas face exact trei lucruri — citește ce e sub el, scrie ceva în loc, și se mișcă stânga, dreapta, sau stă pe loc.
> 
> ---
> 
> Componentele una câte una:
> 
> - Q e mulțimea stărilor, exact ca la automate. Mașina se află mereu într-o singură stare.
>     
> - Γ e alfabetul de lucru, tot ce poate apărea pe bandă. E mai mare decât Σ pentru că în timpul calculului poți scrie simboluri intermediare care nu existau în input.
>     
> - B e simbolul de blanc, celula goală. Toate celulele nevizitate conțin B. Fără el nu știi unde se termină datele și unde începe golul benzii.
>     
> - Σ e alfabetul de intrare, ce primești inițial pe bandă. E submulțime din Γ fără B, pentru că inputul nu conține goluri.
>     
> - δ e inima mașinii. δ(q, a) = (p, b, D) se citește așa — ești în starea q și citești simbolul a, atunci treci în starea p, scrii b în loc de a, și te miști în direcția D. D poate fi L stânga, R dreapta, sau N stai pe loc.
>     
> - q0 e starea inițială, de unde pornești.
>     
> - F e mulțimea stărilor finale. Ajungi acolo și calculul se oprește cu succes.
>     
> 
> ---
> 
> Față de automatul pushdown unde memoria era o stivă LIFO rigidă, aici banda e memorie liberă. Poți citi și scrie oriunde, te poți întoarce oricând. De aia TM e mai puternică decât orice automat din ierarhia Chomsky și poate procesa orice tip de limbaj.
> 
> ---
> 
> Conjectura Church-Turing spune că orice calcul care poate fi descris algoritmic poate fi simulat de o mașină Turing. Nu e demonstrată formal, dar niciun contraexemplu nu a fost găsit vreodată, și orice model de calcul inventat după a fost dovedit echivalent cu TM.
> 

## Proprietăți și terminologie

- Configurație / descriere instantanee: cuvântul format din αqβ, cu α, β ∈ Γ* și q ∈ Q.

- Tipuri de mașini după funcția de tranziție: Mașina poate fi deterministă (δ univocă) sau nedeterministă (δ multivocă/multifuncție).

- Echipotență: Fie că este deterministă sau nedeterministă sau dacă folosește un singur sens de deplasare sau două sau dacă folosește mai multe benzi (k) de citire/scriere simultană (δ: Q × Γ^k -> Q × (Γ × {L, R, N})^k), mașinile pot procesa aceleași limbaje, adică sunt echipotente.


> [!quote]+ Explicatie
> Configurația  e o fotografie instantanee a mașinii. Conține exact trei lucruri:
> 
> - α — ce e pe bandă în stânga capului
> - q — starea curentă
> - β — ce e pe bandă în dreapta capului, inclusiv celula pe care stă capul acum
> 
> Dacă știi configuratia  știi absolut tot despre mașină în acel moment. E echivalentul configurației (q, input_rămas, stivă) de la automatul pushdown.
> 
> ---
> 
> Determinist vs nedeterminist e aceeași logică ca la automate:
> 
> - determinist — δ(q, a) = exact o singură instrucțiune. Un singur drum posibil.
> - nedeterminist — δ(q, a) = mai multe instrucțiuni posibile simultan. Mai multe drumuri explorate în paralel.
> 
> ---
> 
> Echipotența înseamnă că toate variantele de TM pot procesa exact aceleași limbaje. Nu contează dacă e:
> 
> - deterministă sau nedeterministă
> - cu o bandă sau cu k benzi simultane
> - cu deplasare într-un sens sau în ambele
> 
> Toate sunt echivalente ca putere de calcul. O variantă mai complexă poate fi mai rapidă, dar nu poate recunoaște limbaje pe care varianta simplă nu le poate recunoaște. Puterea e aceeași, doar eficiența diferă.
> 


## Exemple de Mașini Turing

### Exemplu de mașină Turing de substituire/rescriere/ștergere simboluri a repetitive

- Limbaj: L = {a^n | n >= 0} = {λ, a, aa, ..}

- Translare: T = M(L) = {x^n | n >= 0} = {λ, x, xx, ..}

- Mașină: M = ({A, B}, {a, x, s}, s, {A}, {δ(A, a) = (A, x, R), δ(A, s) = (B, s, N)}, A, {B})

![[Pasted image 20260609213020.png]]


| **δ** | **a**     | **x** | **s**     |
| ----- | --------- | ----- | --------- |
| A     | (A, x, R) | ∅     | (B, s, N) |
| B     | ∅         | ∅     | ∅         |

> [!example]+ Exp
> Tabela se citește exact ca la automate — linia e starea curentă, coloana e simbolul citit, celula e ce faci.
> 
> Diferența față de automate e că celula nu mai conține doar starea următoare, ci trei lucruri deodată.
> 
> ---
> 
> (A, x, R) înseamnă:
> 
> - A — starea în care treci
> - x — simbolul pe care îl scrii pe bandă în celula curentă
> - R — direcția în care se mișcă capul după ce a scris
> 
> ---
> 
> (B, s, N) înseamnă:
> 
> - B — starea în care treci
> - s — scrii s pe bandă, adică nu modifici nimic, blanul rămâne blanc
> - N — nu te miști nicăieri, capul stă pe loc
> 
> ---
> 
> ∅ înseamnă că tranziția nu e definită. Dacă ajungi acolo mașina se blochează.
> 
> ---
> 
> Deci prima linie a tabelei se citește așa:
> 
> - ești în A și citești `a` — fă (A, x, R) — rămâi în A, scrie x, du-te dreapta
> - ești în A și citești `x` — ∅ — blocat
> - ești în A și citești `s` — fă (B, s, N) — treci în B, nu schimba nimic, stai pe loc
> 
> 
> Mașina asta face ceva simplu — înlocuiește fiecare `a` cu `x` până dă de simbolul de blanc `s`, apoi se oprește.
> 
> ---
> 
> Componentele:
> 
> - Q = {A, B} — două stări. A e starea de lucru, B e starea finală.
> - Γ = {a, x, s} — ce poate apărea pe bandă. `a` e inputul, `x` e ce scriem în loc, `s` e blanul.
> - B = s — simbolul de blanc e `s` aici.
> - Σ = {a} — inputul conține doar `a`-uri.
> - q0 = A — pornim în starea A.
> - F = {B} — ne oprim când ajungem în B.
> 
> ---
> 
> Tranzițiile:
> 
> - δ(A, a) = (A, x, R) — ești în A, citești `a`, scrii `x` în loc, mergi dreapta, rămâi în A. Adică înlocuiești `a`-ul curent cu `x` și avansezi.
> - δ(A, s) = (B, s, N) — ești în A, citești blanul `s`, adică ai terminat inputul. Scrii tot `s`, nu te miști, treci în B. Acceptat.
> 
> Orice altă combinație e ∅, adică tranziție nedefinită, mașina se blochează.
> 
> ---
> 
> Exemplu pas cu pas pentru inputul `aaa`:
> 
> ```
> start:        A | a a a s
> citești a:    A | x a a s     scrii x, mergi R
> citești a:    A | x x a s     scrii x, mergi R
> citești a:    A | x x x s     scrii x, mergi R
> citești s:    B | x x x s     scrii s, stai, treci în B
> ```
> 
> Rezultat pe bandă: `x x x` — fiecare `a` a fost înlocuit cu `x`. Acceptat.
> 

### Exemplu de mașină Turing pentru procesarea limbajului {λ, ab, aabb, ..}

- Limbaj: L = {a^n b^n | n >= 0} = {λ, ab, aabb, ..}
    
- Translare: T = {x^n y^n | n >= 0} = {λ, xy, xxyy, ..}
    
- Mașină: M = ({A, B, C, D}, {a, b, x, y, s}, s, {a, b}, {δ(A, s) = (D, s, N), δ(A, y) = (A, y, R), δ(A, a) = (B, x, R), δ(B, y) = (B, y, R), δ(B, a) = (B, a, R), δ(B, b) = (C, y, L), δ(C, y) = (C, y, L), δ(C, a) = (C, a, L), δ(C, x) = (A, x, R)}, A, {D})
    

| **δ** | **a**     | **b**     | **x**     | **y**     | **s**     |
| ----- | --------- | --------- | --------- | --------- | --------- |
| A     | (B, x, R) | ∅         | ∅         | (A, y, R) | (D, s, N) |
| B     | (B, a, R) | (C, y, L) | ∅         | (B, y, R) | ∅         |
| C     | (C, a, L) | ∅         | (A, x, R) | (C, y, L) | ∅         |

![[Pasted image 20260609213036.png]]



> [!example]+ Exp
> Mașina asta e mai complexă — trebuie să verifice că numărul de a-uri e egal cu numărul de b-uri. Strategia e să marcheze câte un a și câte un b pe rând, până nu mai rămâne nimic nemarcат.
> 
> Componentele rapid:
> 
> Q = {A, B, C, D} — A e starea de start și reset, B caută un b după ce a marcat un a, C se întoarce înapoi, D e starea finală.
> Σ = {a, b} — inputul are doar a-uri și b-uri.
> Γ = {a, b, x, y, s} — pe bandă mai apar și x (a marcat), y (b marcat), s (blanc).
> q0 = A, F = {D}.
> 
> 
> Logica generală e un ciclu repetat:
> 
> marchează primul a nevăzut cu x, mergi dreapta
> caută primul b nevăzut, marchează-l cu y, întoarce-te stânga
> repetă până nu mai sunt a-uri nemarcate
> dacă dai de blanc direct din A, totul e marcat egal, acceptă
> 
> 
> Tranzițiile stare cu stare:
> starea A — caută următorul a de marcat:
> 
> δ(A, a) = (B, x, R) — găsești un a, îl marchezi cu x, treci în B și mergi dreapta să cauți un b
> δ(A, y) = (A, y, R) — dai de un y deja marcat, treci peste el, rămâi în A
> δ(A, s) = (D, s, N) — dai de blanc, nu mai sunt a-uri nemarcate, acceptă
> 
> 
> starea B — mergi dreapta să găsești un b de marcat:
> 
> δ(B, a) = (B, a, R) — dai de un a nemарcat, treci peste el, continuă dreapta
> δ(B, y) = (B, y, R) — dai de un y deja marcat, treci peste el, continuă dreapta
> δ(B, b) = (C, y, L) — găsești primul b nemарcat, îl marchezi cu y, treci în C și întoarce-te stânga
> 
> 
> starea C — întoarce-te stânga până dai de un x:
> 
> δ(C, a) = (C, a, L) — dai de un a, treci peste el, continuă stânga
> δ(C, y) = (C, y, L) — dai de un y, treci peste el, continuă stânga
> δ(C, x) = (A, x, R) — dai de un x, adică ai ajuns la începutul zonei nemarcate, treci înapoi în A și reia ciclul
> 
> 
> Exemplu pas cu pas pentru aabb:
> ```
> 
> start:           A | a a b b s
> A citește a:     B | x a b b s     marchează a cu x, mergi R
> B citește a:     B | x a b b s     trece peste a, mergi R
> B citește b:     C | x a y b s     marchează b cu y, mergi L
> C citește a:     C | x a y b s     trece peste a, mergi L
> C citește x:     A | x a y b s     ai găsit x, treci în A, mergi R
> 
> A citește a:     B | x x y b s     marchează a cu x, mergi R
> B citește y:     B | x x y b s     trece peste y, mergi R
> B citește b:     C | x x y y s     marchează b cu y, mergi L
> C citește y:     C | x x y y s     trece peste y, mergi L
> C citește x:     A | x x y y s     ai găsit x, treci în A, mergi R
> 
> A citește y:     A | x x y y s     trece peste y, mergi R
> A citește y:     A | x x y y s     trece peste y, mergi R
> A citește s:     D | x x y y s     blanc, totul marcat, acceptă
> Rezultat — acceptat, pentru că erau exact 2 a-uri și 2 b-uri.
> ```
> 


# Automat Liniar Mărginit (LBA)

Un automat liniar mărginit LBA (Linear Bounded Automaton) este o mașină Turing nedeterministă pentru care alfabetul de intrare conține și două simboluri speciale {L0, R0} ⊆ Σ ce indică limitele de deplasare la stânga respectiv dreapta a mașinii.

LBA = M = <Q, Γ, {L0, R0}, Σ, δ, q0, F>

Automatele LBA procesează limbaje generate de gramatici monotone: α -> β, |α| <= |β|.


> [!quote]+ Exp
> Un LBA e practic o mașină Turing cu banda limitată. În loc să aibă bandă infinită în ambele direcții, capul nu poate ieși din zona inputului. Asta e singura diferență față de TM.
> 
> ---
> 
> Limitele sunt marcate de două simboluri speciale din alfabetul de intrare:
> 
> - L0 — marginea stângă, capul nu poate trece mai la stânga de aici
> - R0 — marginea dreaptă, capul nu poate trece mai la dreapta de aici
> 
> Deci banda pe care lucrează LBA e fix lungimea inputului, nimic mai mult.
> 
> ---
> 
> De ce e asta important? Pentru că puterea de calcul e direct legată de câtă memorie ai. TM are memorie infinită și poate procesa orice. LBA are memorie limitată la lungimea inputului și poate procesa mai puțin — exact limbajele de tip 1 din ierarhia Chomsky, adică cele generate de gramatici monotone.
> 
> ---
> 
> O gramatică monotonă înseamnă că în orice regulă α -> β, partea dreaptă e cel puțin la fel de lungă ca partea stângă, adică |α| <= |β|. Nu poți șterge simboluri, doar le poți înlocui sau extinde. De aia memoria limitată la lungimea inputului e suficientă — outputul nu e niciodată mai scurt decât inputul.
> 
> ---
> 
> Față de ierarhia completă:
> 
> - automat finit — memorie zero, tip 3
> - automat pushdown — memorie stivă, tip 2
> - LBA — memorie = lungimea inputului, tip 1
> - TM — memorie infinită, tip 0
> 

## Exemplu de automat liniar mărginit

- Limbaj: L = {a^n b^n | n >= 1} = {ab, aabb, ..}

- Translare: T = {x^n y^n | n >= 1} = {xy, xxyy, ..}

- Mașină:

- LBA = ({A, B, C, D, E, F}, {a, b, x, y, \[, ]}, {\[, ]}, {a, b}, {δ(A, \[) = (B, \[, R), δ(B, a) = (C, x, R), δ(B, y) = (E, y, R), δ(C, a) = (C, a, R), δ(C, b) = (D, y, L), δ(C, y) = (C, y, R), δ(D, a) = (D, a, L), δ(D, x) = (B, x, R), δ(D, y) = (D, y, L), δ(E, y) = (E, y, R), δ(E, ]) = (F, ], R)}, A, {F})


| **δ** | **[**     | **a**     | **b**     | **x**     | **y**     | **]**     |
| ----- | --------- | --------- | --------- | --------- | --------- | --------- |
| A     | (B, [, R) | ∅         | ∅         | ∅         | ∅         | ∅         |
| B     | ∅         | (C, x, R) | ∅         | ∅         | (E, y, R) | ∅         |
| C     | ∅         | (C, a, R) | (D, y, L) | ∅         | (C, y, R) | ∅         |
| D     | ∅         | (D, a, L) | ∅         | (B, x, R) | (D, y, L) | ∅         |
| E     | ∅         | ∅         | ∅         | ∅         | (E, y, R) | (F, ], R) |


> [!example]+ exp
> LBA-ul ăsta face același lucru ca mașina Turing pentru aⁿbⁿ de mai devreme, dar banda e limitată între `[` și `]`. Capul nu poate ieși din aceste margini.
> 
> ---
> 
> Componentele rapid:
> 
> - Q = {A, B, C, D, E, F} — A e starea de start, F e starea finală.
> - Σ = {a, b} — inputul are doar `a`-uri și `b`-uri.
> - Γ = {a, b, x, y, \[, ]} — pe bandă mai apar `x` (a marcat), `y` (b marcat), și marginile `[` și `]`.
> - {L0, R0} = {\[, \]} — marginile benzii.
> - q0 = A, F = {F}.
> 
> ---
> 
> Logica e același ciclu ca la TM — marchează câte un `a` cu `x`, găsește un `b` și marchează-l cu `y`, întoarce-te, repetă. Diferența e că nu poți ieși din `[` și `]`.
> 
> ---
> 
> Stările una câte una:
> 
> - A — starea de start. Singura tranziție e δ(A, \[) = (B, \[, R), adică citești marginea stângă și intri în B. Nimic altceva nu e definit din A.
>     
> - B — începutul fiecărui ciclu. Cauți următorul `a` de marcat.
>     
>     - δ(B, a) = (C, x, R) — găsești `a`, îl marchezi cu `x`, treci în C mergi dreapta
>     - δ(B, y) = (E, y, R) — nu mai sunt `a`-uri, dai de `y`, înseamnă că toate `a`-urile sunt marcate, treci în E să verifici că și `b`-urile sunt toate marcate
> - C — mergi dreapta să găsești primul `b` nemарcat.
>     
>     - δ(C, a) = (C, a, R) — treci peste `a`-uri nemarcate, continuă dreapta
>     - δ(C, y) = (C, y, R) — treci peste `y`-uri deja marcate, continuă dreapta
>     - δ(C, b) = (D, y, L) — găsești `b`, îl marchezi cu `y`, treci în D și întoarce-te stânga
> - D — întoarce-te stânga până dai de un `x`.
>     
>     - δ(D, a) = (D, a, L) — treci peste `a`-uri, continuă stânga
>     - δ(D, y) = (D, y, L) — treci peste `y`-uri, continuă stânga
>     - δ(D, x) = (B, x, R) — ai găsit `x`, adică ai ajuns la ultimul `a` marcat, treci în B și reia ciclul
> - E — verifică că nu mai sunt `b`-uri nemarcate, mergi dreapta spre `]`.
>     
>     - δ(E, y) = (E, y, R) — treci peste `y`-uri, continuă dreapta
>     - δ(E, ]) = (F, ], R) — ai ajuns la marginea dreaptă și totul era marcat, treci în F, acceptat
> 
> ---
> 
> Exemplu pas cu pas pentru `aabb`, banda inițială e `[aabb]`:
> 
> ```
> start:            A | [ a a b b ]
> A citește [:      B | [ a a b b ]     intri în B, mergi R
> 
> B citește a:      C | [ x a b b ]     marchează a cu x, mergi R
> C citește a:      C | [ x a b b ]     trece peste a, mergi R
> C citește b:      D | [ x a y b ]     marchează b cu y, mergi L
> D citește a:      D | [ x a y b ]     trece peste a, mergi L
> D citește x:      B | [ x a y b ]     ai găsit x, treci în B, mergi R
> 
> B citește a:      C | [ x x y b ]     marchează a cu x, mergi R
> C citește y:      C | [ x x y b ]     trece peste y, mergi R
> C citește b:      D | [ x x y y ]     marchează b cu y, mergi L
> D citește y:      D | [ x x y y ]     trece peste y, mergi L
> D citește x:      B | [ x x y y ]     ai găsit x, treci în B, mergi R
> 
> B citește y:      E | [ x x y y ]     nu mai sunt a-uri, treci în E, mergi R
> E citește y:      E | [ x x y y ]     trece peste y, mergi R
> E citește y:      E | [ x x y y ]     trece peste y, mergi R
> E citește ]:      F | [ x x y y ]     ai ajuns la margine, acceptă
> ```
> 
> Acceptat — erau exact 2 `a`-uri și 2 `b`-uri.
> 

# Mașini Moore și Mealy

- Mașinile de tip Moore stabilesc output-ul exclusiv prin starea în care se află.

- În cazul unor mașini de tip Mealy ieșirile depind atât de starea curentă cât și de input-ul curent.
