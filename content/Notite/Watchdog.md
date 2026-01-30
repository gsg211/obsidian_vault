> [!example] definitie
> Watchdog-ul este un timer care contorizează ciclurile semnalului de ceas pe un oscilator on-chip separat, ce are frecvența de 128 kHz.

lucreza in dom dif de frecv fata de CPU

Pentru a configura WDT-ul sunt necesare 2-3 perioade de clock ale WDT-ului

Setările sunt scrise în registrele de control ale WDT-ului, acestea fiind efective la următorul front pozitiv al clock-ului watchdog-ului, adică după 2-3 ms după ce setările au fost scrise în registru

De aici rezultă că perioada iniţială de time-out este cu 3 ms mai lungă. Dacă perioada de time-out este de 8 ms, perioada actuală este între 10 și 11 ms

![[Pasted image 20260130140947.png]]

 Intervalul dintre două instrucţiuni de WDT reset poate fi de 4.9 ms sau mai mic: 
 
 T = 8 ms – 1 ms incertitudine - 30% precizia oscilatorului

In modul fereastră, Watchdog poate fi resetat numai într-un interval de timp specific (fereastra). 

Resetarea în afara acestui interval, prea devreme sau prea târziu, va cauza resetarea procesorului. Acest mod oferă un control mai strict asupra momentului în care este făcut resetul pentru a detecta erori mai complexe.

## Surse resetare
Atmega1280 are 5 surse de resetare: 

•Resetarea la pornire(Power-on Reset): microcontrolerul este resetat, atunci când tensiunea de alimentare crește de la zero și se află sub pragul de resetare la pornire; procesorul începe execuția abia după ce VCC este stabilă. 

• Resetarea externă(External Reset): atunci când pinul RESET este menținut la nivel logic 0 pentru o durată mai mare decât perioada minimă necesară pentru semnalul de reset. 

• Resetarea prin Watchdog(Watchdog Reset): dacă temporizatorul watchdog nu este resetat la timp de către program, el generează un reset automat pentru a evita blocarea sistemului. 

• Resetarea de tip brown-out(Brown-out Reset): fenomenul de brown-out constă în scăderea valorii tensiunii într-un circuit electric sub un anumit prag, numit prag de brown-out; microcontrolerul este resetat când valoarea tensiunii de alimentare VCC este inferioară valorii pragului de resetare brown-out (VBOT) și detectorul fenomenului de brown-out este activat. 

• Resetarea de tip JTAG AVR: microcontrolerul e resetat când registrul de Reset are valoarea 1 logic

# Moduri functionare

#### Interrupt mode

Watchdog-ul produce o întrerupere atunci când timpul setat expiră, aceasta fiind folosită pentru a “trezi” anumite dispositive din modul sleep.

Un exemplu de utilizare a acestui mod este de a limita timpul maxim al unei anumite operații, producându-se o întrerupere atunci când acesta este atins. Astfel, operația nu rulează mai mult timp decât este așteptat.

#### System reset mode

Watchdog-ul produce o resetare atunci când timpul expiră. 

Acesta este utilizat pentru a preveni blocarea sistemului în cazul în care se execută cod interminabil

#### Interrupt and system reset

Interrupt and System Reset Mode combină celelalte 2 moduri de funcționare. În primul rând se produce întreruperea, apoi resetarea sistemului.


Dacă bitul WDE este setat, watchdog-ul se află în modul acesta. 

Primul time-out al numărătorului va seta WDIF (interrupt flag) , iar executarea vectorului de întreruperi va reseta biții WDIE și WDIF cu ajutorul hardware-ului, watchdog-ul trecând în modul de resetare. 

> [!warning]
> Pentru a-l menține în modul de întreruperi, bitul WDIE trebuie setat după fiecare întrerupere

## autodiagnosticare

[[SOC FINAL 2025.pdf#page=177&selection=74,0,78,13|SOC FINAL 2025, page 177]]



# Registri

#### [[SOC FINAL 2025.pdf#page=179&selection=322,0,322,5|MCUSR]] MCU STATUS REGISTER

contine flaguri

 Brown-out Reset Flag 
 
 Fenomenul de Brown-out constă în scăderea valorii tensiunii sub un anumit prag într-un circuit electronic. Acest bit este setat în cazul unui Brown-out Reset și resetat în cazul unui Power-on Reset dacă este scrisă valoarea 0 în dreptul său.

 Bit 3 – WDRF: Watchdog Reset Flag Acest bit este setat în cazul unui Watchdog Reset și resetat în cazul unui Power-on Reset dacă este scrisă valoarea 0 în dreptul său. 


#### [[SOC FINAL 2025.pdf#page=180&selection=143,0,147,32|WDTCSR - WATCHDOG TIMER CONTROL REGISTER]]

Bit 5, 2:0 – WDP 3:0: Watchdog Timer Prescaler


Bit 3 – WDE : Watchdog System Reset Enable 

Bitul WDE este suprascris de bitul WDRF în registrul MCUSR. Prin urmare, WDE este setat întotdeauna când WDRF este setat. Pentru a reseta WDE, mai întâi trebuie resetat WDRF. 

Acest lucru asigură resetări multiple în condițiile blocării sau nefuncționării unui program și o pornire sigură după acest lucru.

Bit 6 – WDIE: Watchdog Interrupt Enable
![[Pasted image 20260130152441.png]]

![[Pasted image 20260130152411.png]]

# Calcularea Frecventei 
#important


##### Cerință: 

Să se creeze o aplicație care să calculeze frecvența efectivă a Watchdog Timer-ului (WDT) folosind unul dintre timerele interne ale microcontrolerului. 

La activarea WDT-ului, se va porni un <u>timer hardware</u>, iar după resetarea automată generată de acesta, se vor folosi valorile stocate ale timer-ului pentru a determina perioada reală de time-out. 

Frecvența va fi calculată pe baza acestei perioade și transmisă prin interfața serială, fără a utiliza tipul de date double.

---
##### Sugestii: 

Sugestii: Se vor folosi variabile declarate cu atributul \__no_init pentru a păstra valorile timer-ului și numărul de overflow-uri chiar și după resetarea microcontrolerului. 

Timer-ul va fi configurat în mod normal, cu un prescaler potrivit și întrerupere la overflow pentru a număra depășirile. 

La prima execuție, aplicația va porni WDT-ul și timer-ul. După resetarea cauzată de WDT, valorile salvate vor fi folosite pentru a calcula perioada efectivă a WDT-ului conform relației T = 1 / f. 

Calculul se va face în unități convenabile (milisecunde, nanosecunde, kHz), iar rezultatul va fi transmis pe serială folosind funcții dedicate. După transmiterea rezultatului, timer-ul și variabilele vor fi resetate, iar ciclul de măsurare se poate repeta

![[Pasted image 20260130153902.png]]


###### Cod prof

```c
#include <iom1280.h> 
#include <inavr.h> #include <stdint.h> 
#include "mylib.h"

__no_init uint16_t Timer1_currentValue;
__no_init uint8_t Timer1_numberOverflows;

// Rutina de întrerupere pentru overflow-ul Timer1 
#pragma vector = TIMER1_OVF_vect __interrupt void T1_OVF() 
{ 
// Incrementarea numărului de overflow-uri  
	Timer1_numberOverflows++; 
}

int main(void)
{
	MCUSR = 0;

	USART_initialize(BAUD_RATE);

	TCCR1B |= (1 << CS10);
	TIMSK1 |= (1 << TOIE1);

	__enable_interrupt();

	WDTCSR |= (1 << WDCE) | (1 << WDE);
	WDTCSR = (1 << WDE) | (1 << WDP0);

	if (Timer1_numberOverflows > 0 || Timer1_currentValue > 0)
	{
		uint32_t number =
			(Timer1_numberOverflows * (uint64_t)65535) +
			Timer1_currentValue;

		uint8_t period = number * 0.0000625;
		uint16_t time_per_clock = (period * 1000000) / 4096;
		uint8_t frecv = (1000000. / time_per_clock);

		uint16_t copyFrecv = frecv;
		myprint(INTEGER, &copyFrecv);
	}

	Timer1_numberOverflows = 0;
	Timer1_currentValue = 0;
	TCNT1 = 0;

	asm("WDR");

	while (1)
	{
		Timer1_currentValue = TCNT1;
	}

	return 0;
}

```

