
[[LFT_Lab_all_in_one.pdf#page=7&selection=9,0,9,8|LFT_Lab_all_in_one, page 7]]
## Codarea

Codarea simbolurilor presupune în principal a indexa tipul simbolului 

şi secundar de a descrie / identifica anumite caracteristici ale acestuia (elemente de stil precum designul simbolului, înclinare, îngroşare etc.).

# UNICODE

este standardul pentru indexarea digitală a simbolurilor precum: caractere ale limbilor globului, scripturi antice, simboluri matematice, sigle etc

Simbolurile sunt grupate pe diferite categorii:
	Multilingual Basic (0000–FFFF) 
	Supplementary (10000–1FFFF)
	ideographic (20000–2FFFF) etc.

Referirea la un simbol Unicode se specifică prin: U+IndexHexa (Exemplu A=U+41)

utilizarea în diferite limbaje a unui simbol necesitând specificarea indexului acestuia (sub forma zecimală sau hexa) de exemplu :
	&#d      -> html folosind baza 10
	&#xh    -> html folosind baza 16
	\u.. etc -> programare

## Cele mai populare:

- ASCII

- 859-1..ISO/IEC-8859-16 (16 categorii ce codifică simboluri pentru diferite grupuri de limbi

- UCS-2 (2-byte Universal Character Set, codare pe 16 biți a simbolurilor Unicode în intervalul U+0000 .. U+FFFF)

- UTF-32 / UCS-4 (Unicode Transformation Format in 32 bits/4-byte Universal Character Set).


## Plasare octeti

in cazul codărilor multi-octet, ordinea plasări octeţilor poate fi de tip

Little-Endian (octetul nesemnificativ primul)

Big-Endian (octetul semnificativ primul) 

Middle/Mixed-Endian (combinaţii ale celor două).

Exemplu:

|          | MSB |     |     | LSB |
| :------: | :-: | :-: | :-: | :-: |
|  octeti  | 61  | 62  | 63  | 64  |
| caracter |  a  |  b  |  c  |  d  |
$$
61626364_{16}=abcd_{\ \ BigEndian}= dcba_{\ \ lilEndian} = badc_{\ \ mixEndian} 
$$

mixedEndian explicatie:
- face perechi:
	- Prima pereche: `[a][b]`
	- Perechea 2     :  `[c][d]`
- perechile raman in aceeasi ordine p1, p2 ...
- in interioru perechilor inversam
	- Prima pereche: `[b][a]`
	- Perechea 2     :  `[d][c]`
- => BADC
 
ABCDEFGH => BADCFEHG
AB  => BA
CD  => DC
EF   => FE
GH  => HG  

#### Cele mai reprezentative standarde de codare pe un număr variabil de octeţi: 

- UTF-8 (Unicode Transformation Format in 8 bits) 
- UTF-16 (Unicode Transformation Format in 16 bits)
## UTF-8

| **Interval Unicode (Hexa)** | **Câți octeți ocupă** |       **Șablonul binar UTF-8**        |                **Observații / Rolul biților ficși**                |
| :-------------------------: | :-------------------: | :-----------------------------------: | :----------------------------------------------------------------: |
|      U+0000 .. U+007F       |        1 octet        |              `0xxxxxxx`               |   Bitul `0` de start indică un singur octet (compatibil ASCII).    |
|      U+0080 .. U+07FF       |       2 octeți        |          `110xxxxx 10xxxxxx`          | Primul octet începe cu `110` (arată că urmează 2 octeți în total). |
|      U+0800 .. U+FFFF       |       3 octeți        |     `1110xxxx 10xxxxxx 10xxxxxx`      |     Primul octet începe cu `1110` (arată că urmează 3 octeți).     |
|     U+10000 .. U+10FFFF     |       4 octeți        | `11110xxx 10xxxxxx 10xxxxxx 10xxxxxx` |    Primul octet începe cu `11110` (arată că urmează 4 octeți).     |

nr de 1 urmat de zero 0 => cati octeti din acelasi simbol urmeaza  


## UTF 16

[[LFT_Lab_all_in_one.pdf#page=8&selection=64,1,84,25|LFT_Lab_all_in_one, page 8]]

### 1. Cazul simplu: Caractere pe 2 octeți (U+0000 .. U+FFFF)

U+0000 <--> U+FFFF.

Pentru majoritatea caracterelor folosite zilnic (alfabetul latin, diacritice, caractere chirilice, matematică uzuală), codul lor Unicode se încadrează între U+0000 și U+FFFF. 

În acest interval, UTF-16 nu face nicio transformare matematică. Pur și simplu ia valoarea Unicode și o salvează direct pe 2 octeți.

> [!warning] Intervalul interzis (U+D800 .. U+DFFF) 
> 
> intervalul U+D800..U+DFFF este rezervat pentru codări extinse.
> 
> Asta înseamnă că nicio literă din lume nu are voie să aibă codul nativ în acest interval. 
> 
> Aceste numere sunt folosite strict ca „piese de puzzle” (surrogate) pentru caracterele mari, după cum vedem mai jos.

### 2. Cazul extins: Caractere pe 4 octeți (U+10000 .. U+10FFFF)

(U+10000 .. U+10FFFF)

Când un caracter are un cod mai mare de U+FFFF (cum sunt emoji-urile sau hieroglifele complexe), 

el nu mai încape pe 2 octeți. Atunci UTF-16 folosește o tehnică numită: Perechi de Surrogate(Surrogate Pairs): 
- caracterul este spart în două bucăți de câte 2 octeți, numite **HS** (High Surrogate) și **LS** (Low Surrogate). impreuna ele ocupă 4 octeți.

### Algoritm

![[Pasted image 20260609153631.png]]

###### 1 ) $U - 10000_{16}$

Deoarece intervalul extins începe de la $10000_{16}$, primul lucru pe care îl face algoritmul este să scadă această valoare din codul caracterului ($U$). 

Rezultatul este o valoare compactă stocată pe 20 de biți (notată în text cu $U(20)$).

###### 2) $HS$

Formulă: $\text{HS} = D800_{16} + (U(20) \gg 10)$

pastraza cei mai semnificativi 10 biti  si ii aduna la 0xD800

###### 2) $LS$


Formulă: $\text{LS} = DC00_{16} + (U(20) \ \& \ 3FF_{16})$

- Se aplică o mască binară cu valoarea $3FF_{16}$ care în binar înseamnă zece de 1: `1111111111`. 

- Operația `&` (ȘI binar) păstrează doar ultimii 10 biți din dreapta ai numărului nostru.

- Acești 10 biți sunt adunați la valoarea de bază $DC00_{16}$. Rezultatul va fi un număr care se va afla mereu în intervalul $DC00 - DFFF$.


La final, translatorul scrie în fișier valoarea HS urmată de LS. 

Când un cititor de text vede un cod din zona $D800$, el știe instant că acela nu este un caracter de sine stătător, ci prima jumătate a unui caracter pe 4 octeți.

