
### info dump initial

Există o gamă largă de procesoare bazate pe acelaşi nucleu ARM

Application Processors 
	ARM Cortex-A8 ARM Cortex-A9
Embedded Processors 
	ARM Cortex-M0 ARM Cortex-M1
Secure Cores
	SC300 SecurCore SC100 SecurCore SC200

JDSnet – ARM emulator
a real-time, transparent in-circuit debugger with a real- time trace buffer.

#### Intreruperi

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

