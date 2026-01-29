```c
#include <ioavr>
#include <inavr>
#include <stdint>
#include <stdio.h>
#include <string.h>

#define FOSC 16000000UL
#define BAUD 9600UL
#define STR_LEN 64


__no_init uint16_t ovfs;
uint32_t total_ticks;
_no_init uint16_t current_ticks;

uint8_t stringToSend[STR_LEN];

#pragma vector = TIMER3_OVF_vect
__interrupt void Timer3_OVF_vect(void){
    ovfs++;
}


void config_timer3();

void config_usart3();

void transmit_usart(uint8_t char_to_send);

int main()
{
    uint8_t MCUSRbackup = MCUSR;
    MCUSR = 0;
    memset(stringToSend,0,16*sizeof(uint8_t));

    uint8_t isResetByWD = (MCUSRbackup & (1<<WDRF));

    // enable timer here
        // it has interrupt on ovf
    config_usart3();

    if( (ovfs > 0 || current_ticks > 0) && isResetByWD){
        uint32_t total_ticks = ((uint32_t)ovfs*65536
        UL) + current_ticks;

        uint32_t knum = 2048UL * 16000UL;
        uint32_t fkhz = knum / total_ticks;
        uint32_t rest = knum % total_ticks;
        uint32_t fhz = fkhz*1000 + (1000UL*rest)/total_ticks;
        snprintf(stringToSend,STR_LEN,"f=%lu\n",fhz);
        uint8_t stringLen = strlen(stringToSend);
        int i;
        for(i = 0; i < stringLen; i++){
            transmit_usart(stringToSend[i]);
        }
    }

    config_timer3();
    __enable_interrupt();
    // setup wd
    WDTCSR |= ((1<<WDTCE) | (1<<WDE));
    WDTCSR |= (1<<WDE);

    asm("WDR");
    while(1){
        current_ticks = TCNT3;
    }

    return 0;
}

void config_timer3(){

    // mode normal -- no prescaler -- Tovf aprox 4ms
    TCNT3 = 0;
    ovfs = 0;
    current_ticks = 0;
    TIMSK3 |= (1<<TOIE3);
    TCCR3B |= (1<<CS30);
}

void config_usart3(){
    uint16_t baud_rate = (FOSC/16UL)/BAUD_RATE - 1;
    UBRR3H = (baud_rate >> 8) & 0x00FF;
    UBRR3L = (baud_rate & 0x00FF);

    UCSR3C |= ((1<<UCSZ31) | (1<<UCSZ30)); // 8bit
    UCRS3B |= ( (1<<RXEN3) | (1<<TXEN3 )); // enable receive and transmit
}

void transmit_usart(uint8_t char_to_send){
    while( !(UCSR3A & (1<<UDRE3)) ){
        ;
    }
    UDR3 = char_to_send;
}

```