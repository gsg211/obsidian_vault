

| Tip intreruperere | Ce face                              | Exemplu    |
| ----------------- | ------------------------------------ | ---------- |
| Interne           | pt chestii din interiorului chipului | TIMER      |
| Externe           | pt pini                              | pin change |

### Tabela vectorilor de întrerupere (TVI)

listă de adrese la începutul memoriei Flash. Fiecare tip de întrerupere are un "loc" rezervat (un vector).
cât numărul vectorului este mai mic (adresa mai apropiată de $000), cu atât prioritatea este mai mare. **RESET** are prioritatea maximă, urmat de **INT0**.


![[Pasted image 20260107152353.png]]


# Registri

### SREG = Registrul de stare 

=> contine bitul pt enable global

### - EICRA = External Interrupt Control Register A: 

Controlează **modul de declanșare** pentru întreruperile externe (de exemplu, INT0). Folosește biții ISC01 și ISC00.


| ISCX1 | ISCX0 | Descriere                                                            |
| ----- | ----- | -------------------------------------------------------------------- |
| 0     | 0     | Nivelul 0 logic pe INTx generează o cerere de întrerupere.           |
| 0     | 1     | Orice schimbare logică pe INTx generează o cerere de întrerupere     |
| 1     | 0     | Pe frontul negativ al lui INTx se generează o cerere de întrerupere  |
| 1     | 1     | Pe frontul pozitiv al lui INTx se generează o cerere de întrerupere. |

pt astea vezi dadasheet


### EIMSK = External Interrupt Mask Register


Bit 0 -> Int 0

bitul 4 -Bitul 5 – PCIE0: activat la oricare din pinii PCINT 7:0 (din registrul PCMSK0)
PCINT0

Bitul 5 – PCIE1: activat la oricare din pinii PCINT 15:8 (din registrul PCMSK1)
PCINT1

• Bitul 6 – PCIE2: activat la oricare din pinii PCINT 23:16 (din registrul PCMSK2)
PCINT2

• Bitul 7 – PCIE3: activat la oricare din pinii  PCINT 30:24(din registrul PCMSK3)
PCINT3
### EIFR = External int FLAG register

=contine flaguri (pune pe 0 daca s-a realizat o intrerupere)

<font color="#c0504d">SE RESETEAZA CU 1</font>

# PCINTx VS INTx

intreruperile externe INTx și întreruperile la schimbare de pin PCINTx


| Tip Int | cate      | Activare           | Obs                                                                           |
| ------- | --------- | ------------------ | ----------------------------------------------------------------------------- |
| Intx    | 0-7       | Configurabila      | Control precis al detectiei                                                   |
| Pcintx  | mai multe | la orice schimbare | monitorizarea simultană a mai multor pini fără a consuma resurse suplimentare |

Prin urmare, alegerea între INTx și PCINTx depinde de cerințele aplicației, nivelul de precizie necesar în
detecția evenimentelor și disponibilitatea pinilor corespunzători.

# Activari Intreruperi

=> PROCEDURA BOTTOM UP

### 1. Setam registrul de control (ex Eicr A)
#### 2. Activam intreruperea specifica (reg EIMSK)
#### 3. Activarea Globala SREG (\_\_enable_interrupt())


# Exemplu general

```c
#include <ioavr.h>
#include <inavr.h>
#include <intrinsics.h>
int main( void )
{
	// Configurarea întreruperii externe INT0 la frontul crescător
	EICRA |= (1<< ISC01) | (1 << ISC00);
	
	// Activarea întreruperii externe INT0
	EIMSK |= (1<< INT0);
	
	// Activarea întreruperilor globale
	__enable_interrupt();
	while(1)
	{
	/*Buclă infinită pentru blocarea procesorului până la        apariția unei întreruperi*/
	}
}

```

# Exemplu intr hardware (timer)

```c
#include <ioavr.h>
#include <intrinsics.h>

// Frecvența procesorului
#ifndef F_CPU
#define F_CPU 16000000UL
#endif

// Led ON / OFF / Init
#define LED_ON() (PORTD |= (1 << 7))
#define LED_OFF() (PORTD &= ~(1 << 7))
#define LED_INIT() (DDRD |= (1 << 7))

// Puls pe semnal ON / OFF / Init
#define REF_ON() (PORTC |= (1 << 0))
#define REF_OFF() (PORTC &= ~(1 << 0))
#define REF_INIT() (DDRC |= (1 << 0))

// ISR pentru Timer0 Overflow
#pragma vector = TIMER0_OVF_vect
/*
Funcția generează un puls de durată fixă la fiecare overflow al Timer0, folosit pentru debugging sau sincronizare.
*/
__interrupt void Timer0_Overflow_ISR(void) {
REF_ON(); // Puls pe semnalul de referință (pentru osciloscop)
LED_ON(); // Aprindere LED

__delay_cycles(10000); // Întârziere ~10 μs (la 16 MHz)

LED_OFF(); // Stingere LED
REF_OFF(); // Final semnal trigger
}

void main(void) {

	LED_INIT(); // Configurare pin LED
	REF_INIT(); // Configurare pin referința
	TCCR0A = 0x00;
	
	// Timer0: normal mode, prescaler 64
	TCCR0B = (1 << CS01) | (1 << CS00);
	TIMSK0 = (1 << TOIE0); // Activare overflow interrupt
	__enable_interrupt(); // Activare întreruperi globale
	while (1) {
	
	}
}
```

ref:  Un "semnal de referință" folosit de obicei pentru a monitoriza execuția pe un osciloscop.

# Exemplu intr software


```c

// Includes
#include <ioavr.h>
#include <intrinsics.h>
/*
* Funcția configurează întreruperea externă INT0 pe frontul pozitiv.
* Aceasta setează toți pinii PORTD ca ieșire și aprinde inițial toate
* LED-urile, după care activează întreruperile globale.
*/
void init_interrupt_INT0(void) {
// Se setează toți pinii PORTD ca ieșire (LED-uri, test)
DDRD = 0xFF;
// Inițial, toate LED-urile sunt aprinse (logica inversă)
PORTD = 0xFF;
// Se configurează INT0 pe front pozitiv
EICRA |= (1 << ISC01) | (1 << ISC00);
// Se activează întreruperea INT0
EIMSK |= (1 << INT0);
// Se activează întreruperile globale
__enable_interrupt();
}

int main(void)
{
	// Se configurează PORTD ca ieșire și inițializare LED-uri
	DDRD = 0xFF;
	PORTD = 0xFF;
	
	// Se configurează INT0 pe front pozitiv și se activează întreruperea
	EICRA |= (1 << ISC01) | (1 << ISC00);
	EIMSK |= (1 << INT0);
	__enable_interrupt();
	
	// Buclă infinită pentru generarea unei întreruperi software
	while (1)
	{
		PORTD = ~PORTD;
	}
}
// ISR pentru INT0
#pragma vector=INT0_vect
/*
* Rutina de întrerupere asociată cu INT0. La declanșarea întreruperii,
* variabila statică 'test' este incrementată. Această variabilă poate fi
* utilizată pentru test sau debug.
*/
__interrupt void INT0_ISR(void) {
	static unsigned char test = 0;
	test += 1;
}

```

atunci când execuți PORTD = ~PORTD, bitul 0 (adică pinul **PD0**) trece din 0 în 1 și din 1 în 0 foarte rapid.

1. Când **PD0** trece de la **0 la 1** (front crescător / rising edge)...
2. Hardware-ul de întreruperi vede această schimbare pe pinul **INT0**.
3. Deoarece ai configurat EICRA pentru front pozitiv (ISC01 și ISC00 pe 1), microcontrolerul crede că a primit un semnal extern.
4. Procesorul oprește bucla while, sare în funcția INT0_ISR, incrementează variabila test, apoi revine în whilE

 Rezumat: Este o "Întrerupere Software"
Acest cod nu așteaptă un semnal de la un buton de afară. El **se întrerupe singur**.
- **Pinul PD0** este "legat" de **INT0** prin construcția chip-ului.
- Schimbarea valorii lui **PD0** prin cod (ca ieșire) păcălește modulul de întreruperi să creadă că s-a întâmplat un eveniment extern


# Exemplu Debounce


Acest cod implementează un algoritm de debounce software pentru a filtra zgomotul electric de la o intrare
mecanică (buton T4 / PE6), asigurând o singură tranziție logică per apăsare. Metoda se bazează pe polling
temporizat în bucla principală, utilizând o bază de timp precisă generată de Timer0. Acesta este configurat în
modul CTC pentru a declanșa o întrerupere la fiecare 1 ms, incrementând un contor global (system_millis).
La detectarea unei tranziții pe pinul de intrare, programul înregistrează momentul și intră într-o perioadă de
așteptare de 50 ms. O acțiune (comutarea LED-ului A) este validată și executată doar dacă starea pinului rămâne
stabilă pe toată durata acestui interval, eliminând astfel citirile false cauzate de contactele mecanice oscilante.
LED-ul B oferă feedback vizual, fiind activ doar pe durata intervalului de debounce.


#### Definitii 

```c
// Includes
#include <ioavr.h>
#include <intrinsics.h>
#include <stdint.h>

// Buton T4
#define BUTTON_PIN_REG PINE
#define BUTTON_DDR DDRE
#define BUTTON_PORT PORTE
#define BUTTON_PIN 6

// LED A pe PA5
#define LED_A_DDR DDRA
#define LED_A_PORT PORTA
#define LED_A_PIN 5
// LED B pe PA6
#define LED_B_DDR DDRA
#define LED_B_PORT PORTA
#define LED_B_PIN 6

// Variabila pentru stocarea timpului scurs, similar cu millis()
volatile unsigned long system_millis = 0;
// Variabile pentru starea debounce
// Starea anterioară a butonului (1 = neapăsat)
uint8_t last_button_state = 1;
// Starea stabilă a butonului
uint8_t debounced_button_state = 1;
// Timpul de la ultimul debounce
unsigned long last_debounce_time = 0;
// Timpul de așteptare debounce (în ms)
const unsigned int debounce_delay = 50;

```

#### functii

```c
// ISR pentru TIMER0 (ceasul sistemului)
#pragma vector = TIMER0_COMPA_vect
__interrupt void Millis_ISR(void) {
system_millis++;
}


// Funcția de inițializare timer
void init_millis_timer(void) {
/*
* Configurare Timer0 mod CTC pentru a genera o întrerupere la 1 ms
* F_CPU = 16 MHz. Prescaler = 64. F_timer = 16 MHz / 64 = 250 kHz.
* Pentru 1 ms (1 kHz), OCR0A = (250 kHz / 1 kHz) - 1 = 249.
*/
TCCR0A |= (1 << WGM01); // Mod CTC
TCCR0B |= (1 << CS01) | (1 << CS00); // Prescaler 64
OCR0A = 249;
TIMSK0 |= (1 << OCIE0A); // Activează întreruperea pe Compare Match A
}

/*
* Funcția configurează butonul (PE6) ca intrare cu pull-up și LED-urile
* (PA5, PA6) ca ieșiri, pornind cu ambele stinse.
*/
void init_gpio(void) {
	// Configurare Buton (PE6) ca intrare cu pull-up
	BUTTON_DDR &= ~(1 << BUTTON_PIN);
	BUTTON_PORT |= (1 << BUTTON_PIN);
	// Configurare LED-uri (PA5, PA6) ca ieșiri
	LED_A_DDR |= (1 << LED_A_PIN);
	LED_B_DDR |= (1 << LED_B_PIN);
	// Se opresc LED-urile inițial
	LED_A_PORT &= ~(1 << LED_A_PIN);
LED_B_PORT &= ~(1 << LED_B_PIN);
}

```

#### Main

poza pt ca sec de cod e prea nested sa se vada bine

![[Pasted image 20260107161439.png]]

# schimbare tabela 

Acest text descrie un mecanism avansat de gestionare a microcontrolerului, numit **Relocarea Tabelei de Vectori de Întrerupere (IVT)**.

În esență, este ca și cum ai avea un microcontroler cu "două personalități" diferite, fiecare având propriul set de instrucțiuni pentru aceleași butoane.

### 1. Explicația tehnică: Cum funcționează?

În mod normal, când apeși un buton (ex: INT6), procesorul se uită întotdeauna la o adresă fixă (ex: 0x001C) pentru a ști ce să facă. Relocarea tabelei schimbă acest comportament:

- **Bitul IVSEL (Interrupt Vector Select):** Este "comutatorul".
    
    - Când **IVSEL = 0**, tabela este la adresa **0x0000** (Mod Aplicație).
        
    - Când **IVSEL = 1**, tabela se mută la adresa **0x1E000** (Mod Bootloader).
        
- **Mecanismul de siguranță (IVCE):** Nu poți schimba tabela din greșeală. Trebuie mai întâi să "ceri voie" setând bitul IVCE, apoi, în următoarele 4 cicluri de ceas, să schimbi IVSEL.
    
- **Fișierul de asamblare (.s90):** Acesta este esențial. El "desenează" manual a doua tabelă la adresa de memorie superioară. Spune procesorului: "Dacă ești în modul Bootloader și se apasă INT6, nu mai sări la funcția de LED A, ci sari la funcția de LED B".
    
- **PCINT0 (Puntea de legătură):** Această întrerupere este pusă în **ambele** tabele. De ce? Pentru că ea este cea care face comutarea. Dacă ești în Bootloader și vrei să te întorci în Aplicație, procesorul are nevoie să găsească codul de "switch" și în tabela de Bootloader.
    

---

### 2. De ce am vrea să facem asta? (Utilitatea practică)

Pare complicat, deci de ce nu am folosi un simplu if (mod == bootloader) { ... } în interiorul unei singure funcții? Iată motivele principale:

#### A. Independența Bootloader-ului (Update de Firmware)

Cea mai importantă utilizare este **actualizarea codului**.

- **Aplicația** este codul principal (ex: softul unui ceas inteligent).
    
- **Bootloader-ul** este codul care știe să primească o versiune nouă de soft prin USB/Bluetooth și să o scrie în memorie.
    
- Dacă tabela de întreruperi ar fi doar la adresa 0x0000 (în zona de Aplicație) și tu tocmai ștergi aplicația ca să scrii una nouă, microcontrolerul s-ar prăbuși (crash) dacă apare o întrerupere în acel timp. Mutând tabela în zona de Bootloader, acest cod devine **autonom** și sigur.
    

#### B. Izolarea și Siguranța (Security)

Codul de Bootloader este critic. Prin relocarea tabelei, izolezi complet comportamentul hardware al bootloader-ului de cel al aplicației. Chiar dacă aplicația are un bug grav și se blochează, secțiunea de Bootloader rămâne intactă și poate fi accesată pentru a "repara" dispozitivul.

#### C. Economie de resurse și viteză

În loc să verifici de fiecare dată într-un if în ce mod ești (ceea ce consumă timp de procesare la fiecare întrerupere), procesorul "sare" direct la instrucțiunea corectă pentru contextul actual. Este o metodă hardware de a schimba contextul de execuție.

#### D. Personalizarea aceluiași buton

Așa cum arată exemplul tău:

- În **Mod Aplicație**, butonul de acțiune aprinde LED-ul A (funcția normală a aparatului).
    
- În **Mod Bootloader**, același buton fizic aprinde LED-ul B (poate fi o funcție de "Confirmare Update" sau "Ștergere Memorie").  
    Hardware-ul este același, dar creierul (tabela de vectori) se schimbă complet.
    

### Rezumat cod:

1. **PCINT0_vect**: Schimbă starea bitului IVSEL.
    
2. **MCUCR |= (1 << IVCE)**: Descuie posibilitatea de schimbare.
    
3. **MCUCR = (1 << IVSEL)**: Mută tabela la adresa de bootloader.
    
4. Din acest moment, deși apeși același buton fizic (INT6), procesorul va executa Bootloader_Action_ISR în loc de Application_Action_ISR.
