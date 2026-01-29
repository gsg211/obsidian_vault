# main.c

```c
#include "TimerOptions.h"
#include <ioavr.h>
#include <inavr.h>

int main(){
    setup_LED_A()
    setup_timer();
    __enable_interrupts();

    while(1){
        ;
    }
}
```

# Timer options.h

```c
#ifndef TIMER_OPTIONS_H
#define TIMER_OPTIONS_H

#define DC 30U
#define FREQ 100U // in hz
#define SEL_TMR 3
#define N 8
#define FOSC 16000000UL

#define NORMAL 1
#define CTC 2
#define PWM 3
#define PCPWM 4 // phase correct
#define FPCPWM 5 // freq and phase correct

#define SEL_MODE PWM

//LEDA PIN IS: MB5INT output -- PE6
void setup_LED_A(){
    DDRE |= (1<<PE6);
}

#pragma vector TIMER3_COMPA_vect
__interrupt TIMER3_COMPA_ISR(void){
    PORTE &= ~(1<<PE6);
}

#pragma vector TIMER3_OVF_vect
__interrupt TIMER3_OVF_ISR(void){
    PORTE |= (1<<PE6);
    // e pornit, si la 30% se va stinge
}

void setup_timer_clk_and_prescaler(){
    switch(SEL_TMR){
        case 3:
        TCCR3B &= ~((1<<CS32) | (1<<CS31) | (1<<CS30));
        switch(N){
            case 0:
            break;
            case 1:
                TCCR3B |= (1<<CS30);
            break;
            case 8:
                TCCR3B |= (1<<CS31);
            break;
            case 64:
                TCCR3B |= ((1<<CS31) | (1<<CS30));
            break;
            case 256:
                TCCR3B |= (1<<CS32);
            break;
            case 1024:
                TCCR3B |= ((1<<CS32)|(1<<CS30));
            break;
        }
        break;

        default:
        break;
    }
}

void setup_timer(){

    switch(SEL_TMR){
        case 3:
        switch(SEL_MODE){

            case PWM: // timer 3 pwm
            {
                TCCR3B |= ((1<<WGM33) | (1<<WGM32));
                TCCR3A |= (1<<WGM31); // mode 14, TOP is ICR3
                uint32_t new_ICR = (FOSC/N)/FREQ - 1; // asta n-o sa mearca cu prescaler mai mic sau cu frecvente prea mici pentru ca da overflow -- grija!!
                ICR3 = (uint16_t)new_ICR;
                
                uint32_t new_OCRA = ((uint32_t)DC*(new_ICR+1))/100UL - 1;
                OCR3A = (uint16_t)new_OCRA;
                //output pe oc3a, deci PE3 deci MB4RST deci J3 26

                // TCCR3A |= (1<<COM3A1); // CLEAR on MATCH, SET on BOTTOM
                // this is replaced with interrupts so i can control led pins
                TIMSK3 |= (1<<OCIE3A); // when compare triggers i want to:
                // flip the current output pin value

                TIMSK3 |= (1<<TOIE3);// when compare triggers i want to:
                // set the output pin to 1 // turning it on

                setup_timer_clk_and_prescaler();
            }
            break;

            case PCPWM:
            {
                TCCR3B |= (1<<WGM33);
                TCCR3A |= (1<<WGM31);
                uint32_t new_ICR = (FOSC/(2UL*N))/FREQ;
                ICR3 = (uint16_t)new_ICR;
                
                uint32_t new_OCR = (30UL*ICR3)/100UL;
                OCR3A = (uint16_t)new_OCR;

                TIMSK3 |= (1<<OCIE3A);
                TIMSK3 |= (1<<TOIE3);

                setup_timer_clk_and_prescaler();
            }
            break;

            default:
            break;

        }
        break;

        default:
        break;
    }
}

#endif
```