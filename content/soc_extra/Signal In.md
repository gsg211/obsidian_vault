```c
#include <ioavr.h>
#include <inavr.h>

#define FOSC 16000000UL
#define N 8

volatile uint16_t ovfs;
volatile uint8_t state;

void setup_PE4_input();
void setup_INT4();
void setup_timer3();

void start_timer3();
void stop_timer3();


uint32_t timer3_freq;


// daca primesc semnal pe MB4 CS
// MB4CS = PE4
// PE4 = INT4

// folosesc timer3 pentru a masura
#pragma vector TIMER3_COMPA_vect
__interrupt TIMER3_COMPA_ISR(void){
    // nu e nevoie sa setez tcnt3 pe 0 pentru ca oricum urmeaza sa dea overflow si reset aici
    ovfs++;    
}

#pragma vector INT4_vect
__interrupt INT4_vect_ISR(void){
    uint8_t current_signal_edge = ((PINE & (1<<PE4)) !=0);
    if(current_signal_edge == 0 ){
        return;
    } // vreau peak to peak

    if(state == 0){
        start_timer3();
        state = 1;
    }
    else if(state == 1){
        stop_timer3();
        state = 2;
    }
}



int main(){
    setup_PE4_input();
    setup_timer3();
    setup_INT4();
    __enable_interrupt();

    while(1){
        if(state == 2){
            __disable_interrupt();
            uint32_t current_ticks = TCNT3;
            uint16_t current_OCR3A = OCR3A;
            uint32_t total_ticks = (ovfs*(current_OCR3A+1UL)) + current_ticks;
            uint32_t ticks_per_second = FOSC/N;

            uint32_t F_SIG_hz = ticks_per_second / total_ticks;

            TCNT3 = 0;
            ovfs = 0;
            state = 0;
            __enable_interrupt();
        }
    }
    return 0;
}




void setup_PE4_input(){
    DDRE &= ~(1<<PE4);
    PORTE |= (1<<PE4); // pullup 
}

void setup_INT4(){
    EIMSK |= (1<<INT4);
    EICRB |= (1<<ISC40); // any logical input generates an input req
}

void setup_timer3(){
    uint8_t SREG_cpy = SREG;
    __disable_interrupt();


    TCNT3 = 0;
    uint32_t new_OCR3A = FOSC/(1000UL*N) - 1;
    OCR3A = (uint16_t)(new_OCR3A); // compare match la fiecare msec

    timer3_freq = (FOSC / N) / (new_OCR3A + 1);


    // CTC mode 4 with TOP = OCR3A
    TCCR3B |= (1<<WGM32);

    // OVF overflow -- match cu OCR3A
    TIMSK3 |= (1<<OCIE3A);

    SREG = SREG_cpy;
}

void start_timer3(){
    uint8_t SREG_cpy = SREG;
    __disable_interrupt();

    // prescaler 8
    TCCR3B |= (1<<CS31);

    SREG = SREG_cpy;
}

void stop_timer3(){
    uint8_t SREG_cpy = SREG;
    __disable_interrupt();

    // prescaler 8 -- STOP
    TCCR3B &= ~((1<<CS32) | (1<<CS31) | (1<<CS30));

    SREG = SREG_cpy;
}


```
