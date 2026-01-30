
| Comunicare  | Directie                  |
| ----------- | ------------------------- |
| Simplex     | Aceeasi directie mereu    |
| Half Duplex | ambele directii pe rand   |
| Full Duplex | ambele directii simulatan |


$$
baud\ rate = (Foscilator / 16 / baud )-1
$$
### COMUNICAȚIA ASINCRONĂ – UART

Universal Asynchronous Receiver-Transmitter
asincron => nu exista clock comun


Biţii de START şi de STOP încadrează fiecare caracter transmis; caracterul transmis între aceşti 2 biţi reprezintă un cadru de date. 

În cazul comunicaţiei asincrone, intervalul de timp între transmisia a 2 caractere succesive este variabil, pe durata acestui interval linia de comunicaţie fiind în starea 1 logic. Acest mod de comunicaţie este numit şi START-STOP

transmisia caracterului începe cu bitul cel mai puţin semnificativ (b0)


Bitul de paritate este opţional
Există şi posibilitatea ca bitul de paritate să fie setat la 0 sau 1, indiferent de paritatea efectivă a caracterulu (poti folosi asta ca o forma de adresare)


Sincronizarea la nivel de bit se realizează cu ajutorul semnalelor de ceas locale cu aceeaşi frecvenţă. Atunci când receptorul detectează începutul unui caracter indicat prin bitul de START, porneşte un oscilator de ceas local, care permite eşantionarea corectă a biţilor individuali ai caracterului. Eşantionarea biţilor se realizează aproximativ la mijlocul intervalului corespunzător fiecărui bit.

### COMUNICAȚIA SINCRONĂ – USART

universal synchronous and asynchronous receiver-transmitter

In cazul comunicaţiei sincrone, un cadru nu conţine un singur caracter, ci un bloc de caractere sau un mesaj. Sincronizarea la nivel de bit trebuie asigurată permanent, nu numai în timpul transmisiei propriu-zise, ci şi în intervalele de pauză.

fără biţi de START şi STOP. 

De aceea, timpul este divizat în mod continuu în intervale elementare la transmițător, intervale care trebuie regăsite apoi la receptor

Dacă datele de transmis constau din şiruri lungi de 1 sau de 0, trebuie inserate tranziţii suficiente pentru resincronizarea ceasurilor. Asemenea tehnici sunt dificil de implementat, astfel încât se utilizează de obicei o tehnică numită comunicaţie asincronă sincronizată (numită în mod simplu comunicaţie sincronă).



Pentru ajustarea oscilatorului local la începutul unui mesaj, fiecare mesaj este precedat de un număr de caractere speciale de sincronizare, de exemplu, caracterul SYN (0x16). Pentru menţinerea sincronizării, se pot insera caractere de sincronizare suplimentare în mesajul transmis, la anumite intervale de timp.



La receptor există trei nivele de sincronizare:

• Sincronizare la nivel de bit, utilizând circuite cu calare de fază PLL (Phase–Locked Loop), pe baza
tranziţiilor existente în semnalul recepţionat;

• Sincronizare la nivel de caracter, asigurată prin recunoaşterea anumitor caractere de sincronizare;

• Sincronizare la nivel de bloc sau mesaj, care depinde de protocolul de date utiliza




# Usart IAR

![[Pasted image 20260130213913.png]]

Modul sincron (Synchronous): funcționează ca Master (generează CLK-ul) sau Slave (primește)


 BAUD (Baud Rate) – Rezultatul dorit

mod normal => esantioneaza de 16 ori pt a fi sigur ca citirea s=a facut in siguranta

viteza duble => easantioneaza doar de 8 ori : mai sensibil la zgomot dar se pot obtine viteze mai mari

 În Modul Sincron (2) Deoarece există un fir de ceas care spune exact când să citești, nu mai e nevoie de atâtea verificări, deci divizarea este minimă.

## Initializare


1. Setarea Baud Rate-ului: se calculează valoarea UBRR și se încarcă registrele UBRRH și UBRRL. 

2. Setarea Formatului de Cadru: se configurează numărul de
biți de date, biți de paritate și biții de stop în registrul UCSRC.

3. Activarea Transmițătorului și / sau a Receptorului: se setează biții TXEN și / sau RXEN din reg. UCSRB.
## Registri

N => nr modulului usart folosit

### UDR  
USART I/O DATA REGISTE
registru 8bit  => buffer transmisie/receptie

### UCSRNA – USART CONTROL AND STATUS REGISTER A

contine flaguri 
mod dublare viteza
mod multiprocesor

RXCn (Bitul 7): Receive Complete
Acest flag este 1 când în buffer-ul de receptive există date necitite. Poate genera o întrerupere.

• TXCn (Bitul 6): Transmit Complete
Acest flag este 1 când un frame a fost transmis și buffer-ul de transmisie este gol. Poate genera o
întrerupere.

• UDREn (Bitul 5): USART Data Register Empty
Acest flag este 1 când buffer-ul de transmisie este gol și gata să primească un nou caracter pentru a fi
transmis. Poate genera o întrerupere.

• FEn (Bitul 4): Frame Error
Setat pe 1 dacă este detectată o eroare la un frame (ex: bitul de stop este 0).

• DORn (Bitul 3): Data OverRun
Setat pe 1 dacă o suprascriere de date are loc în buffer-ul de receptive.

• UPEn (Bitul 2): Parity Error
Setat pe 1 dacă este detectată o eroare de paritate.
• 
U2Xn (Bitul 1): Double the USART Transmission Speed
Când este setat pe 1 se dublează viteza de transfer în modul asincron.

• MPCMn (Bitul 0): Multi-Processor Comunication Mode
Activează modul de comunicare multi-procesor.

### UCSRNB – USART CONTROL AND STATUS REGISTER B

control intreruperi
control rx,tx
marime caract (5-9)
bit extra (receive transmit pt modul 9)

RXCIEn (Bit 7): RX Complete Interrupt Enable
Activează întreruperea pe flag-ul RXC.

• TXCIEn (Bit 6): TX Complete Interrupt Enable
Activează întreruperea pe flag-ul TXC.

• UDRIEn (Bit 5): USART Data Register Empty Interrupt Enable
Activează întreruperea pe flag-ul UDRE.

• RXENn (Bit 4): Receiver Enable
Activează receptorul USART.

• TXENn (Bit 3): Transmitter Enable
Activează transmițătorul USART.

• UCSZn2 (Bit 2): Character Size
Folosit împreună cu biții UCSZ1:0 pentru a seta numărul de biți de date (5-9).

• RXB8n (Bit 1): Receive Data Bit 8
Al 9-lea bit de date recepționat (în modul 9 biți de date).

• TXB8 (Bit 0): Transmit Date Bit 8
Al 9-lea bit de date de transmis (în modul 9 biți de date).


### USART CONTROL AND STATUS REGISTER C

selectare mod operare, paritate, biti stop

UMSELn1:0 (Bit 7:6): USART Mode Select
Setează modul de operare după următorul tabel:

| UMSELn1 | UMSELn0 | Mode           |
| ------- | ------- | -------------- |
| 0       | 0       | USART asincron |
| 0       | 1       | USART sincron  |
| 1       | 0       | Reserved       |
| 1       | 1       | Master SPI     |
UPMn1:0 (Bit 5:4): Parity Mode
Setează modul de paritate după următorul tabel:

| UPMn1 | UPMn0 | Mode                 |
| ----- | ----- | -------------------- |
| 0     | 0     | Disabled             |
| 0     | 1     | Reserved             |
| 1     | 0     | Enabled, Even Parity |
| 1     | 1     | Enabled, Odd Parity  |

USBSn (Bit 3): Stop Bit Select
Setează numărul de biți de stop

| USBSn | Mode  |
| ----- | ----- |
| 0     | 1-bit |
| 1     | 2-bit |

UCSZn1:0 (Bit 2:1): Character Size
Folosiți împreună cu UCSZ2 pentru a seta numărul de biți de date:

| UCSZn2 | UCSZn1 | UCSZn0 | Mode     |
| ------ | ------ | ------ | -------- |
| 0      | 0      | 0      | 5-bit    |
| 0      | 0      | 1      | 6-bit    |
| 0      | 1      | 0      | 7-bit    |
| 0      | 1      | 1      | 8-bit    |
| 1      | 0      | 0      | Reserved |
| 1      | 0      | 1      | Reserved |
| 1      | 1      | 0      | Reserved |
| 1      | 1      | 1      | 9-bit    |

UCPOLn (Bit 0): Clock Polarity
Folosit doar în modul sincron pentru a defini polaritatea clock-ului:

| UCPOLn | Transmited Data Changed<br>(output of TxDn Pin) | Received Data Sampled<br>(input on RxDn Pin) |
| ------ | ----------------------------------------------- | -------------------------------------------- |
| 0      | Rising XCKn Edge                                | Falling XCKn Edge                            |
| 1      | Falling XCKn Edge                               | Rising XCKn Edge                             |


# Exemple + cod

#### Ex 1: echo



```c
#include <stdint.h>
#include <inavr.h>
#include <ioavr.h>

#define F_OSC 16000000UL
// Baud Rate
#define BAUD 9600
#define BAUD_RATE (F_OSC / 16 / BAUD - 1)

// Transmițătorul
#define TRANSMITTER (1 << TXEN3)
// Receptorul
#define RECEIVER (1 << RXEN3)

// Folosim pinul TX ca ieșire
#define TXD_OUT() DDRJ |= (1 << PJ1)
// Folosim pinul RX ca intrare
#define RXD_IN() DDRJ &= ~(1 << PJ0)

```

RXEN3 SI TXEN3 is pt a activa si transmiterea si primirea
is pt registrul B

PJ1 si PJ0 is legati la pinii rx3 si tx3

```c
/*
* Funcția configurează Baud Rate-ul pentru USART3, activează receptorul
* și transmițătorul, iar apoi setează pinul TX ca ieșire și pinul RX ca
* intrare.
*/
void usart_initialize(uint16_t baud_rate)
{
	// Se configurează Baud Rate-ul reg HIGH si LOW
	UBRR3H = (uint8_t)(baud_rate >> 8);
	UBRR3L = (uint8_t)(baud_rate & 0xFF);
	// Se pornește transmițătorul și receptorul
	UCSR3B = TRANSMITTER | RECEIVER;
	// Se setează pinul TXD ca ieșire
	TXD_OUT();
	// Se setează pinul RXD ca intrare
	RXD_IN();
}

```


```c
/*
* Funcția așteaptă până când buffer-ul de transmisie este gol (UDRE3 = 1)
* iar apoi scrie un octet în registrul UDR3 pentru a fi trimis pe linia
* serială.
*/
void usart_transmit(uint8_t data)
{
	while(!(UCSR3A & (1 << UDRE3)))
	{
	 	// Se așteaptă până când buffer-ul de                    transmisie e gol
	}
	UDR3 = data;
}

/*
* Funcția așteaptă până când este recepționat un caracter (RXC3 = 1), iar
* apoi returnează octetul citit din registrul UDR3.
*/
uint8_t usart_receive(void)
{
	while(!(UCSR3A &(1 << RXC3)))
	{
	// Se așteaptă recepționarea unui caracter
	}
	return UDR3;
}

```

