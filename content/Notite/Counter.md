
[[SOC FINAL 2025.pdf#page=132&selection=9,0,15,6|SOC FINAL 2025, page 132]]

## GENERAL
### Frecv

$$
F_{PWM} = \frac{F_{clk}}{Prescaler * {timer_{max}}}
$$
timer_max = 2^nrBiti

### Duty Cycle

Factorul de umplere (Duty Cycle) este procentul de timp în care semnalul PWM este "activ" (adică are valoare logică 1).

$$
Duty\ cycle = \frac{OCR_{xn}}{timer_{max}} \cdot 100
$$
$$
	OCR_XN= timer_{max} \cdot duty\ cycle / 100
$$


### Mod operare

• BOTTOM: reprezintă valoarea minimă a numărătorului (counter); 
• MAX: reprezintă valoarea maximă a numărătorului (counter); 
• TOP: counter-ul ajunge la valoarea TOP când devine egal cu cea mai mare valoare din secvența de numărat, iar această valoare poate fi fixă (MAX) sau valoarea stocată în registrul <span style="background:#affad1">OCRnx</span> (output compare register) în funcție de modul de operare.

##### CLEAR TIMER ON COMPARE MATCH (CTC)

numărătorul crește de la 0, dar în loc să ajungă la valoarea MAX, se resetează automat la 0 atunci când valoarea sa devine egală cu cea din registrul OCRnx (sau ICRn în cazul TCNT pe 16 biți). Astfel, OCRnx acționează ca o valoare maximă personalizată (TOP).

basically cand atinge top se reseteaza

![[Pasted image 20260125141431.png]]
Generarea unui semnal pe pin se face de obicei prin funcția de "Toggle" (comutare). Pinul trece din 0 în 1 la prima potrivire și din 1 în 0 la a doua. Din acest motiv, **<u>factorul de umplere este întotdeauna fix la 50</u>%** 
##### Fast PWM

Modul Fast PWM se bazează pe o numărare cu o singură pantă (single-slope): 

numărătorul crește de la 0 la valoarea TOP, apoi este resetat brusc înapoi la 0, formând o undă de tip "fierăstrău". Ieșirea PWM își schimbă starea la începutul ciclului (când numărătorul este la 0) și la potrivirea dintre TCNTn și OCRnx

![[Pasted image 20260125142557.png]]

Întrerupere Match OCRnX -> final  de DC (ocnx se schimba)
intrerupere overflow => se reseteaza timeru (final perioada)

##### Phase correct PWM

Phase Correct PWM funcționează pe principiul numărării cu pantă dublă (dual-slope): 

numărătorul crește de la 0 (BOTTOM) la TOP, apoi scade simetric înapoi la 0, formând o undă triunghiulară. Ieșirea PWM își schimbă starea o dată pe panta crescătoare și a doua oară pe panta descrescătoare, la potrivirea cu OCRnx.

![[Pasted image 20260125143228.png]]


## Timer atmega

ATmega1280 dispune de 6 timere dintre care:

##### 2 sunt pe 8 biți 
	Timer / Counter0
	Timer / Counter2
###### TCNT0
are două registre pe 8 biți de comparare a ieșirilor (Output Compare Registers)
poate face PWM

###### TIMER/COUNTER2

Al doilea modul pe 8 biți, Timer/Counter2 (TCNT2), oferă în plus capacitatea de a opera asincron, însă clock-ul este selectat diferit, deoarece trebuie să fie utilizată o sursă de clock asincronă.


##### 4 pe 16 biți 
	Timer / Counter1
	Timer / Counter3
	Timer / Counter4
	Timer / Counter5

Acestea au în plus față de Timer / Counterele pe 8 biți încă un registru de comparare a ieșirii (OCRnC) și unitatea de captare a intrărilor (Input Capture) cu registrele aferente.

## SURSA DE CLOCK ȘI CONTROLUL FRECVENȚEI


Poate fi selectată fie
	intern, derivată din ceasul de sistem (f_clk_I/O), 
	fie extern, prin intermediul pinului Tn.

###### interna
Sursa de ceas internă este derivată din ceasul de sistem al microcontrolerului (f_clk_I/O). Această opțiune asigură o funcționare sincronă cu restul perifericelor și este modul de operare principal pentru funcții de temporizare și generare de semnale PWM

Diviziunile disponibile pentru frecvența de ceas sunt: 
	• clkIO / 1 (fără prescalare); 
	• clkIO / 8;
	• clkIO / 64; 
	• clkIO / 256; 
	• clkIO / 1024;

Notă: Resetarea prescaler-ului (prin registrul GTCCR) va afecta toate temporizatoarele care îl utilizează, fiind necesară o resincronizare a acestora dacă operarea simultană este critică.

###### externa
Temporizatorul poate fi configurat să utilizeze o sursă externă de ceas, aplicată pe pinul corespunzător Tn (ex: T0, T1). Această funcționalitate este esențială pentru contorizarea evenimentelor externe, independent de frecvența de operare a microcontrolerului. Configurarea permite selectarea frontului pe care temporizatorul va acționa: • front crescător (rising edge) al semnalului extern; • front descrescător (falling edge) al semnalului extern. <span style="background:#d3f8b6">Notă: Sursa de ceas externă nu poate fi divizată de către prescalerul intern.</span>


# model programare

```
Pașii necesari pentru configurarea timer-ului: 

1. Inițializare pin de ieșire – se setează pinul asociat canalului PWM ca output în registrul DDR. 

2. Selectarea timer-ului – se poate alege un timer pe 8 biți (TCNT0, TCNT2) sau pe 16 biți (TCNT1, TCNT3, TCNT4, TCNT5).
   
3. Configurarea modului de generare a formei de undă – se setează biții WGMn3:0 din registrele TCCRnA / TCCRnB. 
   
4. Setarea modului de comparare pe pin (COMnx1:0) – se alege modul “non-inversat” (semnal HIGH la începutul perioadei și LOW la comparației) sau inversat. 
   
5. Alegerea sursei de clock și a prescalerului – se configurează biții CSn2:0 în registrul TCCRnB pentru prescaler (1, 8, 64, 256, 1024). 
   
6. Setarea factorului de umplere (duty cycle) – se scrie în registrul OCRnx.

7. Creare buclă principală – se poate modifica OCRnx în timp real pentru a ajusta factorul de umplere.
```

![[Pasted image 20260125150948.png]]

