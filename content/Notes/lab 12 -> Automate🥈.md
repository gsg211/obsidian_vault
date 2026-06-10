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

> [!quote]+ explicatie
> Un automat e practic inversul unei gramatici. Gramatica generează cuvinte, automatele verifică dacă un cuvânt aparține unui limbaj.
> 
> Dai unui automat un șir de simboluri, el îl citește caracter cu caracter, și la final îți spune da sau nu — cuvântul ăsta e valid în limbajul meu?
> 
> ---
> 
> Cele 5 componente:
> 
> Q e mulțimea stărilor. Gândește-te la stări ca la "situații" în care se poate afla automatul la un moment dat. De exemplu "am citit doar a-uri până acum" e o stare, "am început să citesc b-uri" e altă stare.
> 
> Σ e alfabetul de intrare, exact ca la gramatici. Simbolurile pe care le poate citi automatul.
> 
> q0 e starea inițială, de unde pornește automatul înainte să citească orice simbol.
> 
> F e mulțimea stărilor finale, numite și stări de acceptare. Dacă după ce ai citit tot cuvântul automatul se află într-o stare din F, cuvântul e acceptat, adică aparține limbajului. Dacă nu, e respins.
> 
> δ e funcția de tranziție și e inima automatului. δ(q, a) = p înseamnă "dacă ești în starea q și citești simbolul a, treci în starea p". La fiecare caracter citit, automatul își schimbă starea conform acestei funcții.
> 
> ---
> 
> Exemplu concret. Să facem un automat pentru limbajul aⁿb, adică oricâte a-uri urmate de exact un b:
> 
> ```
> Q = {q0, q1, q2}
> Σ = {a, b}
> q0 = q0
> F = {q1}
> 
> δ(q0, a) = q0
> δ(q0, b) = q1
> δ(q1, a) = q2
> δ(q1, b) = q2
> δ(q2, a) = q2
> δ(q2, b) = q2
> ```
> 
> q0 e starea "citesc a-uri", q1 e starea "am citit un b și e valid", q2 e starea "am citit ceva greșit, mort".
> 
> Pentru cuvântul "aaab":
> 
> ```
> q0 -a-> q0 -a-> q0 -a-> q0 -b-> q1
> ```
> 
> Am terminat în q1 care e stare finală, deci "aaab" e acceptat.
> 
> Pentru cuvântul "abb":
> 
> ```
> q0 -a-> q0 -b-> q1 -b-> q2
> ```
> 
> Am terminat în q2 care nu e stare finală, deci "abb" e respins.
> 
> ---
> 
> Legătura cu gramaticile e directă — automatele finite acceptă exact limbajele de tip 3, adică cele regulate. Pentru fiecare gramatică de tip 3 există un automat echivalent și invers. Cu cât limbajul e mai complex, ai nevoie de automate mai puternice, de exemplu automate cu stivă pentru tip 2 sau mașini Turing pentru tip 0.
> 

## Tipuri de automate după funcția de tranziție

- Automat determinist: daca δ este functie univoca (realizeaza tranzitii unice, adica δ(q, a) = x ∈ Q)

- Automat nedeterminist: daca δ este functie multivoca/multifunctie (exista tranzitii multiple/alternative, adica ∃δ(q, a) = {x, y, ...} ⊆ Q)


> [!quote]+ explicatie
> Un automat determinist înseamnă că din orice stare, pentru orice simbol citit, există exact o singură stare în care poți merge. Nu ai nicio alegere, traseul e unic și predictibil.
> 
> δ(q, a) = x — dai o stare și un simbol, primești exact o stare. Punct.
> 
> ---
> 
> Un automat nedeterminist înseamnă că din aceeași stare, pentru același simbol, poți merge în mai multe stări simultan. Nu alegi una, ci explorezi toate variantele în paralel.
> 
> δ(q, a) = {x, y, z} — dai o stare și un simbol, primești o mulțime de stări posibile.
> 
> Cuvântul e acceptat dacă cel puțin una din variantele explorate ajunge într-o stare finală.
> 
> ---
> 

Automat finit: daca poate avea un numar finit de stari (Q este finita)



## Proprietăți și reprezentare

- Limbajul acceptat de automatul A se defineste prin L(A) = {w | w ∈ Σ*, δ(q0, w) ∈ F}.

- Doua automate sunt echivalente (A1 <=> A2) daca accepta acelasi limbaj (L(A1) = L(A2)).

- Tabelele de stare/tranzitie: reprezinta modalitatea tabelara de a descrie tranzitiile si caracteristicile starilor. Pe linii se afla informatii ale cate unei stari, iar pe coloane se afla tranzitiile sau proprietatile starii in raport cu elementele alfabetului de intrare, tipul starii (intrare, iesire) etc.

- Graful de stare/tranzitie: reprezinta un graf orientat cu noduri si arce etichetate cu stari respectiv tranzitii. Starea initiala este marcata cu o sageata, nodurile asociate starilor finale sunt incercuite cu linii duble sau cu sageti emergente.

> [!quote]+ Explicatie
> L(A) = {w | w ∈ Σ*, δ(q0, w) ∈ F} se citește așa: limbajul acceptat de automatul A e mulțimea tuturor cuvintelor w pentru care, pornind din starea inițială q0 și citind w caracter cu caracter, ajungi într-o stare finală. δ(q0, w) înseamnă că aplici funcția de tranziție repetat pentru fiecare simbol din w, nu doar pentru unul singur.
> 
> ---
> 
> Echivalența a două automate e simplă — nu contează cum arată pe dinăuntru, câte stări au, cum se numesc stările. Dacă acceptă exact aceleași cuvinte, sunt echivalente. La fel cum două gramatici diferite pot genera același limbaj.
> 
> ---
> 
> Tabela de tranziție e o reprezentare tabelară a funcției δ. Liniile sunt stările, coloanele sunt simbolurile din alfabet, și în fiecare celulă scrii în ce stare ajungi.
> 
> Exemplu pentru automatul cu limbajul aⁿb de mai devreme:
> 
> ```
>         a       b
> → q0    q0      q1
> * q1    q2      q2
>   q2    q2      q2
> ```
> 
> Săgeata → marchează starea inițială, asteriscul * marchează stările finale. Citești tabela așa: ești în q0 și citești a, rămâi în q0. Ești în q0 și citești b, mergi în q1. Și așa mai departe.
> 
> ---
> 
> Graful de tranziție e aceeași informație dar vizual. Fiecare stare e un nod, fiecare tranziție e o săgeată între noduri etichetată cu simbolul citit. Starea inițială are o săgeată care vine din nimic spre ea, stările finale au dublu cerc.
> 
> Același automat ca graf:
> 
> ```
> → (q0) --a--> (q0)
>    (q0) --b--> ((q1))
>   ((q1)) --a--> (q2)
>   ((q1)) --b--> (q2)
>    (q2) --a--> (q2)
>    (q2) --b--> (q2)
> ```
> 
> q1 e între paranteze duble pentru că e stare finală. q2 e starea "moartă", odată ajuns acolo nu mai poți ieși și nu e finală, deci orice cuvânt care trece prin ea e respins.
> 
> Graful e mai intuitiv pentru a vedea traseele posibile, tabela e mai compactă și mai ușor de procesat algoritmic.

### Exemplu de automat determinist

A = ({x, y, z}, {a, b}, {δ(x, a)=x, δ(x, b)=y, δ(y, a)=x, δ(y, b)=z, δ(z, a)=y, δ(z, b)=x}, x, {y, z})

L(A) = {b, ab, bb, aab, abb, bba, bab, aaab, aabb, abba, abab, baab, babb, bbab, bbbb, ..}

![[Pasted image 20260609210851.png]]

| **δ** | **a** | **b** | **In/Out** |
| ----- | ----- | ----- | ---------- |
| x     | x     | y     | In         |
| y     | x     | z     | Out        |
| z     | y     | x     | Out        |


> [!example]+ explicatie
> Componentele sunt:
> 
> Q = {x, y, z} — trei stări
> 
> Σ = {a, b} — două simboluri
> 
> q0 = x — starea inițială
> 
> F = {y, z} — stările finale VALIDE sunt y și z, adică Out în tabelă
> 
> ---
> 
> Funcția de tranziție din tabelă se citește simplu: ești pe linia stării curente, te uiți la coloana simbolului citit, și acolo e starea următoare.
> 
> ```
> δ(x, a) = x    din x citind a, rămâi în x
> δ(x, b) = y    din x citind b, mergi în y
> δ(y, a) = x    din y citind a, mergi în x
> δ(y, b) = z    din y citind b, mergi în z
> δ(z, a) = y    din z citind a, mergi în y
> δ(z, b) = x    din z citind b, mergi în x
> ```
> 
> ---
> 
> De ce e determinist? Pentru că în fiecare celulă din tabelă e exact o singură stare. Nicio ambiguitate, nicio alegere.
> 
> ---
> 
> Să verificăm câteva cuvinte din L(A):
> 
> "b" — q0=x, citești b, ajungi în y. y e finală. Acceptat.
> 
> "ab" — x citește a rămâne x, x citește b merge în y. y e finală. Acceptat.
> 
> "bb" — x citește b merge în y, y citește b merge în z. z e finală. Acceptat.
> 
> "aa" — x citește a rămâne x, x citește a rămâne x. x nu e finală. Respins.
> 
> "a" — x citește a rămâne x. x nu e finală. Respins.
> 
> ---
> 
> Deci intuitiv automatul acceptă orice cuvânt care nu se termină în x, adică nu se termină cu un număr par de a-uri consecutivi de la start sau mai general, orice cuvânt al cărui ultim simbol te lasă în y sau z și nu în x. Starea x e "moartă" în sensul că nu e finală, y și z sunt stări bune.
> 

### Exemplu de automat nedeterminist

A = ({x, y, z}, {a, b}, {δ(x, a)=x, δ(x, b)=y, δ(y, a)=x, δ(y, b)={x, z}, δ(z, a)=y, δ(z, b)=∅}, x, {y, z})

L(A) = {b, ab, bb, aab, abb, bba, bab, bbb, aaab, aabb, abba, abab, abbb, baab, babb, bbab, bbbb, ..}

![[Pasted image 20260609210930.png]]

|**δ**|**a**|**b**|**In/Out**|
|---|---|---|---|
|x|x|y|In|
|y|x|x, z|Out|
|z|y|∅|Out|

> [!example]+ explicatie 
> Aici e automatul nedeterminist despre care vorbeam mai devreme.
> 
> Uită-te la δ(y, b) = {x, z} — din starea y citind b poți merge în două stări simultan, x și z. Asta e exact definiția nedeterminismului, funcția returnează o mulțime de stări nu una singură.
> 
> Și δ(z, b) = ∅ — din z citind b nu mergi nicăieri, tranziția nu există. Acel traseu moare.
> 
> ---
> 
> Cum funcționează acceptarea la nedeterminist? Explorezi toate variantele în paralel, și dacă cel puțin una din ele termină într-o stare finală, cuvântul e acceptat.
> 
> Să verificăm "bb":
> 
> ```
> x citește b => y
> y citește b => {x, z}    (două variante simultan)
> 
> varianta 1: termini în x, nu e finală, respinsă
> varianta 2: termini în z, e finală, acceptată
> ```
> 
> Pentru că varianta 2 a acceptat, cuvântul "bb" e acceptat.
> 
> ---
> 
> Față de automatul determinist de mai devreme, singura diferență e regula δ(y, b) care acolo era doar z, aici e {x, z}. De aia limbajul e puțin diferit — apar cuvinte noi acceptate pe care determinismul le rata.
> 
> 
> Teoretic e explorare în lățime — toate variantele avansează simultan pas cu pas. Nu alegi una și mergi până la capăt, ci la fiecare simbol citit calculezi toate stările posibile în care te poți afla.
> 
> Practic gândește-te că în loc să urmărești o singură stare curentă, urmărești o mulțime de stări curente. La fiecare simbol citit, transformi toată mulțimea.
> 
> Pentru "bb" de exemplu:
> 
> ```
> start:        {x}
> după b:       {y}
> după bb:      {x, z}    (din y cu b mergi în x și z simultan)
> ```
> 
> La final te uiți dacă mulțimea {x, z} conține cel puțin o stare finală. Conține z, deci acceptat.
> 
> ---
> 
> Pentru ∅, dacă ești în z și citești b, acel fir moare pur și simplu. Dispare din mulțimea stărilor curente. Nu e eroare, nu oprește tot automatul, doar acel traseu specific nu mai există.
> 
> Deci dacă mulțimea stărilor curente era {y, z} și citești b:
> 
> ```
> din y cu b => {x, z}
> din z cu b => ∅ (dispare)
> rezultat:  {x, z}
> ```
> 
> Firele moarte se ignoră. Contează doar dacă mai rămâne cel puțin un fir viu la final care să fie în stare finală.

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

> [!quote]+ explicatie
> Un automat pushdown e practic un automat finit obișnuit dar cu o stivă atașată. Stiva îi dă memorie, ceea ce îi permite să accepte limbaje de tip 2, pe care automatele finite simple nu le pot accepta.
> 
> ---
> 
> De ce e nevoie de stivă?
> 
> Ține minte că automatele finite nu pot recunoaște aⁿbⁿ pentru că nu pot număra. Nu au unde să țină minte câte a-uri au citit. Stiva rezolvă exact asta — când citești un a, pui ceva pe stivă, când citești un b, scoți ceva din stivă, și la final dacă stiva e goală numerele erau egale.
> 
> ---
> 
> Componentele noi față de automatul finit:
> 
> Γ e alfabetul stivei, simbolurile pe care le poți pune în stivă. Pot fi diferite de simbolurile din Σ.
> 
> z0 e simbolul inițial din stivă, un marcaj de fund care îți spune că ai ajuns la baza stivei. E important ca să știi când stiva e goală.
> 
> ---
> 
> Funcția de tranziție e mai complexă acum:
> 
> δ(q, a, z) = (p, γ) înseamnă "ești în starea q, citești simbolul a din input, și în vârful stivei ai z — atunci treci în starea p și înlocuiești z cu γ pe stivă".
> 
> γ poate fi mai multe simboluri, un singur simbol, sau λ. Dacă γ = λ înseamnă că ai scos z din stivă fără să pui nimic în loc, adică pop. Dacă γ = Az înseamnă că ai pus A peste z, adică push.
> 
> Și a poate fi λ, ceea ce înseamnă că faci o tranziție fără să citești nimic din input, doar manipulezi stiva.
> 
> ---
> 
> Configurația (q, a, z) e o fotografie instantanee a automatului la un moment dat — în ce stare ești, ce mai ai de citit din input, și ce e în stivă. Din configurație poți deduce exact tot ce s-a întâmplat și ce urmează.
> 
> ---
> 
> Exemplu simplu pentru aⁿbⁿ:
> 
> ```
> citești a => push A pe stivă
> citești b => pop A din stivă
> la final stiva goală și stare finală => acceptat
> ```
> 
> Pentru "aabb":
> 
> ```
> citești a => stivă: A
> citești a => stivă: AA
> citești b => stivă: A
> citești b => stivă: goală
> stivă goală => acceptat
> ```
> 
> Pentru "aab":
> 
> ```
> citești a => stivă: A
> citești a => stivă: AA
> citești b => stivă: A
> input terminat, stivă nu e goală => respins
> ```
> 


## Exemplu de automat pushdown

- G = ({S}, {a, b}, {S -> aSb|λ}, S) este gramatica care genereaza limbajul

- L = {a^n b^n | n >= 0} = {λ, ab, aabb, ..} recunoscut/acceptat de automatul

- A = <Q, F q0, z0, Γ, Σ, δ,> ce este format din componentele:
    Q = {p, q, r}
    Σ = {a, b}
    Γ = {X, Y}
    q0 = p
    z0 = Y
    F = {r}

δ = {δ(p, a, Y) = (p, XY), δ(p, a, X) = (p, XX), δ(p, λ, Y) = (q, Y), δ(p, λ, X) = (q, X), δ(q, b, X) = (q, λ), δ(q, λ, Y) = (r, Y)}



![[Pasted image 20260609210946.png]]

| **δ**  | **λ**  | **a**   | **b**  | efect                                             | effect 2                                         |
| ------ | ------ | ------- | ------ | ------------------------------------------------- | ------------------------------------------------ |
| (p, Y) | (q, Y) | (p, XY) | ∅      | push X cand e A                                   | daca e lambda te muti in q  si nu modifici stiva |
| (p, X) | (q, X) | (p, XX) | ∅      | push X cand e A                                   | daca e lambda te muti in q  si nu modifici stiva |
| (q, X) | ∅      | ∅       | (q, λ) | POP de pe stiva                                   | a si lambda nu au treaba                         |
| (q, Y) | (r, Y) | ∅       | ∅      | cand primesc un lambda => nu modif si ma duc in r | a si b nu au treaba                              |

> [!quote]+ explicatie
> Tabelul are linii de forma (stare, vârf_stivă) și coloane pentru fiecare simbol din input (inclusiv λ).
> Celula îți spune (stare nouă,       ce pui în stivă în loc de vârf).
> 
> Deci când citești tabelul, linia îți spune UNDE ești (stare + ce e pe vârful stivei), coloana îți spune CE citești, și celula îți spune UNDE mergi și CE faci cu stiva.
> 
> Hai pe exemple concrete:
> Linia (p, Y), coloana a, celula (p, XY) — ești în starea p, vârful stivei e Y, citești a. Rezultatul e că mergi în p și pui XY în loc de Y. Adică ai scos Y și ai pus XY, deci net ai adăugat un X deasupra lui Y. Push X.
> Linia (p, X), coloana a, celula (p, XX) — ești în p, vârful e X, citești a. Pui XX în loc de X. Adică ai scos X și ai pus XX, deci net ai adăugat un X. Push X.
> Linia (q, X), coloana b, celula (q, λ) — ești în q, vârful e X, citești b. Pui λ în loc de X. Adică ai scos X și n-ai pus nimic. Pop X.
> Linia (q, Y), coloana λ, celula (r, Y) — ești în q, vârful e Y, nu citești nimic. Pui Y în loc de Y, stiva neschimbată, dar treci în r. Acceptat.
> 
> Deci regula pentru push/pop e simplă: te uiți ce era pe vârf (din linie) și ce e în celulă, și compari. Dacă celula are mai multe simboluri decât vârful, e push. Dacă celula e λ, e pop. Dacă e același simbol, stiva e neschimbată.


> [!example]+ Exemplu
> Hai să înțelegem componentele mai întâi.
> 
> Q = {p, q, r} — trei stări. p e faza de citire a-uri, q e faza de citire b-uri, r e starea finală.
> 
> Γ = {X, Y} — alfabetul stivei. Y e simbolul de fund al stivei (z0 = Y), X e simbolul pe care îl punem pentru fiecare a citit.
> 
> F = {r} — singura stare finală e r.
> 
> ---
> 
> Logica generală e simplă:
> 
> În faza p citești a-uri și pentru fiecare a pui un X pe stivă. Când nu mai ai a-uri de citit, treci în faza q. În faza q citești b-uri și pentru fiecare b scoți un X din stivă. Când stiva a rămas doar cu Y (fundul), treci în r și accepți.
> 
> ---
> 
> Tranzițiile una câte una:
> 
> δ(p, a, Y) = (p, XY) — ești în p, citești a, fundul stivei e Y. Pui X peste Y. Stiva devine XY.
> 
> δ(p, a, X) = (p, XX) — ești în p, citești a, vârful stivei e X. Pui încă un X. Stiva crește.
> 
> δ(p, λ, Y) = (q, Y) — ești în p, nu citești nimic, vârful stivei e Y adică stiva e goală. Treci în q fără să modifici stiva. Asta e tranziția pentru cazul n=0, adică λ.
> 
> δ(p, λ, X) = (q, X) — ești în p, nu citești nimic, vârful e X. Treci în q, ai terminat de citit a-urile.
> 
> δ(q, b, X) = (q, λ) — ești în q, citești b, vârful e X. Scoți X din stivă. Pop.
> 
> δ(q, λ, Y) = (r, Y) — ești în q, nu citești nimic, vârful e Y adică stiva e goală. Treci în r, acceptat.
> 
> ---
> 
> Exemplu pentru "aabb":
> 
> ```
> start:         (p, aabb, Y)      stare, input rămas, stivă
> citești a:     (p, abb,  XY)     push X
> citești a:     (p, bb,   XXY)    push X
> λ tranziție:   (q, bb,   XXY)    treci în q fără să citești
> citești b:     (q, b,    XY)     pop X
> citești b:     (q, λ,    Y)      pop X
> λ tranziție:   (r, λ,    Y)      stivă are doar Y, treci în r
> ```
> 
> Ai terminat inputul și ești în r care e finală. Acceptat.
> 
> ---
> 
> Exemplu pentru "ab":
> 
> ```
> start:         (p, ab, Y)
> citești a:     (p, b,  XY)
> λ tranziție:   (q, b,  XY)
> citești b:     (q, λ,  Y)
> λ tranziție:   (r, λ,  Y)
> ```
> 
> Acceptat.
> 
> ---
> 
> Exemplu pentru "aab" (invalid, nu e aⁿbⁿ):
> 
> ```
> start:         (p, aab, Y)
> citești a:     (p, ab,  XY)
> citești a:     (p, b,   XXY)
> λ tranziție:   (q, b,   XXY)
> citești b:     (q, λ,   XY)
> input terminat, ești în q nu în r, stiva nu e goală
> ```
> 
> Nu poți face tranziția spre r pentru că vârful stivei e X nu Y. Respins.
