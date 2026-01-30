
> [!quote]+
> aceste exercitii  au fost create de gemini


#  ÎNTRERUPERI (External Interrupts & Pin Change)

Măsurare lățime impuls: Să se implementeze un program care măsoară durata cât un buton conectat la pinul INT0 (PD0) este apăsat. 

Măsurătoarea se va face folosind Timer1 (16 biți) pornit pe frontul descrescător și oprit pe frontul crescător al întreruperii externe. Durata va fi transmisă pe seriala USART0 sub forma 

`<DURATA APASARE: X.XXX s>.`

Contorizare evenimente concurente: Să se configureze pinii INT4 și INT5 (disponibili pe ATmega1280) pentru a detecta orice schimbare de front (Any logical change). 

Programul va număra câte întreruperi au loc pe fiecare pin într-un interval de 1 secundă. Rezultatul se transmite pe serială: `
`<INT4: XXX ev/s | INT5: XXX ev/s>.`

Monitorizare porturi complete (PCINT): Folosind întreruperile de tip "Pin Change" (PCINT0 pe Portul B), să se detecteze care pin specific (PB0...PB7) și-a schimbat starea. Se va transmite pe serială masca binară a portului în momentul întreruperii: <PORTB CHANGE: 0bXXXXXXXX>.

# COMUNICAREA SERIALĂ – UART (ATmega1280 are 4 USART-uri)

Gateway Serial (Bridge): Să se implementeze un "pod" de comunicație. Tot ce se recepționează pe USART0 trebuie retransmis pe USART1 criptat simplu (XOR cu 0xFF), iar tot ce se primește pe USART1 trebuie decriptat (XOR cu 0xFF) și transmis pe USART0.

Protocol cu confirmare: Să se implementeze un program pe USART0 care așteaptă comenzi de forma <CMD:SET_LED=ON#> sau <CMD:SET_LED=OFF#>. Dacă comanda este validă, se aprinde/stinge un LED pe PORTA și se răspunde cu <ACK: LED CHANGED>. Dacă formatul este greșit, se răspunde cu <NACK: SYNTAX ERROR>.

Multiplexare date: Se citesc date numerice (0-9) de pe USART0 și caractere (a-z) de pe USART1. Programul le va combina și le va transmite pe USART2 sub forma unui pachet: <DATA0: [cifra] | DATA1: [caracter]>.

# TIMER / COUNTER (Accent pe Timerele 16-bit: T1, T3, T4, T5)  


Generator PWM de precizie (Timer3): Implementați un program care generează un semnal dreptunghiular pe pinul OC3A (PE3) cu o frecvență fixă de 50Hz (specifică servomotoarelor). Factorul de umplere (FU) trebuie să varieze automat, sinusoidal, între 5% (1ms) și 10% (2ms) pe parcursul a 3 secunde. Valoarea curentă a impulsului în microsecunde se transmite pe serială: <Pulse: XXXX us>.

Modulare în Frecvență (Timer4): Implementați un program care generează un semnal pe pinul OC4A. Frecvența semnalului trebuie să crească liniar de la 1kHz la 10kHz în timp de 1 secundă, apoi să scadă înapoi la 1kHz în următoarea secundă (efect de sirenă). Factorul de umplere rămâne constant la 50%. Transmiteți frecvența curentă pe serială la fiecare 100ms: <FREQ: XXXXX Hz>.

Input Capture (Timer5): Folosind funcția Input Capture pe pinul ICP5 (PL1), măsurați perioada exactă a unui semnal extern (generat de un alt timer sau extern). Calculați frecvența cu o precizie de 2 zecimale și transmiteți-o pe serială: `<DETECTED FREQ: XX.XX Hz>.`

Fast PWM variabil: Să se genereze pe Timer1 un semnal de 25kHz. Factorul de umplere se modifică prin comenzi seriale primite pe USART0: U (Up +1%), D (Down -1%). Limitați FU între 10% și 90%. Afișați noua valoare doar la modificare: `<PWM SET: XX %>`.

# RESET & WATCHDOG

Watchdog în mod Întrerupere (Nu Reset): ATmega1280 permite configurarea WDT să genereze o întrerupere înainte de reset sau doar o întrerupere. Configurați WDT să genereze o întrerupere la fiecare 4 secunde. În rutina de întrerupere (ISR), incrementați o variabilă globală și transmiteți-o pe serială: <WDT WAKEUP: #XXXX>. Sistemul principal nu trebuie să se reseteze.

Simulare "Window Watchdog": Implementați un mecanism software care impune ca instrucțiunea wdt_reset() să fie apelată doar într-o fereastră de timp specifică (între 40ms și 60ms de la ultimul reset). Dacă wdt_reset() este apelat prea devreme (<40ms) sau prea târziu (>60ms), forțați un reset software (prin activarea WDT cu timeout minim). Transmiteți motivul resetului la pornire (verificați regiștrii MCUSR).

Oscilator WDT calibrat: Calculați și transmiteți pe serială frecvența estimată a oscilatorului Watchdog intern (128kHz tipic), numărând câte cicluri de ceas de sistem trec între două întreruperi de Watchdog configurat la 16ms. Format: `<WDT Actual Freq: XXX.X kHz>.`

# CODURI REDUNDANTE CICLICE – CRC
CRC Tabel Vectori Întrerupere: Să se calculeze și să se transmită pe serială CRC-ul pe 16 biți (0x1021) al zonei de memorie Flash unde se află Vector Table (adresele 0x0000 - 0x00DD pentru ATmega1280). Rezultatul: <CRC IVT: 0xXXXX>.

Validare integritate funcție critică: Definiți o funcție critică:

C
void critical_task(void) {
    // ...cod arbitrar...
    PORTA |= (1<<2);
    _delay_ms(10);
    PORTA &= ~(1<<2);
}
Calculați CRC-ul acestei funcții la runtime (folosind pointeri la funcție pentru adresele de start) și transmiteți-l. Dacă CRC-ul nu corespunde cu o valoare precalculată (hardcoded), transmiteți `<INTEGRITY ERROR>`.

CRC Stream Serial: Implementați un program care primește un șir continuu de octeți pe serială terminat cu \n. Pe măsură ce octeții vin, calculați un "Rolling CRC" (actualizați CRC-ul cu fiecare octet nou fără a stoca tot șirul). La primirea \n, afișați: `<FINAL CRC16: 0xXXXX>`.