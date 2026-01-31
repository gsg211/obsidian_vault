
# info dump initial

Există o gamă largă de procesoare bazate pe acelaşi nucleu ARM

Application Processors 
	ARM Cortex-A8 ARM Cortex-A9
Embedded Processors 
	ARM Cortex-M0 ARM Cortex-M1
Secure Cores
	SC300 SecurCore SC100 SecurCore SC200

JDSnet – ARM emulator
a real-time, transparent in-circuit debugger with a real- time trace buffer.

# Intreruperi

1978, von Neumann, CISC, 99 instrucțiuni 

CPU dispune de 2 canale: 
– NMI (Non-Maskable Interrupt) 
– INTR (Interrupt Request)

Expandare posibilă cu PIC: 
– Programmable Interrupt Controller 
– până la 224 surse diferite

Magistrală de sistem în mod minim (Minimum mode system bus) – magistrală controlată de un singur CPU

Magistrală de sistem în mod maxim (Multimaster system bus) – comunicația poate fi inițiată de mai multe procesoare – magistrală multimaster – bus controller necesar

Intreruperi vectorizate 
• Asociere cod(type) ↔ eveniment
– evenimente interne: type 0, 1, 3, 4 
– evenimente externe: type 2, 32, 33 … 
– interne/externe relativ la CPU

CS + IP = adresa ISR

Prioritățile evenimentelor
– fixe la nivel de CPU 
– configurabile la nivel de 

#### ARMv5

Folosește noțiunea de: “Excepție” (Exception) 
• 7 tipuri de excepții 

• 7 moduri de execuție (Processor mode) – Modul utilizator: acces limitat la resurse
– 6 moduri de execuție privilegiată 
	• System mode – OS tasks 
	• 5 exception mode

####  ARMv8-M – TrustZone

Tehnologia TrustZone – securitatea execuției 
• conceptual similar cu TrustZone din arhitectura Cortex-A •optimizat pentru sisteme incorporate de timp real
• optimizat pentru consum redus de energie – implementare opțională (de către proiectanții de MCU / SoC

# memoria

Arhitecturi de calculatoare 
(dpdv. al interacțiunii CPU ↔ memorie)
– von Neumann (aka: Princeton model)
	O singură memorie principală, o unitate aritmetico-logică (ALU), o unitate de control și registre.

– Harvard 
	Două memorii distincte, fiecare cu propria magistrală de adrese și de date.

– Advanced Harvard
	Deși păstrează magistralele separate, permite anumite interacțiuni între spațiile de memorie.

– hibrid: mixtură între von Neumann și Harvard
(core Harvard, mem ext. von Neumann)


Convenția de stocare (endianess) 
– Little-endian (lit. Intel convention): octetul cel mai semnificativ la adresa mai mare 

– Big-endian (lit. Network order): octetul cel mai semnificativ la adresa mai mică

Aliniere (alignment) 
– stocarea datelor/instrucțiunilor doar la adrese pare 

– accesul nealiniat poate conduce la(depinde de target, compilator): 
	• penalități de timp 
	• ocuparea compactă a spațiu de stocare 


Memorie Read-Only (ROM) 
	– mask ROM – scriere din fabrică (low-cost) 
	– PROM (Programmable ROM) – scriere cu dispozitiv programator 
	– EPROM (Erasable PROM) – ștergere totală prin expunere la UV 
	– Flash EPROM – ștergere totală electrică 
	– EEPROM (Electrically Erasable PROM) – scriere la nivel de octet

Memorie Read-Write 
– DRAM (Dynamic RAM) 
	• un bit de informație = un condensator + un tranzistor 
	• elementul de stocare: condensatorul

– trebuie reîncărcat atunci când stochează “1” logic, din 2 motive: 
	» pierde sarcină electrică (charge leakage) 
	» citirea este un proces destructiv 

– SRAM (Static RAM) 
• un bit de informație = un flip-flop = 4 tranzistori 
• elementul de stocare: flip-flop-ul

– NVRAM (Non-volatile RAM) 
• RAM + baterie + circuit de control al bateriei


Memoria Cache, respectiv memoria virtuală creează iluzia pentru programator (pentru programul scris de el) că memoria de care dispune este: 
– mare cât o memorie de tip Disk 
– rapidă cât o memorie de tip SRAM

##### 8086

Arhitectură de tip von Neumann, deoarece:
memoriile de cod, date sunt conectate la aceeași magistrală de date/adrese

Spațiul de memorie este unul continuu la nivel de adresă fizică


#### Little endian

Valoarea 0x5602 stocată la adresa 724H

|       | 74h  | 74h  | 75H  | 75H  |
| ----- | ---- | ---- | ---- | ---- |
| HEXA  | 0    | 2    | 5    | 6    |
| BiNAR | 0000 | 0010 | 0101 | 0110 |

#### adresare

bază segment (16 biți) + deplasament (16 biți)

Moduri de adresare: 
– imediată
– directă 
– indirectă prin registru
– bazată 
– indexată 
– bazată și indexată

# Stiva

o zonă de memorie folosită convențional ca o structură

Utilizată pentru: 
	salvarea contextului 
	transmiterea parametrilor între funcții 
	stocarea adresei de întoarcere din funcție 
	stocarea temporară de date 
	stocarea variabilelor locale funcției


Baza(bottom) stivei = adresa de început a stivei

Stiva se populează descrescător 
	PUSH realizează decrementarea SP 
	element introdus mai recent = adresă mai mică 

```
SP – registrul Pointer de Stivă (Stack Pointer) (16 bit) 
SS – registrul Segment de Stivă (Stack Segment) (16 bit)
SS<<4 + SP = adresa fizică a ultimului element introdus
SS conține adresa segmentului de stivă, SP conține deplasament față de SS 
POP, PUSH: doar cuvinte de 16 biți (2 octeți)
```


Instrucțiunea CALL – apel de funcție 
	generează salvarea pe stivă a adresei de retur 
	parametrii funcției se recomandă a fi transmiși prin stivă

Instrucțiunea RET (return) 
	generează descărcarea de pe stivă și saltul la adresa de retur
	IRET - revenire din rutina de tratare a întreruperii


PUSHF POPF -> flagurile

PUSHA POPA -> incarca toate registrele


Instrucțiunea ACALL Absolute Call (codificare pe 2 octeți) 
	apel de subrutină(funcție) într-un interval de maxim 2kB

Instrucțiunea LCALL (Long Call) (codificare pe 3 octeți) 
	apel de subrutină în întreg spațiul de 64kB