
# Limbaje de Programare și Translatoare

> [!abstract] Definiție Generală
> 
> Se numeşte limbaj 
> (de programare/comunicare/reprezentare/descriere, protocolar, lingvistic etc.) 
> 
> un set bine definit de simboluri, expresii şi reguli 
> (lexicale, sintactice, semantice, pragmatice/semiotice) 
> 
> care permit descrierea sau transmiterea unor informaţii.

## Concepte Fundamentale 

- Lexicul reprezintă mulţimea atomilor/simbolurilor (tokeni).

- Sintaxa reprezintă regulile de combinare a simbolurilor lexicale.

- Semantica reprezintă sensul/înţelesul/atributul unor construcţii/combinaţii sintactico-lexicale (relaţiile dintre simboluri, obiecte şi reprezentări). =>SENSUL

- Pragmatica reprezintă capacitatea de a caracteriza/descrie (limbaj) sau a transmite/converti (translator) fidel/eficient informaţia (relevanţa/corelaţia cod-mesaj).

	La nivel de limbaj:Cât de fidel și ușor poți descrie o problemă din lumea reală în acel limbaj?
	
	Exemplu: Dacă vrei să faci inteligență artificială, Python este pragmatic (are biblioteci gata făcute, codul se scrie repede). Dacă vrei să scrii software pentru un stimulator cardiac, C sau Assembly sunt pragmatice (oferă control direct pe hardware și viteză maximă).

- Semiotica reprezintă relaţia simboluri – obiecte – interpretări.
$$\text{Semiotica} = \text{Sintaxa} + \text{Semantica} + \text{Pragmatica}$$

## Translarea și Translatoarele

- Se numeşte translator un sistem / procedeu / program de conversie a unor elemente descrise într-un limbaj în elementele corespunzătoare unui alt limbaj.
    
- Translarea reprezintă conversia:
    - unor date codate într-un format în date codate în alte formate;
    - a unor elemente din limbajul natural în limbaje/formate digitale;
    - a unor elemente lingvistice;
    - a unor informaţii aleatoriu distribuite în informaţii sistematizate (data/web mining).

> [!info] Ce este Programarea?
> 
> Programarea (descrierea/reprezentarea şi transformarea unor informaţii) este un proces de translare a unor intrări în ieşiri, proces intermediat de către un sistem de a cărui limbaj trebuie să ţinem cont în ultimă instanţă.

## Caracteristicile unui Limbaj / Translator

Un limbaj sau un translator poate să prezinte în grade diferite o serie de caracteristici precum:

- extensibilitatea
- compatibilitatea
-  eficienţa
- portabilitatea
- verificabilitatea
- integritatea
- modularitatea
- fiabilitatea
- ortogonalitatea
- lizibilitatea
- consistenţa
- uniformitatea
- demonstrabilitatea


## Niveluri de Descriere și Operare

Limbajele pot opera sau descrie structuri informativ corelate pe diferite niveluri:

1. Nivel Uman/Matematic – modalitatea de descriere specific umană utilizând elemente naturale (lingvistice) sau logice (matematice).

2. Nivel ridicat(înalt)/Simbolic (High-Level) – operarea cu elemente apropiate de limbajul natural dar adaptate/adresate unui alt sistem (digital, biologic etc.).

3. Nivel coborât – Sistem/Maşină (Low-Level) – operarea cu elemente apropiate de operaţiile efectuate de sistem (Procesor/Maşină/Entitate biologică).


## Arhitecturi ale Sistemelor de Calcul

> [!note] Arhitectura unui sistem de calcul
> 
> Arhitectura unui sistem de calcul reprezintă modalitatea în care sunt distribuite şi interconectate elementele fundamentale necesare procesării. Aceste denumiri sunt utilizate şi pentru stilurile de combinare date-coduri.

### Arhitectura Princeton (von Neumann)

- reprezintă baza majorităţilor arhitecturilor;
- permite accesul complet/combinat/nerestrictiv la zona de date şi cod.
### Arhitectura Harvard

- zonele de cod şi date sunt gestionabile separat (spaţial) dar simultan (temporal).