
[[LFT_Lab_all_in_one.pdf#page=21&selection=9,0,9,8|LFT_Lab_all_in_one, page 21]]

succes
![[Pasted image 20260609202642.png]]

## Descriere

O gramatică G reprezintă o colecţie formată din mulţimi de simboluri şi reguli de combinare ale acestora aferente descrierii sau transmiterii unor informaţii, adică este definită de cvadruplul (4-uplul):

$$
G = < N, Σ, P, S>
$$



> [!quote] explicatie
> Ce este o gramatică formală?
> 
> O gramatică este un sistem formal care descrie cum se construiesc cuvintele sau propozițiile dintr-un limbaj. Exact ca gramatica limbii române care îți spune cum combini cuvintele, doar că aici totul e matematic și precis.
> 
> 
> Cei 4 componenți: G = (N, Σ, P, S)
> 
> **1. N — Alfabetul neterminalelor**
> 
> Sunt simboluri "de lucru", intermediare. Nu apar în rezultatul final, ci reprezintă categorii sau structuri. Se scriu cu majuscule: A, B, S, Expr, etc.
> 
> Gândește-te la ele ca la categorii gramaticale: "substantiv", "verb": nu sunt cuvinte reale, ci etichete.
> 
> **2. Σ (Sigma) — Alfabetul terminalelor**
> 
> Sunt simbolurile "finale", cele care chiar apar în cuvintele limbajului. Se scriu cu minuscule: a, b, 0, 1, etc.
> 
> Acestea sunt cărămizile reale din care e construit limbajul.
> 
> **3. P — Mulțimea producțiilor (regulilor)**
> 
> O producție arată cum înlocuiești un simbol cu altul. Are forma: α → β, adică "ori de câte ori vezi α, îl poți înlocui cu β".
> 
> Exemplu: A → aB înseamnă "A se poate transforma în litera a urmată de B"
> 
> **4. S — Simbolul de start**
> 
> Este neterminalul din care începe orice derivare. Tot limbajul se construiește pornind de la S.
> 
> 
> 
>  Derivarea — cum generezi un cuvânt
> 
> Pornești din S și aplici reguli din P pas cu pas, până rămâi doar cu terminale.
> 
> Exemplu simplu cu G = ({S, A}, {a, b}, P, S):
> 
> ```
> P: S → aA
>    A → b
> 
> S => aA => ab
> ```
> 
> Deci cuvântul "ab" aparține limbajului.
> 
> Săgeata simplă → este o regulă. Săgeata dublă => înseamnă "derivează în", adică am aplicat o regulă.
> 
> ---
> 
>  Limbajul generat L(G)
> 
> Este mulțimea TUTUROR cuvintelor ce pot fi derivate din S urmând regulile P:
> 
> L(G) = { x | S =>* x, unde x conține doar terminale }
> 
> Simbolul =>* înseamnă "derivează în zero sau mai mulți pași".
> 
> Simbolul λ sau ε (șirul vid)
> Este cuvântul fără niciun simbol, lungime zero. Util când o regulă poate "șterge" ceva: A → ε înseamnă că A poate dispărea.
> 


[[gramatica_formala_G.svg]]
![[gramatica_formala_G.svg]]

---

## Ierarhia Chomsky

> [!quote]+ Explicatie
> Ierarhia Chomsky împarte gramaticile în 4 tipuri, de la cel mai restrictiv la cel mai general. Relația G³ ⊂ G² ⊂ G¹ ⊂ G⁰ înseamnă că orice gramatică de tip 3 este și de tip 2, orice tip 2 este și tip 1, și așa mai departe — deci tipul 0 le conține pe toate.
> 
> ---
> 
> **Tip 3 — Gramatici regulate (cele mai restrictive)**
> 
> Regulile au exact forma A → a sau A → aB (liniare dreapta), respectiv A → Ba (liniare stânga). Un singur neterminal în stânga, și în dreapta fie doar un terminal, fie un terminal urmat de un neterminal.
> 
> Exemplu: limbajul aⁿb, adică a repetat de n ori urmat de un singur b: {ab, aab, aaab, ...}
> 
> ```
> S → aS
> S → b
> ```
> 
> Derivare pentru "aaab":
> S => aS => aaS => aaaS => aaab
> 
> Simplu, mecanic, fără memorie. Nu poți număra sau "ține minte" cât ai mers.
> 
> ---
> 
> **Tip 2 — Gramatici independente de context**
> 
> Regula are forma A → γ, unde în stânga e mereu un singur neterminal, dar în dreapta γ poate fi orice șir de terminale și neterminale. Nu contează contextul în care apare A.
> 
> Exemplu clasic: limbajul aⁿbⁿ, adică exact n litere a urmate de exact n litere b: {ab, aabb, aaabbb, ...}
> 
> Asta nu e posibil în tip 3, pentru că ar trebui să "numeri" câte a-uri ai pus. Cu tip 2:
> 
> ```
> S → aSb
> S → ab
> ```
> 
> Derivare pentru "aaabbb":
> S => aSb => aaSbb => aaabbb
> 
> Regula S → aSb adaugă simultan câte un a în față și un b în spate, deci numărul lor rămâne mereu egal. Elegant.
> 
> Motivul că e "independent de context": regula S → aSb se aplică indiferent ce se află în jurul lui S.
> 
> ---
> 
> **Tip 1 — Gramatici dependente de context**
> 
> Regula are forma αAβ → αγβ, unde A se rescrie cu γ, dar numai când se află între α în stânga și β în dreapta. Contextul contează. Condiția de monotonie spune că |γ| ≥ 1, adică nu poți șterge simboluri (cu excepția simbolului de start S → λ dacă S nu apare în dreapta niciunei reguli).
> 
> Exemplu: limbajul aⁿbⁿcⁿ, adică exact n litere a, n litere b, n litere c: {abc, aabbcc, aaabbbccc, ...}
> 
> Asta nu e posibil cu tip 2. Ai nevoie de context:
> 
> ```
> S → aSBC
> S → aBC
> CB → BC       (regula dependentă de context: C în contextul urmând după B se mută)
> aB → ab
> bB → bb
> bC → bc
> cC → cc
> ```
> 
> Derivare pentru "aabbcc":
> S => aSBC => aaBCBC => aaBBCC => aabBCC => aabbCC => aabbcC => aabbcc
> 
> Regula CB → BC nu e de forma unui singur neterminal în stânga — are doi simboluri. Asta e esența tipului 1: rescrierea unui simbol depinde de vecinii lui.
> 
> ---
> 
> **Tip 0 — Gramatici nerestricționate (cele mai generale)**
> 
> Regula e pur și simplu α → β, fără nicio constrângere pe formă sau lungime. α poate fi orice șir nevid, β poate fi orice șir inclusiv λ. Poți șterge, poți rescrie arbitrar.
> 
> Exemplu: limbajul {aⁿbⁿcⁿdⁿ | n ≥ 1} sau limbaje care nu pot fi descrise deloc cu celelalte tipuri. Corespund mașinilor Turing, deci sunt cele mai puternice, dar și cele mai greu de analizat — nu există întotdeauna un algoritm care să decidă dacă un cuvânt aparține limbajului.
> 
> ---
> 
> **De ce contează incluziunea strictă G³ ⊂ G² ⊂ G¹ ⊂ G⁰?**
> 
> Incluziunea e strictă, adică la fiecare nivel există limbaje care nu pot fi descrise de nivelul inferior. Concret:
> 
> - aⁿb este de tip 3, dar aⁿbⁿ nu este — ai nevoie de tip 2
> - aⁿbⁿ este de tip 2, dar aⁿbⁿcⁿ nu este — ai nevoie de tip 1
> - aⁿbⁿcⁿ este de tip 1, dar există limbaje (recursiv enumerabile) care cer tip 0
> 
> Cu cât coborî în ierarhie (spre 0), cu atât gramatica e mai expresivă, dar și mai greu de procesat automat. Compilatoarele de exemplu folosesc în principal tip 2 (gramatici fără context), pentru că sunt suficient de expresive pentru limbaje de programare și există algoritmi eficienți de parsare pentru ele.


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


> [!example]+ explicatie exemplu
> Această gramatică este de tip 0 (fără restricții) deoarece regulile de producție
> pot avea orice formă. Mai exact, există reguli în care:
> 
>   - Partea stângă a producției conține atât simboluri neterminale, cât și
>     terminale. De exemplu, regula bB→bbc are în partea stângă un simbol terminal
>     (b) și un simbol neterminal (B). Gramaticile de tip 1 (context-sensibile) și
>     tip 2 (context-libere) nu permit simboluri terminale în partea stângă a
>     producțiilor.
>   - Lungimea părții drepte a unei producții poate fi mai mică decât lungimea
>     părții stângi. De exemplu, dacă am avea o regulă de forma X Y -> Z, unde
>     lungimea din dreapta este mai mică, aceasta ar fi specifică gramaticilor de
>     tip 0. În cazul tău, deși nu ai un exemplu exact de scurtare (cum ar fi AB
>     -> A), regulile cu terminale în partea stângă sunt suficiente pentru a o
>     clasifica drept tip 0.
> 
> Pentru a fi mai specific:
> 
>   - Gramaticile de tip 2 (context-libere) ar permite doar un singur neterminal
>     în partea stângă (ex: A -> aAB). Regula bB→bbc nu respectă această cerință.
>   - Gramaticile de tip 1 (context-sensibile) ar permite șiruri de simboluri în
>     partea stângă, dar cu condiția ca lungimea șirului din dreapta să fie mai
>     mare sau egală cu lungimea șirului din stânga. De asemenea, nu permit
>     terminale în partea stângă a producțiilor. Regula bB→bbc încalcă această
>     cerință.
> 
> Prin urmare, prezența regulii bB→bbc care are un simbol terminal (b) în partea
> stângă a producției este principalul motiv pentru care această gramatică este de
> tip 0.
> 


---

#### Exemplu de gramatică de tip 1:

G = 〈{A, B, C, D, E}, {a, b, c},
{A→aAB|abC, CB→DB, DB→DE, DE→BE, BE→BC, bB→bbc, C→c}, A〉

care generează de asemenea limbajul
L = {anbncn| n≥1} = {abc, aabbcc, ..}

> Explicatie: Spre deosebire de exemplul tip 0, regulile de tip 1 sunt "monotone" — lungimea șirului nu scade niciodată în urma aplicării unei reguli (|stânga| ≤ |dreapta|). Transformarea unui simbol depinde de ce îl înconjoară (context). Observă că același limbaj {anbncn} se poate descrie și cu tip 0 și cu tip 1 — tipul 1 e însă mai controlat.


> [!example]+ explicatie
> A → aAB — un singur neterminal în stânga, e de tip 2 deci implicit și tip 1. Ok.
> 
> A → abC — un singur neterminal în stânga, e de tip 2 deci implicit și tip 1. Ok.
> 
> CB → DB — uită-te atent. C se rescrie ca D, dar numai când are B în dreapta lui. B rămâne neatins în dreapta. Deci contextul e β = B, α = λ, și C se transformă în D în contextul lui B. Forma e exact αAβ → αγβ, adică CB → DB. Tip 1. Ok.
> 
> DB → DE — același lucru. B se rescrie ca E, dar numai când are D în stânga. Contextul α = D rămâne neatins. Tip 1. Ok.
> 
> DE → BE — E rămâne neatins în dreapta, D se rescrie ca B în contextul lui E. Tip 1. Ok.
> 
> BE → BC — E se rescrie ca C în contextul lui B din stânga. Tip 1. Ok.
> 
> bB → bbc — aici b e terminal și rămâne neatins în stânga, B se rescrie ca bc în contextul lui b. Deci α = b, A = B, β = λ, γ = bc. Lungimea crește de la 2 la 3, deci e monotonă. Tip 1. Ok.
>
C → c — un singur neterminal în stânga, se rescrie ca un terminal. Tip 2 implicit tip 1. Ok.

---

#### Exemplu de gramatică de tip 2:

G = 〈{S}, {a, b}, {S→aSb|ab}, S〉

care generează limbajul L = {anbn| n≥1} = {ab, aabb, aaabbb, ..}

> Explicatie: Aceasta este o gramatică clasică independentă de context. Regula S→aSb spune: pune un 'a' în față și un 'b' în spate, recursiv. Regula S→ab oprește recursivitatea. Rezultatul sunt șiruri cu n litere 'a' urmate exact de n litere 'b'. Stiva unui automat pushdown "ține minte" câte 'a'-uri s-au văzut, ca să verifice că sunt tot atâtea 'b'-uri.

> [!example]+ explicatie
> Uită-te la cele două reguli:
> 
> S → aSb — un singur neterminal S în stânga, în dreapta un mix de terminale și neterminale. Forma A → γ, clasic tip 2.
> 
> S → ab — un singur neterminal în stânga, doi terminali în dreapta. Tot forma A → γ, clasic tip 2.
> 
> Nicio regulă nu are doi simboluri în stânga, deci nu e tip 1 strict. Ambele reguli au un singur neterminal în stânga, deci sunt perfect tip 2.
> 
> ---
> 
> De ce nu e tip 3?
> 
> La tip 3 singurele forme permise sunt A → a sau A → aB, adică în dreapta ai un terminal urmat de cel mult un neterminal, și neterminalul e mereu la sfârșit.
> 
> Regula S → aSb strică asta în două moduri. În dreapta are un terminal a, apoi un neterminal S, apoi iar un terminal b. Neterminalul nu e la sfârșit, e în mijloc, cu un terminal după el. Asta e imposibil în tip 3.
> 
> Și are sens intuitiv — tip 3 nu poate număra, iar aⁿbⁿ cere să numeri câte a-uri ai pus ca să pui același număr de b-uri. Tocmai S → aSb face asta, adăugând simultan un a în față și un b în spate, ceea ce tip 3 nu poate exprima.
> 
> ---
> 
> Deci e strict tip 2 și nu tip 3 din cauza regulii S → aSb care are un terminal după neterminal în dreapta, și nu tip 1 strict pentru că nicio regulă nu are context, adică niciodată nu ai doi simboluri în stânga.



---

#### Exemplu de gramatică de tip 3:

G = 〈{S, A, B}, {a, b}, {S→aA, A→aA| bB, B→bB|λ}, S〉

care generează limbajul L = {ambn| m, n ≥1} = {ab, aab, abb, aabb, ..}

> Explicatie: Gramatica de tip 3 (regulată) are reguli de forma A→aB sau A→a — mereu un terminal urmat de cel mult un neterminal, toate "spre dreapta". Aceasta corespunde direct unei expresii regulate: a+b+ (unul sau mai mulți de 'a', urmat de unul sau mai mulți de 'b'). Simbolul λ (șirul vid) din B→bB|λ înseamnă că B poate să dispară, terminând derivarea.


> [!example]+ explicatie
> Uită-te la regulile:
> 
> ```
> S → aA
> A → aA | bB
> B → bB | λ
> ```
> 
> Toate regulile au un singur neterminal în stânga. În dreapta fie ai un terminal urmat de un neterminal, fie ai λ. Să verificăm fiecare:
> 
> S → aA — terminal urmat de neterminal. Forma A → aB. Tip 3. Ok.
> 
> A → aA — terminal urmat de neterminal. Forma A → aB. Tip 3. Ok.
> 
> A → bB — terminal urmat de neterminal. Forma A → aB. Tip 3. Ok.
> 
> B → bB — terminal urmat de neterminal. Forma A → aB. Tip 3. Ok.
> 
> B → λ — neterminal devine șirul vid. Tip 3 permite asta ca caz special.
> 
> ---
> 
> Toate regulile respectă forma tip 3, deci gramatica asta e de fapt tip 3, nu tip 2. Limbajul aᵐbⁿ cu m, n ≥ 1 poate fi descris de o gramatică regulată pentru că nu cere sincronizare între numărul de a-uri și numărul de b-uri. Sunt independente — oricâte a-uri vrei urmate de oricâte b-uri vrei, fără nicio constrângere între ele.
> 
> Dacă ar fi fost aⁿbⁿ, adică același număr, atunci ai fi avut nevoie de tip 2. Dar aici m și n sunt complet independente, deci tip 3 e suficient.


---

## Forma normala (pentru tip 2)

O gramatică de tip 2 (independentă de context) este în formă normală de mai multe tipuri:

#### Chomsky

Dacă regulile sale sunt de tipul: A→BC, A→a, S→λ.

Caracteristica este utilă în etapa de preprocesare (procesare textuală) de tip bottom-up (bottom-up/LR parsing), deoarece permite coborârea mai întâi până la nivelul fundamental/bottom (caracter / terminal) după care se procesează extinderi (up) ale structurilor mai complexe (incluzive).

> [!quote] explicatie1
> A → BC, adică un neterminal se rescrie ca exact doi neterminali. Niciodată trei, niciodată unul singur.
> 
> A → a, adică un neterminal se rescrie ca exact un terminal. Niciodată doi terminali, niciodată un mix.
> 
> S → λ, adică simbolul de start poate genera șirul vid, dar numai dacă S nu apare în dreapta niciunei reguli.

> [!quote]+ explicatie 2
> La Chomsky regulile sunt A → BC sau A → a.
> 
> În parsarea bottom-up pornești de la cuvântul final, de exemplu "aabbcc", și încerci să urci spre S, adică inversezi regulile.
> 
> ---
> 
> Gândește-te că inversul regulilor Chomsky e:
> 
> BC → A, adică dacă vezi doi neterminali alăturați, îi poți combina într-unul singur.
> 
> a → A, adică dacă vezi un terminal, îl poți ridica la neterminalul corespunzător.
> 
> ---
> 
> De ce e convenabil? Pentru că forma normală Chomsky garantează că arborele e binar — fiecare nod are exact doi copii. Asta înseamnă că la fiecare pas de reducere combini exact două simboluri într-unul. Niciodată trei, niciodată unul singur (în afară de terminale).
> 
> Deci algoritmul e simplu și uniform: uită-te la perechi de simboluri alăturate, dacă există o regulă care le combină, aplică-o. Continui până rămâi cu S.
> 
> ---
> 
> Exemplu simplu. Să zicem că ai gramatica:
> 
> ```
> S → AB
> A → a
> B → b
> ```
> 
> Și vrei să parsezi "ab" bottom-up:
> 
> ```
> ab
> => Ab      (a → A)
> => AB      (b → B)
> => S       (AB → S)
> ```
> 
> La fiecare pas fie ridici un terminal la neterminalul lui, fie combini o pereche de neterminali. Forma Chomsky garantează că mereu lucrezi cu perechi exacte, ceea ce face algoritmul predictibil și eficient.

#### Greibach

> [!quote]+ Explicatie
> Forma normală Greibach e tot o restricție pe gramatici de tip 2, similară cu Chomsky, dar cu o filozofie diferită.
> 
> Regula impusă e că orice regulă trebuie să înceapă obligatoriu cu un terminal, adică A → aα, unde a e un terminal și α e un șir de neterminali (poate fi și vid). Deci primul simbol din dreapta e mereu un terminal, urmat de oricâți neterminali vrei.
> 
> ---
> 
> De ce contează că nu e stâng recursivă?
> 
> O gramatică e stâng recursivă dacă ai o regulă de forma A → Aα, adică A apare primul în dreapta propriei reguli. Asta e o problemă fatală pentru parsarea top-down, pentru că dacă încerci să expandezi A, prima chestie pe care o faci e să expandezi din nou A, care expandează din nou A, la infinit. Bucla nu se mai oprește niciodată.
> 
> Greibach elimină asta forțat, pentru că primul simbol din dreapta e mereu un terminal, niciodată un neterminal. Deci A → Aα e imposibilă prin definiție în această formă.
> 
> ---
> 
> De ce e utilă pentru top-down?
> 
> În parsarea top-down începi de la S și cobori spre terminale, încercând să potrivești caracterele din cuvântul de intrare de la stânga la dreapta. La fiecare pas când aplici o regulă, primul lucru generat e un terminal. Asta înseamnă că la fiecare pas "consumi" cel puțin un caracter din cuvântul de intrare.
> 
> Dacă cuvântul are n caractere, știi că după exact n pași ai terminat, pentru că la fiecare pas ai consumat câte unul. De aia se spune că parsarea se oprește după maxim n pași — n e lungimea cuvântului de intrare.
> 
> Cu Chomsky în schimb poți aplica reguli de forma A → BC care nu consumă niciun terminal, deci nu știi exact când te oprești, ceea ce e incomod pentru top-down.
#### Operator

Dacă regulile sale nu conţin consecutiv două simboluri neterminale, adică sunt de tipul A→..BaC...

Regulile gramaticii în forma normală Operator permit ancorarea expresiilor (prin intermediul simbolurilor terminale separatoare de tip a) corespunzând gramaticilor expresiilor (aritmetice) sau de precedenţă.


> [!quote]+ Explicatie
> Forma normală Operator spune că în dreapta oricărei reguli nu ai voie să ai doi neterminali consecutivi, adică mereu între orice doi neterminali trebuie să existe cel puțin un terminal.
> 
> Deci A → BC e interzisă, dar A → BaC e permisă, pentru că a separă cele două neterminale.
> 
> ---
> 
> De ce are sens asta?
> 
> Gândește-te la expresii aritmetice. Când scrii 3 + 4 * 5, operatorii + și * sunt exact terminalele separatoare dintre operanzi. Niciodată nu ai doi operanzi lipiți fără un operator între ei. Forma normală Operator capturează tocmai această structură naturală.
> 
> ---
> 
> Exemplu cu expresii aritmetice:
> 
> ```
> E → E + E
> E → E * E
> E → a
> ```
> 
> Aici E e neterminalul pentru expresie, + și * sunt terminale separatoare, și niciodată nu apar doi E consecutivi fără un terminal între ei. Asta e exact forma normală Operator.
> 
> ---
> 
> Termenul "ancorare" se referă la faptul că terminalele separatoare fixează structura expresiei. Când parsezi, operatorii îți spun exact unde se termină un operand și unde începe altul. Ei sunt punctele de reper față de care organizezi tot restul.
> 
> De aia e utilă pentru expresii de precedență — poți folosi terminalele separatoare ca să determini care operator leagă mai strâns, adică * înainte de +, fără să ai ambiguitate în structura regulilor.
> 
> ---
> 

#### Kuroda

Dacă regulile sale sunt de tipul: AB→CD, A→BC|B|a/λ.

Regulile gramaticii în forma normală Kuroda corespund gramaticilor monotone (monotonic/linear bounded/noncontracting grammar).

Dacă C≡A gramatica devine de tip 1 (dependentă de context), numită şi formă normală Penttonen.

> [!quote]+ explicatie
> Forma normală Kuroda permite patru tipuri de reguli:
> 
> ```
> AB → CD
> A → BC
> A → a
> A → λ
> ```
> 
> ---
> 
> Regula AB → CD e cea care face Kuroda special față de tot ce am discutat până acum. Până acum în toate formele normale aveai mereu un singur neterminal în stânga. Aici ai doi neterminali simultan în stânga, și ambii se transformă simultan în alți doi neterminali.
> 
> Nu e că A se transformă în contextul lui B ca la tip 1. Ci A și B împreună, ca pereche, produc împreună CD. E o transformare simultană a două simboluri.
> 
> ---
> 
> De ce corespunde gramaticilor monotone?
> 
> Monoton înseamnă că lungimea șirului nu scade niciodată în timpul derivării. Uită-te la reguli:
> 
> AB → CD produce exact tot atâtea simboluri câte consumă, doi în stânga doi în dreapta, deci lungimea rămâne egală.
> 
> A → BC crește lungimea cu unu.
> 
> A → a păstrează lungimea egală.
> 
> A → λ scade lungimea, dar e permisă doar pentru simbolul de start în condiții speciale, la fel ca la tip 1.
> 
> Deci în general șirul fie crește fie rămâne la fel, niciodată nu scade. De aia se numește monotonă sau noncontracting.
> 
> ---
> 
> Legătura cu tip 1 și Penttonen.
> 
> Dacă iei regula AB → CD și impui că C trebuie să fie același simbol ca A, adică A rămâne neschimbat și doar B se transformă în D, regula devine AB → AD. Asta e echivalent cu a spune că B se rescrie ca D dar numai când are A în stânga lui, adică exact definiția tipului 1 dependent de context. Contextul A din stânga rămâne neatins, și doar B se transformă.
> 
> Penttonen e practic Kuroda cu această restricție în plus, și de asta corespunde exact tipului 1.
> 
> ---
> 
> Deci Kuroda e mai general decât tip 1 dar mai restrictiv decât tip 0, și se potrivește exact cu clasa gramaticilor monotone, adică cele care nu scurtează niciodată șirul în timpul derivării.
