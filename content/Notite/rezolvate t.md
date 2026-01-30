# 1 + comentariu ai

![[image.png]]

> [!question] Mod watchdog
> Varianta profului e cu reset insa e posibil sa mearga mai oke cu modul intrerupere



```c
#include <ioavr.h>
#include <inavr.h>

void setup_WD();
void disable_WD();

void setup_output_pin();
void toggle_output_pin();

__no_init uint8_t persistent_state;

int main(){
    MCUSR = 0; // reset the MCU status register
    disable_WD();
    setup_output_pin();

    if(persistent_state>1){
        persistent_state = 0;
    }

    if(persistent_state == 0){
        PORTE &=~ (1<<PE4);
    }
    else if(persistent_state == 1){
        PORTE |= (1<<PE4);
    }

    persistent_state = !persistent_state;

    

    setup_WD();
    __enable_interrupt();
    asm("WDR");

    while(1){
        ;
    }
    return 0;
}

void disable_WD(){
    WDTCSR |= ((1<<WDTCE) | (1<<WDE));
    WDTCSR &= ~(1<<WDE); // stops it
    asm("WDR");
}   

void setup_WD(){
    WDTCSR |= ((1<<WDTCE) | (1<<WDE));
    // i want system reset mode
    WDTCSR |= (1<<WDE);

}

void setup_output_pin(){
    // selected output pin is mb4cs -- PE4 -- OUTPUT
    DDRE |= (1<<PE4);
}


```

## Explicatie ai

Cum funcționează codul 

Toggle prin Reset

Pornire/Reset Microcontrolerul pornește. Prima instrucțiune este `MCUSR = 0`, care încearcă să curețe flag-urile de reset.

Starea Persistentă Variabila `persistent_state` este marcată cu `__no_init`. Asta înseamnă că, deși procesorul se resetează, valoarea variabilei **nu** este ștearsă din RAM. Ea rămâne acolo de la o execuție la alta.

Schimbarea Pinului 
    *   Dacă `persistent_state` era 0, pune pinul PE4 pe LOW.
    *   Dacă era 1, îl pune pe HIGH.
    *   Apoi inversează valoarea (`!persistent_state`) pentru următorul reset.

Se activează Watchdog-ul în modul "System Reset". Deoarece nu setezi biții de prescalare (WDP0-3), ei rămân 0, ceea ce corespunde celui mai scurt timp: **aprox. 16 ms**.

Programul intră în `while(1)`. După 16 ms, Watchdog-ul resetează procesorul.

Procesorul o ia de la capăt, vede noua valoare în `persistent_state`, schimbă pinul și se resetează iar.

Calcul frecvență:
*   Un ciclu complet (un HIGH și un LOW) durează: $16ms + 16ms = 32ms$.
*   Frecvența: $f = 1 / 0.032s = 31.25 \text{ Hz}$.
*   Este foarte aproape de cei 30 Hz ceruți (oscilatorul intern de 128kHz al WDT-ului are oricum o eroare de +/- 10%).

---

 ###### Ce trebuie corectat 
există câteva probleme critice de implementare pentru ATmega1280:

ASTA ZICE AI-UL NU STIU CAT DE ACCURATE E


 A. Problema ferestrei de 4 cicli (WDTCSR)
 
Pentru a modifica registrul `WDTCSR`, datasheet-ul impune o secvență strictă de siguranță: trebuie să scrii `WDCE` și `WDE` simultan, iar **în următorii 4 cicli de ceas** să scrii noua valoare.

În funcția ta `setup_WD`:

```c
void setup_WD(){
    WDTCSR |= ((1<<WDTCE) | (1<<WDE)); // Pasul 1: OK
    WDTCSR |= (1<<WDE);                // Pasul 2: Posibil prea lent!
}
```

Operația `|=` (OR) implică o citire, o modificare și o scriere (mai mulți cicli de ceas). Este mai sigur să scrii direct valoarea:

```c
void setup_WD(){
    WDTCSR = (1<<WDTCE) | (1<<WDE); // Deschide fereastra
    WDTCSR = (1<<WDE);               // Setează modul Reset și WDP=0 (16ms)
}
```

 B. Problema flag-ului WDRF (MCUSR)
 
Pe ATmega1280, dacă bitul `WDRF` din `MCUSR` nu este șters, nu poți dezactiva sau modifica Watchdog-ul. Resetarea lui la 0 trebuie făcută **imediat** după pornire.
```c
MCUSR &= ~(1 << WDRF); // Corect ar fi așa
```

C. Timpul de Start-up (Problema frecvenței reale)
După fiecare reset WDT, procesorul nu începe instantaneu să execute codul. Există un timp de "Start-up" (stabilit prin Fuse Bits) pentru stabilizarea cristalului de cuarț (de obicei 16k sau 64k cicli de ceas).
*   Dacă ai un cristal de 16MHz, 64k cicli înseamnă încă **4ms** de întârziere la fiecare reset.
*   Perioada ta ar deveni $(16ms + 4ms) \times 2 = 40ms \rightarrow 25 \text{ Hz}$.
*   Pentru a obține exact 30 Hz, va trebui să ajustezi biții de prescalare ai WDT sau să folosești Modul Întrerupere în loc de Reset.

Versiunea Corectată (Recomandată pentru examen)

Dacă vrei să fii sigur că profesorul îți punctează codul ca fiind robust, folosește Modul Întrerupere al Watchdog-ului. Acesta nu resetează procesorul, deci elimini eroarea dată de timpul de start-up:

```c
#include <ioavr.h>
#include <inavr.h>
#include <intrinsics.h> // Pentru __enable_interrupt()

#pragma vector = WDT_vect  // Sintaxă specifică IAR pentru ISR
__interrupt void WDT_ISR(void) {
    PORTE ^= (1 << PE4);   // Toggle pin PE4 (Mult mai rapid și precis)
}

void setup_WD() {
    __disable_interrupt();
    __watchdog_reset();
    
    MCUSR &= ~(1 << WDRF); // Pas critic pentru ATmega1280
    
    // Secvență de siguranță: WDCE și WDE
    WDTCSR = (1 << WDTCE) | (1 << WDE);
    
    // WDIE = 1 (Interrupt Mode), WDE = 0 (No Reset)
    // WDP0-3 = 0000 (16ms timeout)
    WDTCSR = (1 << WDIE); 
    
    __enable_interrupt();
}

int main() {
    DDRE |= (1 << PE4); // Configurare pin ieșire
    setup_WD();
    
    while(1) {
        // Procesorul stă liniștit aici, nu se mai resetează.
        // Întreruperea se ocupă de tot.
    }
}
```

**De ce e mai bun ăsta?**
1.  **Frecvență constantă:** Nu mai depinde de timpul de boot al procesorului.
2.  **Consum mic:** Poți pune procesorul în modul `Sleep` în `while(1)`.
3.  **Cod curat:** Nu ai nevoie de `__no_init`.

Dacă totuși **trebuie** să folosești varianta cu Reset (cerința examenului), asigură-te că scrii registrele dintr-o singură mișcare (fără `|=`) și că menționezi că frecvența este limitată de oscilatorul intern de 128kHz.

# 2

vezi si exemplu prof 

![[image 1.png]]

```c
#include <ioavr.h>
#include <inavr.h>
#include <string.h>
#include <stdio.h>

#define FOSC 16000000UL
#define N 8UL
#define STRLEN 256

#define BAUD 9600UL
#define QLEN 256

// i will use timer 3


uint8_t TXQ[QLEN];
volatile uint8_t tx_head;
volatile uint8_t tx_tail;

uint8_t RXQ[QLEN];
volatile uint8_t rx_head;
volatile uint8_t rx_tail;

void put_txbuf(uint8_t char_to_send, uint8_t isr_safe);
uint8_t get_txbuf();
void put_rxbuf(uint8_t char_to_send);
uint8_t get_rxbuf(uint8_t isr_safe);
uint8_t get_nxt_idx(uint8_t current_idx);

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe);

void setup_usart3();


#pragma vector USART3_RX_vect
__interrupt void USART3_RX_ISR(void){
    uint8_t char_received = UDR3;
    if(char_received!=0){
        put_rxbuf(char_received); 
    }
}

#pragma vector USART3_UDRE_vect
__interrupt void USART3_UDRE_ISR(void){
    uint8_t char_to_send = get_txbuf();
    if(char_to_send!=0){
        UDR3 = char_to_send;
    }
}

uint8_t string_to_send[STRLEN];

__no_init uint32_t ovfs;
__no_init uint16_t current_ticks;

void setup_WD();
void stop_WD();
void setup_timer3();

#pragma vector TIMER3_OVF_vect
__interrupt TIMER3_OVF_ISR(void){
    ovfs++;
}


int main(){
    uint8_t MCUSR_backup = MCUSR;
    uint8_t reset_by_wd = ( (MCUSR_backup & (1<<WDRF)) !=0);
    MCUSR = 0;
    stop_WD();

    setup_timer3();
    setup_usart3();
    __enable_interrupt();

    if(reset_by_wd && ( (ovfs > 0) || (current_ticks > 0)) ){
        // calculate and send freq
        uint32_t total_ticks = (ovfs*65536UL)+current_ticks;
        uint32_t ticks_per_second = FOSC/N;
        uint32_t freq_hz = ticks_per_second / total_ticks;

        uint8_t nr_khz = freq_hz / 1000;
        uint16_t nr_hz_radix = freq_hz & 1000;

        memset(string_to_send,0,STRLEN*sizeof(uint8_t));
        snprintf(string_to_send,STRLEN,"%u.%3lu KHZ\n",nr_khz,nr_hz_radix);
        uint8_t real_len = strlen(string_to_send);

        sendString(string_to_send,real_len,0);

    }

    setup_WD(); 

    TCNT3 = 0;
    ovfs = 0;
    current_ticks = 0;
    while(1){
        current_ticks = TCNT3;
    }
    return 0;
}

void setup_WD(){
    uint8_t SREG_cpy = SREG;
    __disable_interrupt();
    asm("WDR");
    WDTCSR |= ((1<<WDTCE) | (1<<WDE));
    WDTCSR = ((1<<WDE) | (1<<WDP0)); // enable system interrupt
    SREG = SREG_cpy;
}

void stop_WD(){
    uint8_t SREG_cpy = SREG;
    __disable_interrupt();
    asm("WDR");
    WDTCSR |= ((1<<WDTCE) | (1<<WDE));
    WDTCSR = 0;
    SREG = SREG_cpy;
}


void setup_timer3(){
    // timer 3 -- normal -- ovf interrupt
    TIMSK3 |= (1<<TOIE3);
    TCCR3B |= (1<<CS31); // prescaler 8 and clock select
}



// USART STUFF -----------------------------------------------------------------------------------------------------------------------------

void setup_usart3(){
    UCSR3B |= (1<<RXCIE3); // enable receive interrupt. udre interrupt will be only enabled on sending smth
    uint16_t baud_rate = (FOSC/16UL/BAUD) - 1;
    UBRR3H = (baud_rate >> 8) & (0x00FF);
    UBRR3L = (baud_rate & 0x00FF);

    UCSR3C |= ((1<<UCSZ31) | (1<<UCSZ30));
    
    UCSR3B |= ((1<<RXEN3) | (1<<TXEN3));
}

uint8_t get_nxt_idx(uint8_t current_idx){
    return (current_idx + 1) % QLEN;
}

// called by user
void put_txbuf(uint8_t char_to_send, uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }

    if(char_to_send == 0){
        if(isr_safe == 0){
            __enable_interrupt();
        }   
        return;
    }

    uint8_t next_tx_tail = get_nxt_idx(tx_tail);
    if(next_tx_tail == tx_head){
        // queue full
        if(isr_safe == 0){
            __enable_interrupt();
        }  
        return;
    }

    TXQ[tx_tail] = char_to_send;
    tx_tail = next_tx_tail;

    // enable sending intr
    UCSR3B |= (1<<UDRIE3);
    
    if(isr_safe == 0){
        __enable_interrupt();
    }
}

// called by intr
uint8_t get_txbuf(){
    if(tx_tail == tx_head){
        // q empty so the intr is stopped
        UCSR3B &= ~(1<<UDRIE3);
        return 0;
    }
    uint8_t current_char = TXQ[tx_head];
    uint8_t next_tx_head = get_nxt_idx(tx_head);
    tx_head = next_tx_head;
    return current_char;
}


// called by intr
void put_rxbuf(uint8_t char_to_send){
    if(char_to_send == 0){
        return;
    }

    uint8_t next_rx_tail = get_nxt_idx(rx_tail);
    if(next_rx_tail == rx_head){
        // queue full
        return;
    }

    RXQ[rx_tail] = char_to_send;
    rx_tail = next_rx_tail;
}


// called by user
uint8_t get_rxbuf(uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }
    
    if(rx_tail == rx_head){
        // q empty
        return 0;
    }
    uint8_t current_char = RXQ[rx_head];
    uint8_t next_rx_head = get_nxt_idx(rx_head);
    rx_head = next_rx_head;

    if(isr_safe == 0){
        __enable_interrupt();
    }

    return current_char;

}


void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe){
    if(string_to_send == NULL){
        return;
    }
    int i;
    for(i = 0;i<string_length;i++){
        put_txbuf(string_to_send[i], isr_safe);
    }
}

```

Când procesorul pornește (după un reset), prima întrebare pe care și-o pune este: **"De ce m-am resetat?"**.

- Verifică registrul MCUSR. Dacă bitul WDRF este 1, înseamnă că ultima dată procesorul a fost resetat de Watchdog.

- Dacă a fost un reset de Watchdog, ia valorile salvate în ovfs și current_ticks (care reprezintă timpul scurs în ciclul anterior) și face niște calcule.

Programul vrea să transforme timpul măsurat într-o frecvență.

- **Timer 3** este folosit ca un cronometru (stopwatch).

- Numără de câte ori Timer-ul a făcut "overflow" (a ajuns la maxim și a luat-o de la capăt) în variabila ovfs.

- Combină numărul de overflow-uri cu valoarea curentă a timer-ului (current_ticks) pentru a afla totalul de "tick-uri" de ceas scurse.

- Calculul freq_hz = ticks_per_second / total_ticks determină de câte ori pe secundă s-ar produce acel reset dacă s-ar repeta constant.

După calcul, rezultatul (ex: 1.050 KHZ) este formatat într-un text cu snprintf și trimis prin portul serial către un PC, folosind un sistem de cozi (buffere) și întreruperi pentru a nu bloca procesorul.

După ce a trimis datele, programul:

1. Configurează din nou Watchdog-ul să expire după un timp scurt (aprox. 15ms).

2. Resetează cronometrul (Timer 3) la zero.

3. Intră într-o buclă infinită while(1) unde scrie non-stop valoarea curentă a cronometrului în current_ticks.

4. **Important:** Programul NU dă "mâncare la câine" (nu apelează comanda de resetare a watchdog-ului).

5. Așteaptă moartea: Watchdog-ul expiră, procesorul primește reset, iar tot procesul o ia de la capăt (înapoi la pasul 2).

###### Potentiale probleme

Problema cea mai mare este eroarea de calcul: ai scris freq_hz & 1000 pentru a obține restul de herți, dar operatorul & este un "ȘI pe biți". Trebuia să folosești operatorul modulo % (adică freq_hz % 1000), altfel rezultatul afișat va fi complet greșit.

O altă problemă critică este lipsa atomicității. Variabila ovfs are 32 de biți, iar procesorul AVR este pe 8 biți. Când citești ovfs în main pentru calcul, procesorul face 4 citiri separate. Dacă exact în acel moment vine o întrerupere de timer care modifică ovfs, valoarea citită de tine va fi coruptă. Trebuie să dezactivezi întreruperile (cli()) înainte de a copia valorile variabilelor multi-octet și să le activezi după (sei()).

Variabilele declarate cu \__no_init (ovfs și current_ticks) reprezintă un risc la prima pornire. Când alimentezi plăcuța (Power-On), memoria RAM are valori aleatorii (gunoi). Deoarece codul tău nu le inițializează cu zero decât dacă vede un reset de watchdog, prima măsurătoare va folosi acele valori aleatorii și va afișa prostii. Ar trebui să verifici în MCUSR dacă a fost un reset de pornire (PORF) și să le pui pe zero manual atunci.

În bucla while(1), scrierea current_ticks = TCNT3 este periculoasă. Resetul de watchdog poate veni exact în momentul în care procesorul a apucat să scrie doar primul octet din cei doi ai variabilei current_ticks. Astfel, după reset, vei citi o valoare care nu a existat niciodată în realitate. Mai sigur ar fi să citești TCNT3 direct în calculul de după reset, deși registrul se resetează la zero, deci salvarea lui în buclă e singura variantă – dar trebuie făcută cu grijă.

Logica de calcul pentru frecvență pare inversată sau neadaptată la valorile WDT. Dacă watchdog-ul este setat la 15ms (cum ai pus tu prin WDP0), frecvența evenimentului este de aproximativ 66 Hz. Calculul tău nr_khz = freq_hz / 1000 va da rezultatul 0, deoarece 66 împărțit la 1000 este 0. Codul pare să se aștepte la o frecvență mult mai mare decât cea reală a unui watchdog.


În funcția put_txbuf, folosești \__enable_interrupt() la final. Aceasta este o problemă dacă funcția este apelată dintr-o altă zonă de cod unde întreruperile trebuiau să rămână dezactivate. Metoda corectă este să salvezi registrul de stare SREG la început și să îl restaurezi la final.

În setup_WD, ai activat watchdog-ul în regim de "System Reset", dar ai setat un timp de expirare foarte scurt (WDP0 înseamnă ~15ms). Având în vedere că trimiterea string-ului pe serială la 9600 baud durează destul de mult, trebuie să te asiguri că procesul de trimitere nu este întrerupt de un nou reset înainte să se termine.

În snprintf, ai folosit formatul %u.%3lu. Dacă restul împărțirii este mic (de exemplu 5), acesta va fi afișat ca "0. 5 KHZ" (cu spații goale). Formatul corect pentru frecvență ar fi %03lu, ca să afișeze "0.005 KHZ"
# 3.

![[image 2.png]]

```c
#include <ioavr.h>
#include <inavr.h>
#include <string.h>
#include <stdio.h>

// sa se calculeze si sa se trimita pe seriala crc16-ul zonei de memorie de tip program ocupata de functia:



#define FOSC 16000000UL
#define N 8UL

#define BAUD 9600UL
#define QLEN 256

uint8_t TXQ[QLEN];
volatile uint8_t tx_head;
volatile uint8_t tx_tail;

uint8_t RXQ[QLEN];
volatile uint8_t rx_head;
volatile uint8_t rx_tail;

void put_txbuf(uint8_t char_to_send, uint8_t isr_safe);
uint8_t get_txbuf();
void put_rxbuf(uint8_t char_to_send);
uint8_t get_rxbuf(uint8_t isr_safe);
uint8_t get_nxt_idx(uint8_t current_idx);

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe);

void setup_usart3();


#pragma vector USART3_RX_vect
__interrupt void USART3_RX_ISR(void){
    uint8_t char_received = UDR3;
    if(char_received!=0){
        put_rxbuf(char_received); 
    }
}

#pragma vector USART3_UDRE_vect
__interrupt void USART3_UDRE_ISR(void){
    uint8_t char_to_send = get_txbuf();
    if(char_to_send!=0){
        UDR3 = char_to_send;
    }
}



#define SLEN 256
uint8_t string_to_send[SLEN];

#define CRC16_MSBF 0x1021

__root
unsigned char sum(unsigned char x, unsigned char y){
    return (x+y);
}

__root
void sum_end(){
    asm("nop");
}


uint16_t crc16(uint16_t polinom16, uint16_t init_val_16, uint32_t adr_start, uint32_t len);

int main(){

    unsigned char const __huge * start = (unsigned char const __huge *)sum;
    unsigned char const __huge * end   = (unsigned char const __huge *)sum_end;

    uint32_t fct_sz = (uint32_t)(end-start);

    uint16_t calculated_crc16 = crc16(CRC16_MSBF, 0, (uint32_t)start, fct_sz);

    memset(string_to_send,0,SLEN*sizeof(uint8_t));
    setup_usart3();

    snprintf(string_to_send, SLEN, "<CRC16 ADR[0x%04luX - %04luX] %04X>" , (uint32_t)start, (uint32_t)end, calculated_crc16 );

    __enable_interrupt();

    sendString(string_to_send, strlen(string_to_send), 0);

    while(1){
        ;
    }
    return 0;
}

uint16_t crc16(uint16_t polinom16, uint16_t init_val_16, uint32_t addr_start, uint32_t len)
{
    uint16_t crc = init_val_16; // Inițializează CRC cu valoarea de start
    uint16_t data = 0;          // Variabilă temporară pentru octetul curent
    while (len--)
    {
    uint16_t i;
    data = *(unsigned char __huge *)addr_start;
        data <<= 8;  // Se aliniază octetul la MSB
        crc ^= data; // Se transferă și integrează datele în CRC
        for (i = 0; i < 8; ++i)
        {
            if (crc & 0x8000) // Se verifică dacă MSB este 1
                crc = (crc << 1) ^ polinom16;
            else
                crc = crc << 1;
        }
        addr_start++;
    }
    return crc;
}


// USART STUFF -----------------------------------------------------------------------------------------------------------------------------

void setup_usart3(){
    UCSR3B |= (1<<RXCIE3); // enable receive interrupt. udre interrupt will be only enabled on sending smth
    uint16_t baud_rate = (FOSC/16UL/BAUD) - 1;
    UBRR3H = (baud_rate >> 8) & (0x00FF);
    UBRR3L = (baud_rate & 0x00FF);

    UCSR3C |= ((1<<UCSZ31) | (1<<UCSZ30));
    
    UCSR3B |= ((1<<RXEN3) | (1<<TXEN3));
}

uint8_t get_nxt_idx(uint8_t current_idx){
    return (current_idx + 1) % QLEN;
}

// called by user
void put_txbuf(uint8_t char_to_send, uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }

    if(char_to_send == 0){
        if(isr_safe == 0){
            __enable_interrupt();
        }   
        return;
    }

    uint8_t next_tx_tail = get_nxt_idx(tx_tail);
    if(next_tx_tail == tx_head){
        // queue full
        if(isr_safe == 0){
            __enable_interrupt();
        }  
        return;
    }

    TXQ[tx_tail] = char_to_send;
    tx_tail = next_tx_tail;

    // enable sending intr
    UCSR3B |= (1<<UDRIE3);
    
    if(isr_safe == 0){
        __enable_interrupt();
    }
}

// called by intr
uint8_t get_txbuf(){
    if(tx_tail == tx_head){
        // q empty so the intr is stopped
        UCSR3B &= ~(1<<UDRIE3);
        return 0;
    }
    uint8_t current_char = TXQ[tx_head];
    uint8_t next_tx_head = get_nxt_idx(tx_head);
    tx_head = next_tx_head;
    return current_char;
}

// called by intr
void put_rxbuf(uint8_t char_to_send){
    if(char_to_send == 0){
        return;
    }

    uint8_t next_rx_tail = get_nxt_idx(rx_tail);
    if(next_rx_tail == rx_head){
        // queue full
        return;
    }

    RXQ[rx_tail] = char_to_send;
    rx_tail = next_rx_tail;
}

// called by user
uint8_t get_rxbuf(uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }
    
    if(rx_tail == rx_head){
        // q empty
        return 0;
    }
    uint8_t current_char = RXQ[rx_head];
    uint8_t next_rx_head = get_nxt_idx(rx_head);
    rx_head = next_rx_head;

    if(isr_safe == 0){
        __enable_interrupt();
    }

    return current_char;

}

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe){
    if(string_to_send == NULL){
        return;
    }
    int i;
    for(i = 0;i<string_length;i++){
        put_txbuf(string_to_send[i], isr_safe);
    }
}
```


- **__root**: Această directivă IAR forțează compilatorul să păstreze funcția în cod, chiar dacă nu este apelată explicit (prevenind optimizarea).

- **Aflarea adresei**: În main, codul ia adresa de început a funcției sum și adresa funcției imediat următoare sum_end.

- **Calculul dimensiunii**: fct_sz = end - start. Practic, se măsoară câți octeți ocupă codul mașină al funcției sum în Flash.
    
- **__huge**: Este un tip de pointer care permite accesarea întregii memorii Flash (peste limita de 64KB), necesar pe microcontrolerele cu memorie mare.


Funcția crc16 implementează algoritmul standard **CRC-16-CCITT** (polinom 0x1021).

- **Logica**: Pentru fiecare octet din funcția sum, se face un XOR cu registrul CRC, apoi se verifică bit cu bit (8 iterații) dacă trebuie aplicat polinomul prin operații de shiftare și XOR.
    
- **Scop**: CRC-ul este folosit pentru a verifica integritatea datelor. Dacă un singur bit din codul funcției sum s-ar schimba (eroare de memorie), CRC-ul ar fi complet diferit.


1. **Inițializare adrese**: Se obțin pointerii către funcția țintă.
    
2. **Calcul mărime**: Se află câți octeți trebuie citiți.
    
3. **Calcul CRC**: Se apelează crc16, care citește direct din memoria Flash (program).
    
4. **Configurare USART3**: Se setează baud rate-ul la 9600 și se activează întreruperile.
    
5. **Formatare String**: Se folosește snprintf pentru a crea un mesaj lizibil care conține adresele de start/end și valoarea CRC calculată.
    
6. **Trimitere**: sendString pune mesajul în buffer-ul de transmisie, iar de aici întreruperile se ocupă de trimiterea efectivă octet cu octet către PC.

###### ce poate fi gresit

- Compilatorul și Linker-ul nu garantează că funcțiile sunt puse în memorie în ordinea în care le scrii tu în fișierul .c. Este foarte posibil ca sum_end să fie plasată de linker înaintea funcției sum sau la o distanță mult mai mare, caz în care fct_sz va fi o valoare imensă sau negativă (eroare de calcul).
    
- **Soluție:** Pentru a fi sigur, ar trebui să verifici fișierul .map generat de linker sau să folosești directive de secțiuni (pragma section) pentru a forța gruparea lor.


 Citirea din Memoria Program (Flash vs RAM)

Pe AVR, memoria Flash (unde stă codul) și memoria RAM au spații de adresare diferite.

code C

```
data = *(unsigned char __huge *)addr_start;
```

**De ce este riscant:**

- Deși în IAR pointerii __huge pot accesa Flash-ul, trebuie să te asiguri că acea adresă este interpretată corect ca fiind în spațiul de cod (Code Space). Dacă compilatorul crede că e un pointer către RAM, va citi date de la adresa respectivă din RAM, nu din Flash, rezultând un CRC greșit.

- **Soluție:** Folosește tipul de date __code pentru pointeri atunci când citești instrucțiuni: unsigned char const __code *.


- %04luX este o combinație greșită. Dacă vrei long unsigned hex, se folosește %08lX (pentru adrese pe 32 biți).
    
- snprintf pe AVR consumă foarte multă memorie Flash și RAM. Dacă microcontrolerul are puțină memorie, s-ar putea să facă stack overflow.
# 4

![[image 3.png]]

```c
#include <ioavr.h>
#include <inavr.h>
#include <stdio.h>
#include <string.h>

#define FREQ 5000UL
#define FTRIGGER 1000UL // every msec

#define FOSC 16000000UL
#define N 8UL

#define BAUD 9600UL
#define QLEN 256

uint8_t TXQ[QLEN];
volatile uint8_t tx_head;
volatile uint8_t tx_tail;

uint8_t RXQ[QLEN];
volatile uint8_t rx_head;
volatile uint8_t rx_tail;

void put_txbuf(uint8_t char_to_send, uint8_t isr_safe);
uint8_t get_txbuf();
void put_rxbuf(uint8_t char_to_send);
uint8_t get_rxbuf(uint8_t isr_safe);
uint8_t get_nxt_idx(uint8_t current_idx);

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe);

void setup_usart3();


volatile uint8_t DC = 5;
volatile uint8_t inc_decn = 1;
uint8_t dc_step = 2;

volatile uint16_t msecs;
// semnal F = 5khz +-2%
// FU +=2% la fiecare 0.5 sec
// de la 5% la 90% si inapoi
// <FU = XX%>

// timer3 for freq gen
void setup_timer3_freq(); 
void start_timer3();
void stop_timer3();
void set_oc3a_with_dc(uint8_t dc);

// timer4 will trigger ovf every 0.5sec
void setup_timer4_dc_update();
void start_timer4();
void stop_timer4();

void setup_output_pin_pe3();
void inc_dc();



#pragma vector USART3_RX_vect
__interrupt void USART3_RX_ISR(void){
    uint8_t char_received = UDR3;
    if(char_received!=0){
        put_rxbuf(char_received); 
    }
}

#pragma vector USART3_UDRE_vect
__interrupt void USART3_UDRE_ISR(void){
    uint8_t char_to_send = get_txbuf();
    if(char_to_send!=0){
        UDR3 = char_to_send;
    }
}




#pragma vector TIMER4_OVF_vect
__interrupt TIMER4_OVF_ISR(void){
    msecs++;
    if(msecs == 500){
        // au trecut 0.5 sec
        msecs = 0;
        // stop_timer3();
        inc_dc();
        set_oc3a_with_dc(DC); // ar trebui sa fie safe ptc sunt fixate locurile in care chiar se foloseste noul OCR
        // start_timer3();
        uint8_t string_to_send[64];
        memset(string_to_send,0,64*sizeof(uint8_t));
        snprintf(string_to_send, 64, "<FU=%2u%%>", DC);
        sendString(string_to_send, strlen(string_to_send), 1);
    }
}

int main(){
    
    setup_timer3_freq();
    setup_timer4_dc_update();
    setup_usart3();
    setup_output_pin_pe3();


    __enable_interrupt();
    start_timer3();
    start_timer4();
    while(1){
        ;
    }
    return 0;
}

void setup_output_pin_pe3(){
    DDRE |= (1<<PE3);
}

void inc_dc(){
    if(inc_decn == 1){
        if(DC+dc_step <=90){
            DC+=dc_step;
        }
        else{
            DC-=dc_step;
            inc_decn = 0;
        }
    }
    else if(inc_decn == 0){
        if(DC-dc_step >= 5){
            DC-=dc_step;
        }
        else{
            DC+=dc_step;
            inc_decn = 1;
        }
    }
}

// TIMER STUFF -------------------------------------------------------------------------------------------------------------------

void setup_timer3_freq(){
    TCNT3 = 0;
    // pwm with ICR3 as top and OCR3A as compare -- output on mb4rst -- J3 -- 26
    // mode 14
    TCCR3B |= ((1<<WGM33) | (1<<WGM32));
    TCCR3A |= (1<<WGM31);

    // set ocr compare output mode on oc3a
    // clear on compare match, set at bottom
    TCCR3A |= (1<<COM3A1); 

    // setup TOP value:
    uint32_t newICR3 = (FOSC/N)/FREQ - 1UL;
    ICR3 = (uint16_t)newICR3;

    set_oc3a_with_dc(DC);
}

void start_timer3(){
    TCCR3B &= ~((1<<CS32) | (1<<CS31) | (1<<CS30));
    TCCR3B |= (1<<CS31); // prescaler 8
}

void stop_timer3(){
    TCCR3B &= ~((1<<CS32) | (1<<CS31) | (1<<CS30));
}

void set_oc3a_with_dc(uint8_t dc){
    // ICR*dc / 100

    uint32_t new_OCR3A = ((ICR3 + 1UL)*(uint32_t)dc) / 100;
    OCR3A = (uint16_t)new_OCR3A;
}




void setup_timer4_dc_update(){
    TCNT4 = 0;
    // timer 4 CTC with top = ICR4 //  mode 12
    // ovf interrupt --> increase DC4
    TCCR4B |= ( (1<<WGM43) | (1<<WGM42));


    uint32_t new_ICR4 = (FOSC/N)/FTRIGGER -1; // ideal 1000hz
    // trigger ovf si increase msecs

    TIMSK4 |= (1<<TOIE4);
}
void start_timer4(){
    TCCR4B &= ~((1<<CS42) | (1<<CS41) | (1<<CS40));
    TCCR4B |= (1<<CS41); // prescaler 8
}
void stop_timer4(){
    TCCR4B &= ~((1<<CS42) | (1<<CS41) | (1<<CS40));
}


// USART STUFF -----------------------------------------------------------------------------------------------------------------------------

void setup_usart3(){
    UCSR3B |= (1<<RXCIE3); // enable receive interrupt. udre interrupt will be only enabled on sending smth
    uint16_t baud_rate = (FOSC/16UL/BAUD) - 1;
    UBRR3H = (baud_rate >> 8) & (0x00FF);
    UBRR3L = (baud_rate & 0x00FF);

    UCSR3C |= ((1<<UCSZ31) | (1<<UCSZ30));
    
    UCSR3B |= ((1<<RXEN3) | (1<<TXEN3));
}

uint8_t get_nxt_idx(uint8_t current_idx){
    return (current_idx + 1) % QLEN;
}

// called by user
void put_txbuf(uint8_t char_to_send, uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }

    if(char_to_send == 0){
        if(isr_safe == 0){
            __enable_interrupt();
        }   
        return;
    }

    uint8_t next_tx_tail = get_nxt_idx(tx_tail);
    if(next_tx_tail == tx_head){
        // queue full
        if(isr_safe == 0){
            __enable_interrupt();
        }  
        return;
    }

    TXQ[tx_tail] = char_to_send;
    tx_tail = next_tx_tail;

    // enable sending intr
    UCSR3B |= (1<<UDRIE3);
    
    if(isr_safe == 0){
        __enable_interrupt();
    }
}

// called by intr
uint8_t get_txbuf(){
    if(tx_tail == tx_head){
        // q empty so the intr is stopped
        UCSR3B &= ~(1<<UDRIE3);
        return 0;
    }
    uint8_t current_char = TXQ[tx_head];
    uint8_t next_tx_head = get_nxt_idx(tx_head);
    tx_head = next_tx_head;
    return current_char;
}

// called by intr
void put_rxbuf(uint8_t char_to_send){
    if(char_to_send == 0){
        return;
    }

    uint8_t next_rx_tail = get_nxt_idx(rx_tail);
    if(next_rx_tail == rx_head){
        // queue full
        return;
    }

    RXQ[rx_tail] = char_to_send;
    rx_tail = next_rx_tail;
}

// called by user
uint8_t get_rxbuf(uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }
    
    if(rx_tail == rx_head){
        // q empty
        return 0;
    }
    uint8_t current_char = RXQ[rx_head];
    uint8_t next_rx_head = get_nxt_idx(rx_head);
    rx_head = next_rx_head;

    if(isr_safe == 0){
        __enable_interrupt();
    }

    return current_char;

}

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe){
    if(string_to_send == NULL){
        return;
    }
    int i;
    for(i = 0;i<string_length;i++){
        put_txbuf(string_to_send[i], isr_safe);
    }
}

```