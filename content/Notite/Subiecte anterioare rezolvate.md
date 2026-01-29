
> [!danger]
> sunt pt atmega 16 dar merge inspirat de aici

# 1

```c
#include <ioavr.h>
#include <inavr.h>
#include "myprint.h"

/*
1.Sa se calculeze si afiseze pe serial in format x.xxx frecventa WatchDog cu WDP2-0:100.
*/

#define FRECV 4021232
#define WDCYCLES 262144

__no_init unsigned int count;
__no_init unsigned int ovfs;


#pragma vector=TIMER1_OVF_vect
__interrupt void overflow() {
  ++ovfs;
}


int main() {
  if (MCUCSR & (1 << WDRF)) {
    unsigned long cycles = ((unsigned long)ovfs << 16) | count;
    unsigned long period = cycles * 1000000000LL / FRECV; // ns  
    
    unsigned long wd_period = period / WDCYCLES; // ns
    unsigned long wd_freq = 1000000L / wd_period; // kHz
    
    USART_initialize(BAUD_RATE);
    
    unsigned long mii = wd_freq / 1000;
    unsigned long rest = wd_freq % 1000;
    
    USART_transmit(mii + '0');
    USART_transmit('.');
    
    integerTransmit(3, &rest);
    USART_transmit('\n');
    USART_transmit('\r');
  }
  
  MCUCSR = 0;  // pune pe 0 flagurile de RESET (care nu se reseteaza la fiecare reset)
  
  __enable_interrupt();
  
  TIMSK |= (1 << TOIE1);
  
  ovfs = 0;
  
  asm("WDR");
  WDTCR |= (1 << WDE) | (1 << WDP2);
  
  TCCR1B |= (1 << CS10);
 
  while(1) {
    count = TCNT1;
  }
}

```

# 2

```c
#include <ioavr.h>
#include <inavr.h>

/*
2.Implementati un generator de semnal dreptunghiular cu frecventa de 5Hz, 
cu factor de umplere de 66%, implementare cu watchdog.
*/

__no_init int state;


int main() {
  if (state == 0 || state == 1) {
    PORTC |= (1 << PC0);	// PC0 are rezistenta de pull-up externa
    state++;
  } else {
    PORTC &= ~(1 << PC0);
    state = 0;
  }
  
  DDRC |= (1 << PC0);
  
  asm("WDR");
  WDTCR |= (1 << WDE) | (1 << WDP1);
  
  while(1);
}
```

# 3.
```c
#include <ioavr.h>
#include <inavr.h>
#include "myprint.h"

/*
3.Generati un semnal de 1kHz cu factor de umplere care variaza crescator si descrescator, 
cu pasi de 5%, intre 5 si 95 % la fiecare 2 secunde si transmitera factorului de umplere pe serial.
*/

#define FRECV 4006424


unsigned int novfs = 0;
unsigned int duty = 5;
unsigned int direction = 0;
unsigned int flag = 0;


#pragma vector = TIMER0_COMP_vect
__interrupt void compare_match() {
  TIMSK &= ~(1 << OCIE0);
  TCCR0 &= ~(1 << WGM01);
  flag = 1;
}


#pragma vector = TIMER0_OVF_vect
__interrupt void overflow() {
  ++novfs;
  
  // 2s / (256 * 1024 * 1000000 / FRECV) = 30.5157
  if (novfs == 30) {
    OCR0 = 142; 
    
    TCCR0 |= (1 << WGM01);

    TIMSK |= (1 << OCIE0);
    
    novfs = 0;
  }
}


int main() {
  USART_initialize(BAUD_RATE);
        
  // TOP = FRECV / 1kHz - 1
  OCR1A = 4005;
  // CMP = (TOP + 1) * 0.05 - 1
  OCR1B = 199;
  
  //pin de iesire a semnalului de 1 kHz
  DDRD |= (1 << PD4);
  
  //pin de iesire a semnalului de perioada 2 sec
  DDRC |= (1 << PC0);
  PORTC &= ~(1 << PC0);
  
  __enable_interrupt();
  
  TIMSK |= (1 << TOIE0);
  
  TCCR0 |= (1 << CS02) | (1 << CS00);
  
  TCCR1A |= (1 << WGM11) | (1 << WGM10) | (1 << COM1B1);
  TCCR1B |= (1 << CS10) | (1 << WGM12) | (1 << WGM13);
  
  while(1) {
    if (flag) {
      flag = 0;
      
      if (duty == 5) {
        direction = 0;
      } else if (duty == 95) {
        direction = 1;
      }
      
      if (direction) {
        duty -= 5;
      } else {
        duty += 5;
      }
      
      OCR1B = (unsigned long)duty * 4021 / 100 - 1;
      
      PORTC ^= (1 << PC0);
      
      integerTransmit(2, &duty);
      USART_transmit('\n');
      USART_transmit('\r');
    }
  }
}

```

# 4

```c
#include <ioavr.h>
#include <inavr.h>
#include "myprint.h"

/*
4.Calculati si transmiteti pe seriala in formatul XX.XX secunde timpul de la ultima apasare a butonului reset de pe placa.
*/


#define FRECV 4006424


__no_init unsigned long long novfs;
__no_init unsigned int count;


#pragma vector = TIMER1_OVF_vect
__interrupt void overflow() {
  ++novfs;
}


void main() {
  if ((MCUCSR & (1 << EXTRF))) {
    unsigned long long cicli = (novfs << 16) | count;
    unsigned long long period = cicli * 1000 / FRECV; // ms
        
    novfs = 0;
    count = 0;
    
    unsigned int sec = period / 1000;
    unsigned int rest = period % 1000;
    
    USART_initialize(BAUD_RATE);
    
    integerTransmit(2, &sec);
    USART_transmit('.');
    integerTransmit(2, &rest);
    USART_transmit('\n');
    USART_transmit('\r');
  }
  
  MCUCSR = 0;  // pune pe 0 flagurile de RESET (care nu se reseteaza la fiecare reset)
  
  __enable_interrupt();
  
  TIMSK |= (1 << TOIE1);
  
  TCNT1 = count;
  
  TCCR1B |= (1 << CS10);
  
  while(1) {
    count = TCNT1;
  }
}
```

# 5

```c
#include <ioavr.h>
#include <inavr.h>
#include "myprint.h"

/*
7.Se citesc din terminal doua nr 0xXXXX si 0xYYYY. 
Sa se calculeze si sa se afiseze pe seriala CRC-ul zonei de memorie flash 
dintre adresa 0xXXXX si 0xYYYY.
*/


#define CRC32 0x04C11DB7 //normal - MSBF
//#define CRC32 0xEDB88320 //inversed - LSBF

//#define CRC16 0x1021 //normal - MSBF
#define CRC16 0x8408 //inversed - LSBF

enum BitOrder { LSBF, MSBF };

unsigned long crc32(unsigned long polinom32, unsigned long init_val_32, unsigned int adr_start,unsigned int len, enum BitOrder ord) 
{ 
  //variabile pe 32 biti 
  unsigned long crc = init_val_32;
  unsigned long data = 0;
  while( len-- ) 
  { 
    int i; //se extrage un octet din memoria flash
    data = *(__flash char *)adr_start; 
    if (ord == MSBF) //opþiunea cu shiftare spre MSB 
    { //octetul extras este shiftat pânã la cel mai semnificativ bit 
      data <<= 24; 
      crc ^= data; 
      adr_start++;
      for( i = 0; i < 8; ++i ) 
      { //se verificã dacã cel mai semnificativ bit este 1 
        if( crc & 0x80000000 ) 
          crc = (crc << 1) ^ polinom32; 
        else 
          crc = crc << 1;
      } 
    } 
    else //opþiunea cu shiftare spre LSB 
      //parametrul polinom32 al funcþiei trebuie sã aibã biþii inversaþi în prealabil 
    { 
      crc ^= data; 
      adr_start++; 
      for( i = 0; i < 8; ++i ) 
      { //se verificã dacã cel mai puþin semnificativ bit este 1 
        if( crc & 0x00000001 ) 
          crc = (crc >> 1) ^ polinom32; 
        else 
          crc = crc >> 1;
      } 
    } 
  }
  return crc;
  //return ~crc; //complement fata de 1
  //return -crc; //complement fata de 2
}


#define FRECV 4006424


unsigned char decode(char c) {
  if (c >= 'A' && c <= 'F') {
    return c - 'A' + 10;
  }
  
  if (c >= '0' && c <= '9') {
    return c - '0';
  }
  
  return 0;
}


unsigned int read_address() {
  unsigned int adr = 0;
  
  // Skip 0x
  USART_receive();
  USART_receive();
  
  char c = USART_receive();
  adr |= decode(c);
  
  c = USART_receive();
  adr = (adr << 4) | decode(c);
  
  c = USART_receive();
  adr = (adr << 4) | decode(c);
  
  c = USART_receive();
  adr = (adr << 4) | decode(c);
  
  return adr;
}


int main() {
  USART_initialize(BAUD_RATE);
  
  unsigned int start_adr = read_address();
  unsigned int end_adr = read_address();
  
  unsigned long crc = crc32(CRC32, 0, start_adr, end_adr - start_adr, MSBF);
  
  hexadecimalTransmit(8, &crc);
}
```
# 6

```c
#include <ioavr.h>
#include <inavr.h>
#include "myprint.h"

/*
8.Generati un semnal de 2,5 Khz cu precizie de 2%, sa aiba factorul de umblere initial de 5% si 
sa urce/scada cu 3% de la 5% la 95% in 2 secunde.
*/

#define FRECV 4006424


unsigned int novfs = 0;
unsigned int duty = 5;
unsigned int direction = 0;
unsigned int flag = 0;


#pragma vector = TIMER0_COMP_vect
__interrupt void compare_match() {
  TIMSK &= ~(1 << OCIE0);
  TCCR0 &= ~(1 << WGM01);
  flag = 1;
}


#pragma vector = TIMER0_OVF_vect
__interrupt void overflow() {
  ++novfs;
  
  // 2s / (256 * 1024 * 1000000 / FRECV) = 30.5157
  if (novfs == 30) {
    OCR0 = 142;
    
    TCCR0 |= (1 << WGM01);

    TIMSK |= (1 << OCIE0);
    
    novfs = 0;
  }
}


int main() {
  USART_initialize(BAUD_RATE);
        
  // TOP = FRECV / 2.5kHz - 1
  OCR1A = 1601;
  // CMP = (TOP + 1) * 0.05 - 1
  OCR1B = 47;
  
  //pin de iesire a semnalului de 1 kHz
  DDRD |= (1 << PD4);
  
  //pin de iesire a semnalului de perioada 2 sec
  DDRC |= (1 << PC0);
  PORTC &= ~(1 << PC0);
  
  __enable_interrupt();
  
  TIMSK |= (1 << TOIE0);
  
  TCCR0 |= (1 << CS02) | (1 << CS00);
  
  TCCR1A |= (1 << WGM11) | (1 << WGM10) | (1 << COM1B1);
  TCCR1B |= (1 << CS10) | (1 << WGM12) | (1 << WGM13);
  
  while(1) {
    if (flag) {
      flag = 0;
      
      if (duty == 5) {
        direction = 0;
      } else if (duty == 95) {
        direction = 1;
      }
      
      if (direction) {
        duty -= 3;
      } else {
        duty += 3;
      }
      
      OCR1B = (unsigned long)duty * 1602 / 100 - 1;
      
      PORTC ^= (1 << PC0);
    }
  }
}

```

# 7

```c
#include <iom16.h>
#include <inavr.h>

/*
9.Sa se implementeze un generator de frecventa cu ajutorului Watchdog Timer. Frecventa sa fie in jurul valorii de 12Hz si factor de umplere de 20% !
*/

__no_init int state;

void main()
{
  asm("WDR");
  if(state == 0)
  {
    PORTD |= (1 << PD6);
    state = 1;     // 1x perioada
  }
  else
  {
    WDTCR |= (1 << WDP1);   // 4x perioada
    PORTD &= ~(1 << PD6);
    state = 0;
  }
  WDTCR |= (1 << WDE);
  
  DDRD |= (1 << PD6);
  
  while(1);
}

```

# 8
count extern

```c
#include <ioavr.h>
#include <inavr.h>

#define FRECV 4006672

#include "usart.h"
#include "myprint.h"
__no_init unsigned int count;

void main() {
  USART_initialize(BAUD_RATE);
  if (MCUCSR & (1 << EXTRF))
  {
    count++;
  
    integerTransmit(3, &count);
    USART_transmit('\n');
    USART_transmit('\r');
    
  }
  else
  {
    if (MCUCSR & (1 << JTRF))
    {
      count = 0;
    }
  }
  MCUCSR = 0;

  while(1);  
}

```

# 9

```c
#include "myprint.h"


#define CRC16 0x1021 //normal - MSBF
//#define CRC16 0x8408 //inversed - LSBF

enum BitOrder { LSBF, MSBF };

unsigned int crc16(unsigned int polinom16, unsigned int init_val_16, unsigned int adr_start,unsigned int len, enum BitOrder ord)
{
  unsigned int crc = init_val_16;
  unsigned int data = 0;
  while( len-- ) {
    unsigned int i;
    //se extrage valoarea unui octet de la adresa de start
    //din memoria flash
    data = *(__flash char *)adr_start;
    if ( ord == MSBF ) //opþiunea cu shiftare spre MSB
    {
      //octetul este shiftat la stânga pentru a se alinia
      //cu polinomul generator
      data <<= 8;
      //datele sunt "transferate" în rezultat
      crc ^= data;
      adr_start++;
      //pentru biþii de date se face XOR cu polinomul generator,
      //daca bitul cel mai semnificativ este 1 sau se shifteaza datele
      //la stânga, dacã bitul cel mai semnificativ este 0
      for( i = 0; i < 8; ++i ) {
        //se verificã dacã bitul cel mai semnificativ este 1
        if( crc & 0x8000 )
          crc = (crc << 1) ^ polinom16;
        else
          crc = crc << 1;
      }
    }
    else
      //opþiunea cu shiftare spre LSB
      //parametrul polinom16 al funcþiei trebuie sã aibã biþii inversaþi în
      //prealabil
    {
      crc ^= data;
      adr_start++;
      //se verificã bitul cel mai putin semnificativ .i dacã
      //acesta este 1 se face XOR cu polinomul generator,
      // altfel datele sunt shiftate la dreapta
      for( i = 0; i < 8; ++i ) {
        //se verificã daca cel mai puþin semnificativ bit este 1
        if( crc & 0x0001 )
          crc = (crc >> 1) ^ polinom16;
        else
          crc = crc >> 1;
      }
    }
  }
  return crc;
}


unsigned char min(unsigned char x, unsigned  char y) {
  if (x < y)
    return x;
  else 
    return y;
}


char antet[] = "CRC16 ADR[";
char mid[] = " - ";
char subsol[] = "] ";


#define RET_INSTRUCTION 0x9508


void main() {
  USART_initialize(BAUD_RATE);
  
  unsigned int start_adr = ((unsigned int)min) << 1;
  unsigned int current_adr = start_adr;
  
  unsigned int instruction;
  do {
    instruction = *(__flash unsigned int *)current_adr;
    current_adr += 2;
  } while(instruction != RET_INSTRUCTION);
  
  do {
    instruction = *(__flash unsigned int *)current_adr;
    current_adr += 2;
  } while(instruction != RET_INSTRUCTION);
  
  unsigned int crc = crc16(CRC16, 0, start_adr, current_adr - start_adr, MSBF);
  
  characterTransmit(sizeof(antet), antet);
  
  paddedHexadecimalTransmit(4, &start_adr);
  
  characterTransmit(sizeof(mid), mid);
  
  paddedHexadecimalTransmit(4, &current_adr);
  
  characterTransmit(sizeof(subsol), subsol);
  
  paddedHexadecimalTransmit(4, &crc);
  
  USART_transmit('\r');
  USART_transmit('\n');
}
```

# 10

pwm 20 khx

```c
#include <ioavr.h>
#include <inavr.h>
#include "myprint.h"


#define FRECV 4006424


unsigned int novfs = 0;
unsigned int duty = 5;
unsigned int direction = 0;
unsigned int flag = 0;


#pragma vector = TIMER0_COMP_vect
__interrupt void compare_match() {
  TIMSK &= ~(1 << OCIE0);
  TCCR0 &= ~(1 << WGM01);
  flag = 1;
}


#pragma vector = TIMER0_OVF_vect
__interrupt void overflow() {
  ++novfs;
  
  // 1s / (256 * 1024 * 1000000 / FRECV) = 15.28329
  if (novfs == 15) {
    // (256 * 0.2839) - 1 = 71.5234
    OCR0 = 71;
    
    TCCR0 |= (1 << WGM01);

    TIMSK |= (1 << OCIE0);
    
    novfs = 0;
  }
}


int main() {
  USART_initialize(BAUD_RATE);
        
  // TOP = FRECV / 1kHz - 1
  OCR1A = 199;
  // CMP = (TOP + 1) * 0.05 - 1
  OCR1B = 9;
  
  //pin de iesire a semnalului de 1 kHz
  DDRD |= (1 << PD4);
  
  //pin de iesire a semnalului de perioada 2 sec
  DDRC |= (1 << PC0);
  PORTC &= ~(1 << PC0);
  
  __enable_interrupt();
  
  TIMSK |= (1 << TOIE0);
  
  TCCR0 |= (1 << CS02) | (1 << CS00);
  
  TCCR1A |= (1 << WGM11) | (1 << WGM10) | (1 << COM1B1);
  TCCR1B |= (1 << CS10) | (1 << WGM12) | (1 << WGM13);
  
  while(1) {
    if (flag) {
      flag = 0;
      
      if (duty == 5) {
        direction = 0;
      } else if (duty == 95) {
        direction = 1;
      }
      
      if (direction) {
        duty -= 1;
      } else {
        duty += 1;
      }
      
      OCR1B = (unsigned long)duty * 199 / 100 - 1;
      
      PORTC ^= (1 << PC0);
      
      /*integerTransmit(2, &duty);
      USART_transmit('\n');
      USART_transmit('\r');*/
    }
  }
}

```

# 11
crc 

```c
#include <iom16.h>
#include <inavr.h>
#include "myprint.h"


#define CRC16 0x1021 //normal - MSBF
//#define CRC16 0x8408 //inversed - LSBF


enum BitOrder { LSBF, MSBF };


unsigned int crc16(unsigned int polinom16, unsigned int init_val_16, char *adr_start,
                   unsigned int len, enum BitOrder ord)
{
  unsigned int crc = init_val_16;
  unsigned int data = 0;
  while( len-- ) {
    unsigned int i;
    //se extrage valoarea unui octet de la adresa de start
    //din memoria flash
    data = *adr_start;
    if ( ord == MSBF ) //opþiunea cu shiftare spre MSB
    {
      //octetul este shiftat la stânga pentru a se alinia
      //cu polinomul generator
      data <<= 8;
      //datele sunt "transferate" în rezultat
      crc ^= data;
      adr_start++;
      //pentru biþii de date se face XOR cu polinomul generator,
      //daca bitul cel mai semnificativ este 1 sau se shifteaza datele
      //la stânga, dacã bitul cel mai semnificativ este 0
      for( i = 0; i < 8; ++i ) {
        //se verificã dacã bitul cel mai semnificativ este 1
        if( crc & 0x8000 )
          crc = (crc << 1) ^ polinom16;
        else
          crc = crc << 1;
      }
    }
    else
      //opþiunea cu shiftare spre LSB
      //parametrul polinom16 al funcþiei trebuie sã aibã biþii inversaþi în
      //prealabil
    {
      crc ^= data;
      adr_start++;
      //se verificã bitul cel mai putin semnificativ .i dacã
      //acesta este 1 se face XOR cu polinomul generator,
      // altfel datele sunt shiftate la dreapta
      for( i = 0; i < 8; ++i ) {
        //se verificã daca cel mai puþin semnificativ bit este 1
        if( crc & 0x0001 )
          crc = (crc >> 1) ^ polinom16;
        else
          crc = crc >> 1;
      }
    }
  }
  return crc;
}


const char *msg = " CRC16 ";


void main()
{
  USART_initialize(BAUD_RATE);
  
  char sir[32];
  int read = 0;
  
  do {
    sir[read] = USART_receive();
    ++read;
  } while(sir[read - 1] != '@' && read < 32);
  
  // Scoatem terminatorul de sir
  --read;
  
  unsigned int crc = crc16(CRC16, 0, sir, read, MSBF);
  
  int i;
  for (i = 0; i < read; ++i) {
    USART_transmit(sir[i]);
  }
  
  characterTransmit(7, (void *)msg);
  
  hexadecimalTransmit(4, &crc);
}
```

# 12

watch dog toggle led

```c
#include <ioavr.h>
#include <inavr.h>
#include "myprint.h"


#define FRECV 4021232
#define WDCYCLES 262144


__no_init unsigned int count;
__no_init unsigned int ovfs;


__no_init int led;


int main() {
  if (MCUCSR & (1 << WDRF)) {
    if (led) {
      led = 0;
      PORTC &= ~(1 << PC0);
    } else {
      led = 1;
      PORTC |= (1 << PC0);
    }
    DDRC |= (1 << PC0);
  }
  
  MCUCSR = 0;  // pune pe 0 flagurile de RESET (care nu se reseteaza la fiecare reset)
  
  __watchdog_reset();
  WDTCR |= (1 << WDE) | (1 << WDP1);
 
  while(1);
}
```

