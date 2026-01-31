
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

### Intreruperi

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




