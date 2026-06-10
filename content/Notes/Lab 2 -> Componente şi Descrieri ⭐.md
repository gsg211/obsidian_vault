## Componentele Limbajelor și Formatele de Descriere (Metalimbaje)

Orice limbaj poate fi descompus într-o serie de elemente esenţial distincte: lexicale, sintactice sau/şi semantice.

Gruparea acestor componente în categorii, funcţie de rolul lor (funcţional, operativ, informativ, delimitativ, abreviativ etc.), oferă perspectiva unor descrieri compacte a limbajelor prin stabilirea doar a unui set de reguli de combinare a acestor elemente.

## Categorii de Componente ale Limbajelor

În general, componentele limbajelor aparţin uneia sau mai multora dintre categoriile următoare:

- Caractere speciale și separatori
- Comentarii
- Cuvinte rezervate și cuvinte cheie
- Identificatori și literali

- Operatori:
    - După numărul de operanzi: unari, binari, n-ari
    - După poziție: de prefixare, infixare, postfixare, delimitare
    - După funcționalitate: aritmetici, relaţionali, logici, de selecţie, de conversie, condiţionali, de atribuire, de secvenţiere

- Expresii
- Instrucţiuni: de atribuire, de intrare/ieşire, de control condiţionat, de ciclare, de transfer/salt, de subprogramare, de compilare condiţionată, de includere
- Metode
- Fraze sau blocuri multi-instrucţiune

## Formate de Descriere (Metalimbaje Sintactice)

Formatele de descriere ale limbajelor reprezintă modalităţi de caracterizare formal-sintetică a acestora plecând de la componentele limbajelor, prin utilizarea unor reguli specifice de combinare a elementelor acestora.

Limbajele de descriere (geometrizare) a unor informaţii, limbaje sau procese (transformări) se mai numesc şi metalimbaje sintactice.

Un format de descriere trebuie să îndeplinească următoarele criterii:

- Concise (simplu)

- Precis (fără ambiguităţi)

- Formal (cu reguli parsabile şi să poată fi implementat pe sisteme de calcul)

- Natural (cu notaţii sugestive)

- General (capabil de a descrie limbaje complexe)

- Auto-descriptiv (să se descrie complet pe sine)

- Liniar (să utilizeze expresii formate din şiruri de caractere)


## Formatul BNF (Backus–Naur Form / Backus Normal Form)

Formatul BNF este compus din producţii (reguli), specificate prin intermediul unor meta-simboluri.

### Meta-simboluri BNF

- `<..>` : marcarea producţiilor (delimitare nume)

- `::=` : operatorul de definire a unei producţii

- `|` : separator al definiţiilor alternative

- `".."` : delimitare simboluri lexicale sau terminale

### Exemple BNF


```c
<Cifra> ::= "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9"
<Natural> ::= <Cifra> | <Cifra> <Natural>
<Semn> ::= "+" | "-"
<Intreg> ::= <Semn> <Natural> | <Natural>
<Litera> ::= "A" | "B" | "C" | "D" | "E" | "F" | "G" | "H" | "I" | "J" | "K" | "L" | "M" | "N" | "O" | "P" | "Q" | "R" | "S" | "T" | "U" | "V" | "W" | "X" | "Y" | "Z" | "a" | "b" | "c" | "d" | "e" | "f" | "g" | "h" | "i" | "j" | "k" | "l" | "m" | "n" | "o" | "p" | "q" | "r" | "s" | "t" | "u" | "v" | "w" | "x" | "y" | "z"
<Cuvant> ::= <Litera> | <Litera> <Cuvant>
```

Exemplu practic: Cum analizează calculatorul numărul `358`?

Translatorul încearcă să verifice dacă `358` respectă regula `<Natural>`:

1. **Pasul 1:** Analizează `358`.
    - `3` este o `<Cifra>` validă.
    - Ce a rămas? `58`. Regula cere ca `58` să fie și el un `<Natural>`. Procesul se repetă (se auto-apelează).

2. **Pasul 2:** Analizează `58`.
    - `5` este o `<Cifra>` validă.
    - Ce a rămas? `8`. Regula cere ca `8` să fie și el un `<Natural>`. Procesul se auto-apelează din nou.

3. **Pasul 3:** Analizează `8`.
    - `8` este o `<Cifra>` validă.
    - De data aceasta, nu mai rămâne nimic după el. Ne oprim pentru că am ajuns la cazul de bază (Cazul 1: doar o singură cifră).
## Formatul WSN (Wirth Syntax Notation)

Formatul permite utilizarea unor descrieri iterative, spre deosebire de BNF care utilizează doar reguli recursive de compunere. O parte din aceste notaţii au fost incluse ulterior în EBNF.

### Meta-simboluri WSN

- `=` : operatorul de definire a unei producţii

- `.` : simbol de marcare a sfârşitului producţiei

- `|` : separator al definiţiilor alternative

- `[..]` : delimitare definiţii opţionale

- `{..}` : delimitare definiţii repetitive

- `(..)` : delimitare definiţii grupate

- `".."` sau `'..'` : delimitare stringuri terminale


## Formatul EBNF (Extended Backus–Naur Form)

EBNF extinde formatul clasic pentru a include structuri repetitive și opționale mai compacte.

### Meta-simboluri EBNF

- `=` : operatorul de definire a unei producţii
- `.` sau `;` : marcarea sfârşitului producţiei
- `|` : separator al definiţiilor alternative
- `[..]` : delimitare definiţii opţionale
- `{..}` : delimitare repetiţii
- `N*E` : multiplicare (repetare de N ori a elementului E)
- `(..)` : delimitare definiţii grupate
- `".."` sau `'..'` : delimitare stringuri terminale
- `,` : operator de concatenare
- `(*..*)` : comentariu
- `?..?` : secvenţă specială (extensie)
- `-` : excepţie (excludere)
### Exemple EBNF

Plaintext

```c
Cifra = "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9" ;
Natural = Cifra , {Cifra} ;
Semn = "+" | "-" ;
Intreg = [Semn] Natural ;

Paronime1 = "familia" ("l" | "r") ; 
//(* Echivalent cu *)
Paronime2 = "familial" | "familiar" ;
```

## Formatul ABNF (Augmented Backus–Naur Form)

ABNF utilizează producţii (reguli) case-insensitive pentru neterminali.

### Meta-simboluri și reguli ABNF

- `<..>` : delimitare opţională pentru numele regulilor
- `=` : definiţie
- `;` : comentariu linie
- spaţiu sau `.` : operatori pentru concatenare
- `/` : separator reguli alternative
- `=/` : adăugarea de noi reguli alternative
- `N*R` : multiplicare reguli (de N ori regula R)
- `(..)` : delimitare reguli grupate
- `[..]` : delimitare reguli opţionale
- `WSP` : spaţiu liniar
- `LWSP` : spaţiu linii
- `-` : operator de infixare pentru specificarea unui interval
- `%B..` : valoare numerică în baza/format B (unde b = binar, d = zecimal, x = hexa)
- `%s` : prefixare pentru constrângere case-sensitive
- `%i` : prefixare pentru case-insensitive (implicit)
- `".."` sau `'..'` : delimitare stringuri terminale    

### Exemple ABNF


```c
Digit = "0" / "1" / "2" / "3" / "4" / "5" / "6" / "7" / "8" / "9"

Cifra = %x30-39 ; //Ca valoare din interval, echivalent cu Digit

Natural = 1*Cifra

Fruct = "Mar" / "Pruna" / "Strugure"
Fruct =/ "Portocala"

CR = %x0D ; Carriage Return
LF = %x0A ; Line Feed

CRLF = CR LF
CRLF = %x0D.0A ; Varianta alternativa pentru CRLF
```

