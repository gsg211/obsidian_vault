[[SOC FINAL 2025.pdf#page=196&selection=13,0,14,1|SOC FINAL 2025, page 196]]

detecția erorilor în transmisia sau stocarea datelor

Codurile Redundante Ciclice (CRC) reprezintă o metodă eficientă de detectare a erorilor pentru blocuri de date.

block de date -> crc

valoarea de control este o **redundanță** (mărește mesajul fără a adăuga informație utilă)  i se bazează pe proprietățile codurilor **ciclice.**

Principiul de bază este împărțirea polinomială în aritmetică modulo 2 (unde adunarea și scăderea sunt echivalente cu operația logică XOR). 

Mesajul este tratat ca un polinom, care este împărțit la un polinom prestabilit, numit polinom generator. Câtul împărțirii este ignorat, iar restul obținut constituie codul CRC

Cel mai simplu exemplu de CRC este bitul de paritate, care este un CRC pe 1 bit ce folosește polinomul generator x + 1


#### Probleme

nu protejeaza datele
Un atacator poate modifica mesajul și recalcula valoarea CRC, astfel încât modificarea să nu fie detectată
Spre deosebire de funcțiile hash criptografice, funcția CRC este ușor reversibilă

crc(x) ⊕ crc(y) = crc(x ⊕ y). Aceasta permite manipularea unui mesaj criptat și a CRC-ului său asociat fără a cunoaște cheia de criptare.

### Polinom

$$
exemplu=x^4 + x + 1
$$

| coeficient  | 4   | 3   | 2   | 1   | 0   |
| ----------- | --- | --- | --- | --- | --- |
| valoare bit | 1   | 0   | 0   | 1   | 1   |

Reprezentari
eliminam MSB
Normal:  => 0011 = 0x3 PT cand MSB e proc primu
Invers: 1100 = 0xC PT cand LSB e proc primu

Inversat reciproc (Koopman) (0x9)  1001 => Elimini x^0

ORDINEA BIȚILOR Specifică dacă biții din fiecare octet al mesajului sunt procesați de la MSB la LSB (direct) sau de la LSB la MSB (inversat / reflected).
### Exemplu

se adauga 3 biti (astia sunt bitii de crc-> nr de grad)
se face xor intre polinom si mesaj

![[Pasted image 20260130002745.png]]

divizorul este mutat cu o poziţie în dreapta și procesul se repetă până când divizorul ajunge în partea dreaptă a mesajului iniţial. Mai jos este prezentat calculul complet

![[Pasted image 20260130003114.png]]

verificare crc

![[Pasted image 20260130003326.png]]

# metode calcul

> [!danger]
>  Se lasă debifată căsuţa Initialize unused interrupt vectors with RETI instructions pentru a evita un conflict cu opţiunea Fill unused code memory din categoria Linker
> 
> [[SOC FINAL 2025.pdf#page=202&selection=29,0,48,1|SOC FINAL 2025, page 202]]

Linker-ul din IAR poate fi configurat pentru a genera automat un CRC pentru codul programului. Acest lucru se face din Options -> Linker -> Checksum

[[SOC FINAL 2025.pdf#page=203&selection=48,1,208,4|SOC FINAL 2025, page 203]]

### Bit cu bit

Aceasta este implementarea directă a împărțirii polinomiale. Mesajul este procesat bit cu bit, iar pentru fiecare bit se efectuează o operație XOR cu polinomul generator, dacă este cazul. Deși este simplu de înțeles, este cea mai lentă metodă.

### ALGORITM BAZAT PE TABELE (PRE-CALCULAT)

Pentru mărirea vitezei, se poate pre-calcula rezultatul împărțirii pentru toți cei 256 de octeți posibili și stoca într-un tabel. La execuție, se procesează mesajul octet cu octet, folosind tabelul pentru a actualiza valoarea CRC, ceea ce elimină buclele de procesare per bit și crește semnificativ viteza.

# Exercitiu

Cerință: Să se realizeze o aplicație care calculează și verifică valoarea CRC-16 a unui bloc de date stocat în memoria Flash. 

Blocul de date are dimensiunea 128 KB și este stocat în zona de adresă 0x000000 – 0x01FFFF (spațiul Flash). 

Valoarea „reală” a CRC-ului pe 16 biți este salvată imediat după blocul de date, la adresele 0x020000 – 0x020003 (2 octeți CRC + un eventual padding / rezervă). 

CRC-ul trebuie calculat folosind două metode: 
1. Metoda „bit cu bit” (fără tabelă); 
2. Metoda cu tabele precalculate (lookup table).

[[SOC FINAL 2025.pdf#page=203&selection=211,0,215,2|SOC FINAL 2025, page 203]]

