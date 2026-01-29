calc tim
```c
#include <iom1280.h> 
#include <inavr.h>        
#include <stdint.h>


#define FCREV_CLK_IO 16000000UL

__no_init uint32_t Timer_curentValue;
__no_init uint32_t Nr_OV;

uint8_t i;
uint32_t timp;
#pragma vector = TIMER1_OVF_vect 
__interrupt void T1_OVF() 
{  
  // Incrementarea numarului de overflow-uri 
  Nr_OV++;  
}


int main()
{
  if((MCUSR & (1 << EXTRF)) == (1 << EXTRF)) //s-a dat reset
  {
    if(Timer_curentValue > 0 || Nr_OV > 0)
    {
      Timer_curentValue += ((uint32_t) Nr_OV) << 16;
      timp = Timer_curentValue * 8/ (FCREV_CLK_IO / 1000UL);
      ++i; //aici pui breakpoint
      Timer_curentValue = 0;
      Nr_OV = 0;
      TCCR1B |= (1 << CS11);
      TIMSK1 |= (1 << TOIE1);
      __enable_interrupt();
    }
    else
    {
      Timer_curentValue = 0;
      Nr_OV = 0;
      TCCR1B |= (1 << CS11);
      TIMSK1 |= (1 << TOIE1);
      __enable_interrupt();
    }
  }
  else
  {
      Timer_curentValue = 0;
      Nr_OV = 0;
  }
  MCUSR = 0;
  while(1)
  {
    Timer_curentValue = TCNT1;
  }
}

```