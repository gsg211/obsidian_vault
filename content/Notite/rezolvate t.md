# 1

![[image.png]]
```c
#include <ioavr.h>
#include <inavr.h>

void setup_WD();
void disable_WD();

void setup_output_pin();
void toggle_output_pin();

__no_init uint8_t persistent_state;

int main(){
    MCUSR = 0; // reset the MCU status register
    disable_WD();
    setup_output_pin();

    if(persistent_state>1){
        persistent_state = 0;
    }

    if(persistent_state == 0){
        PORTE &=~ (1<<PE4);
    }
    else if(persistent_state == 1){
        PORTE |= (1<<PE4);
    }

    persistent_state = !persistent_state;

    

    setup_WD();
    __enable_interrupt();
    asm("WDR");

    while(1){
        ;
    }
    return 0;
}

void disable_WD(){
    WDTCSR |= ((1<<WDTCE) | (1<<WDE));
    WDTCSR &= ~(1<<WDE); // stops it
    asm("WDR");
}   

void setup_WD(){
    WDTCSR |= ((1<<WDTCE) | (1<<WDE));
    // i want system reset mode
    WDTCSR |= (1<<WDE);

}

void setup_output_pin(){
    // selected output pin is mb4cs -- PE4 -- OUTPUT
    DDRE |= (1<<PE4);
}


```

# 2


![[image 1.png]]

```c
#include <ioavr.h>
#include <inavr.h>
#include <string.h>
#include <stdio.h>

#define FOSC 16000000UL
#define N 8UL
#define STRLEN 256

#define BAUD 9600UL
#define QLEN 256

// i will use timer 3


uint8_t TXQ[QLEN];
volatile uint8_t tx_head;
volatile uint8_t tx_tail;

uint8_t RXQ[QLEN];
volatile uint8_t rx_head;
volatile uint8_t rx_tail;

void put_txbuf(uint8_t char_to_send, uint8_t isr_safe);
uint8_t get_txbuf();
void put_rxbuf(uint8_t char_to_send);
uint8_t get_rxbuf(uint8_t isr_safe);
uint8_t get_nxt_idx(uint8_t current_idx);

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe);

void setup_usart3();


#pragma vector USART3_RX_vect
__interrupt void USART3_RX_ISR(void){
    uint8_t char_received = UDR3;
    if(char_received!=0){
        put_rxbuf(char_received); 
    }
}

#pragma vector USART3_UDRE_vect
__interrupt void USART3_UDRE_ISR(void){
    uint8_t char_to_send = get_txbuf();
    if(char_to_send!=0){
        UDR3 = char_to_send;
    }
}

uint8_t string_to_send[STRLEN];

__no_init uint32_t ovfs;
__no_init uint16_t current_ticks;

void setup_WD();
void stop_WD();
void setup_timer3();

#pragma vector TIMER3_OVF_vect
__interrupt TIMER3_OVF_ISR(void){
    ovfs++;
}


int main(){
    uint8_t MCUSR_backup = MCUSR;
    uint8_t reset_by_wd = ( (MCUSR_backup & (1<<WDRF)) !=0);
    MCUSR = 0;
    stop_WD();

    setup_timer3();
    setup_usart3();
    __enable_interrupt();

    if(reset_by_wd && ( (ovfs > 0) || (current_ticks > 0)) ){
        // calculate and send freq
        uint32_t total_ticks = (ovfs*65536UL)+current_ticks;
        uint32_t ticks_per_second = FOSC/N;
        uint32_t freq_hz = ticks_per_second / total_ticks;

        uint8_t nr_khz = freq_hz / 1000;
        uint16_t nr_hz_radix = freq_hz & 1000;

        memset(string_to_send,0,STRLEN*sizeof(uint8_t));
        snprintf(string_to_send,STRLEN,"%u.%3lu KHZ\n",nr_khz,nr_hz_radix);
        uint8_t real_len = strlen(string_to_send);

        sendString(string_to_send,real_len,0);

    }

    setup_WD(); 

    TCNT3 = 0;
    ovfs = 0;
    current_ticks = 0;
    while(1){
        current_ticks = TCNT3;
    }
    return 0;
}

void setup_WD(){
    uint8_t SREG_cpy = SREG;
    __disable_interrupt();
    asm("WDR");
    WDTCSR |= ((1<<WDTCE) | (1<<WDE));
    WDTCSR = ((1<<WDE) | (1<<WDP0)); // enable system interrupt
    SREG = SREG_cpy;
}

void stop_WD(){
    uint8_t SREG_cpy = SREG;
    __disable_interrupt();
    asm("WDR");
    WDTCSR |= ((1<<WDTCE) | (1<<WDE));
    WDTCSR = 0;
    SREG = SREG_cpy;
}


void setup_timer3(){
    // timer 3 -- normal -- ovf interrupt
    TIMSK3 |= (1<<TOIE3);
    TCCR3B |= (1<<CS31); // prescaler 8 and clock select
}



// USART STUFF -----------------------------------------------------------------------------------------------------------------------------

void setup_usart3(){
    UCSR3B |= (1<<RXCIE3); // enable receive interrupt. udre interrupt will be only enabled on sending smth
    uint16_t baud_rate = (FOSC/16UL/BAUD) - 1;
    UBRR3H = (baud_rate >> 8) & (0x00FF);
    UBRR3L = (baud_rate & 0x00FF);

    UCSR3C |= ((1<<UCSZ31) | (1<<UCSZ30));
    
    UCSR3B |= ((1<<RXEN3) | (1<<TXEN3));
}

uint8_t get_nxt_idx(uint8_t current_idx){
    return (current_idx + 1) % QLEN;
}

// called by user
void put_txbuf(uint8_t char_to_send, uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }

    if(char_to_send == 0){
        if(isr_safe == 0){
            __enable_interrupt();
        }   
        return;
    }

    uint8_t next_tx_tail = get_nxt_idx(tx_tail);
    if(next_tx_tail == tx_head){
        // queue full
        if(isr_safe == 0){
            __enable_interrupt();
        }  
        return;
    }

    TXQ[tx_tail] = char_to_send;
    tx_tail = next_tx_tail;

    // enable sending intr
    UCSR3B |= (1<<UDRIE3);
    
    if(isr_safe == 0){
        __enable_interrupt();
    }
}

// called by intr
uint8_t get_txbuf(){
    if(tx_tail == tx_head){
        // q empty so the intr is stopped
        UCSR3B &= ~(1<<UDRIE3);
        return 0;
    }
    uint8_t current_char = TXQ[tx_head];
    uint8_t next_tx_head = get_nxt_idx(tx_head);
    tx_head = next_tx_head;
    return current_char;
}


// called by intr
void put_rxbuf(uint8_t char_to_send){
    if(char_to_send == 0){
        return;
    }

    uint8_t next_rx_tail = get_nxt_idx(rx_tail);
    if(next_rx_tail == rx_head){
        // queue full
        return;
    }

    RXQ[rx_tail] = char_to_send;
    rx_tail = next_rx_tail;
}


// called by user
uint8_t get_rxbuf(uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }
    
    if(rx_tail == rx_head){
        // q empty
        return 0;
    }
    uint8_t current_char = RXQ[rx_head];
    uint8_t next_rx_head = get_nxt_idx(rx_head);
    rx_head = next_rx_head;

    if(isr_safe == 0){
        __enable_interrupt();
    }

    return current_char;

}


void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe){
    if(string_to_send == NULL){
        return;
    }
    int i;
    for(i = 0;i<string_length;i++){
        put_txbuf(string_to_send[i], isr_safe);
    }
}

```


# 3.

![[image 2.png]]

```c
#include <ioavr.h>
#include <inavr.h>
#include <string.h>
#include <stdio.h>

// sa se calculeze si sa se trimita pe seriala crc16-ul zonei de memorie de tip program ocupata de functia:



#define FOSC 16000000UL
#define N 8UL

#define BAUD 9600UL
#define QLEN 256

uint8_t TXQ[QLEN];
volatile uint8_t tx_head;
volatile uint8_t tx_tail;

uint8_t RXQ[QLEN];
volatile uint8_t rx_head;
volatile uint8_t rx_tail;

void put_txbuf(uint8_t char_to_send, uint8_t isr_safe);
uint8_t get_txbuf();
void put_rxbuf(uint8_t char_to_send);
uint8_t get_rxbuf(uint8_t isr_safe);
uint8_t get_nxt_idx(uint8_t current_idx);

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe);

void setup_usart3();


#pragma vector USART3_RX_vect
__interrupt void USART3_RX_ISR(void){
    uint8_t char_received = UDR3;
    if(char_received!=0){
        put_rxbuf(char_received); 
    }
}

#pragma vector USART3_UDRE_vect
__interrupt void USART3_UDRE_ISR(void){
    uint8_t char_to_send = get_txbuf();
    if(char_to_send!=0){
        UDR3 = char_to_send;
    }
}



#define SLEN 256
uint8_t string_to_send[SLEN];

#define CRC16_MSBF 0x1021

__root
unsigned char sum(unsigned char x, unsigned char y){
    return (x+y);
}

__root
void sum_end(){
    asm("nop");
}


uint16_t crc16(uint16_t polinom16, uint16_t init_val_16, uint32_t adr_start, uint32_t len);

int main(){

    unsigned char const __huge * start = (unsigned char const __huge *)sum;
    unsigned char const __huge * end   = (unsigned char const __huge *)sum_end;

    uint32_t fct_sz = (uint32_t)(end-start);

    uint16_t calculated_crc16 = crc16(CRC16_MSBF, 0, (uint32_t)start, fct_sz);

    memset(string_to_send,0,SLEN*sizeof(uint8_t));
    setup_usart3();

    snprintf(string_to_send, SLEN, "<CRC16 ADR[0x%04luX - %04luX] %04X>" , (uint32_t)start, (uint32_t)end, calculated_crc16 );

    __enable_interrupt();

    sendString(string_to_send, strlen(string_to_send), 0);

    while(1){
        ;
    }
    return 0;
}

uint16_t crc16(uint16_t polinom16, uint16_t init_val_16, uint32_t addr_start, uint32_t len)
{
    uint16_t crc = init_val_16; // Inițializează CRC cu valoarea de start
    uint16_t data = 0;          // Variabilă temporară pentru octetul curent
    while (len--)
    {
    uint16_t i;
    data = *(unsigned char __huge *)addr_start;
        data <<= 8;  // Se aliniază octetul la MSB
        crc ^= data; // Se transferă și integrează datele în CRC
        for (i = 0; i < 8; ++i)
        {
            if (crc & 0x8000) // Se verifică dacă MSB este 1
                crc = (crc << 1) ^ polinom16;
            else
                crc = crc << 1;
        }
        addr_start++;
    }
    return crc;
}


// USART STUFF -----------------------------------------------------------------------------------------------------------------------------

void setup_usart3(){
    UCSR3B |= (1<<RXCIE3); // enable receive interrupt. udre interrupt will be only enabled on sending smth
    uint16_t baud_rate = (FOSC/16UL/BAUD) - 1;
    UBRR3H = (baud_rate >> 8) & (0x00FF);
    UBRR3L = (baud_rate & 0x00FF);

    UCSR3C |= ((1<<UCSZ31) | (1<<UCSZ30));
    
    UCSR3B |= ((1<<RXEN3) | (1<<TXEN3));
}

uint8_t get_nxt_idx(uint8_t current_idx){
    return (current_idx + 1) % QLEN;
}

// called by user
void put_txbuf(uint8_t char_to_send, uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }

    if(char_to_send == 0){
        if(isr_safe == 0){
            __enable_interrupt();
        }   
        return;
    }

    uint8_t next_tx_tail = get_nxt_idx(tx_tail);
    if(next_tx_tail == tx_head){
        // queue full
        if(isr_safe == 0){
            __enable_interrupt();
        }  
        return;
    }

    TXQ[tx_tail] = char_to_send;
    tx_tail = next_tx_tail;

    // enable sending intr
    UCSR3B |= (1<<UDRIE3);
    
    if(isr_safe == 0){
        __enable_interrupt();
    }
}

// called by intr
uint8_t get_txbuf(){
    if(tx_tail == tx_head){
        // q empty so the intr is stopped
        UCSR3B &= ~(1<<UDRIE3);
        return 0;
    }
    uint8_t current_char = TXQ[tx_head];
    uint8_t next_tx_head = get_nxt_idx(tx_head);
    tx_head = next_tx_head;
    return current_char;
}

// called by intr
void put_rxbuf(uint8_t char_to_send){
    if(char_to_send == 0){
        return;
    }

    uint8_t next_rx_tail = get_nxt_idx(rx_tail);
    if(next_rx_tail == rx_head){
        // queue full
        return;
    }

    RXQ[rx_tail] = char_to_send;
    rx_tail = next_rx_tail;
}

// called by user
uint8_t get_rxbuf(uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }
    
    if(rx_tail == rx_head){
        // q empty
        return 0;
    }
    uint8_t current_char = RXQ[rx_head];
    uint8_t next_rx_head = get_nxt_idx(rx_head);
    rx_head = next_rx_head;

    if(isr_safe == 0){
        __enable_interrupt();
    }

    return current_char;

}

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe){
    if(string_to_send == NULL){
        return;
    }
    int i;
    for(i = 0;i<string_length;i++){
        put_txbuf(string_to_send[i], isr_safe);
    }
}
```

# 4

![[image 3.png]]

```c
#include <ioavr.h>
#include <inavr.h>
#include <stdio.h>
#include <string.h>

#define FREQ 5000UL
#define FTRIGGER 1000UL // every msec

#define FOSC 16000000UL
#define N 8UL

#define BAUD 9600UL
#define QLEN 256

uint8_t TXQ[QLEN];
volatile uint8_t tx_head;
volatile uint8_t tx_tail;

uint8_t RXQ[QLEN];
volatile uint8_t rx_head;
volatile uint8_t rx_tail;

void put_txbuf(uint8_t char_to_send, uint8_t isr_safe);
uint8_t get_txbuf();
void put_rxbuf(uint8_t char_to_send);
uint8_t get_rxbuf(uint8_t isr_safe);
uint8_t get_nxt_idx(uint8_t current_idx);

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe);

void setup_usart3();


volatile uint8_t DC = 5;
volatile uint8_t inc_decn = 1;
uint8_t dc_step = 2;

volatile uint16_t msecs;
// semnal F = 5khz +-2%
// FU +=2% la fiecare 0.5 sec
// de la 5% la 90% si inapoi
// <FU = XX%>

// timer3 for freq gen
void setup_timer3_freq(); 
void start_timer3();
void stop_timer3();
void set_oc3a_with_dc(uint8_t dc);

// timer4 will trigger ovf every 0.5sec
void setup_timer4_dc_update();
void start_timer4();
void stop_timer4();

void setup_output_pin_pe3();
void inc_dc();



#pragma vector USART3_RX_vect
__interrupt void USART3_RX_ISR(void){
    uint8_t char_received = UDR3;
    if(char_received!=0){
        put_rxbuf(char_received); 
    }
}

#pragma vector USART3_UDRE_vect
__interrupt void USART3_UDRE_ISR(void){
    uint8_t char_to_send = get_txbuf();
    if(char_to_send!=0){
        UDR3 = char_to_send;
    }
}




#pragma vector TIMER4_OVF_vect
__interrupt TIMER4_OVF_ISR(void){
    msecs++;
    if(msecs == 500){
        // au trecut 0.5 sec
        msecs = 0;
        // stop_timer3();
        inc_dc();
        set_oc3a_with_dc(DC); // ar trebui sa fie safe ptc sunt fixate locurile in care chiar se foloseste noul OCR
        // start_timer3();
        uint8_t string_to_send[64];
        memset(string_to_send,0,64*sizeof(uint8_t));
        snprintf(string_to_send, 64, "<FU=%2u%%>", DC);
        sendString(string_to_send, strlen(string_to_send), 1);
    }
}

int main(){
    
    setup_timer3_freq();
    setup_timer4_dc_update();
    setup_usart3();
    setup_output_pin_pe3();


    __enable_interrupt();
    start_timer3();
    start_timer4();
    while(1){
        ;
    }
    return 0;
}

void setup_output_pin_pe3(){
    DDRE |= (1<<PE3);
}

void inc_dc(){
    if(inc_decn == 1){
        if(DC+dc_step <=90){
            DC+=dc_step;
        }
        else{
            DC-=dc_step;
            inc_decn = 0;
        }
    }
    else if(inc_decn == 0){
        if(DC-dc_step >= 5){
            DC-=dc_step;
        }
        else{
            DC+=dc_step;
            inc_decn = 1;
        }
    }
}

// TIMER STUFF -------------------------------------------------------------------------------------------------------------------

void setup_timer3_freq(){
    TCNT3 = 0;
    // pwm with ICR3 as top and OCR3A as compare -- output on mb4rst -- J3 -- 26
    // mode 14
    TCCR3B |= ((1<<WGM33) | (1<<WGM32));
    TCCR3A |= (1<<WGM31);

    // set ocr compare output mode on oc3a
    // clear on compare match, set at bottom
    TCCR3A |= (1<<COM3A1); 

    // setup TOP value:
    uint32_t newICR3 = (FOSC/N)/FREQ - 1UL;
    ICR3 = (uint16_t)newICR3;

    set_oc3a_with_dc(DC);
}

void start_timer3(){
    TCCR3B &= ~((1<<CS32) | (1<<CS31) | (1<<CS30));
    TCCR3B |= (1<<CS31); // prescaler 8
}

void stop_timer3(){
    TCCR3B &= ~((1<<CS32) | (1<<CS31) | (1<<CS30));
}

void set_oc3a_with_dc(uint8_t dc){
    // ICR*dc / 100

    uint32_t new_OCR3A = ((ICR3 + 1UL)*(uint32_t)dc) / 100;
    OCR3A = (uint16_t)new_OCR3A;
}




void setup_timer4_dc_update(){
    TCNT4 = 0;
    // timer 4 CTC with top = ICR4 //  mode 12
    // ovf interrupt --> increase DC4
    TCCR4B |= ( (1<<WGM43) | (1<<WGM42));


    uint32_t new_ICR4 = (FOSC/N)/FTRIGGER -1; // ideal 1000hz
    // trigger ovf si increase msecs

    TIMSK4 |= (1<<TOIE4);
}
void start_timer4(){
    TCCR4B &= ~((1<<CS42) | (1<<CS41) | (1<<CS40));
    TCCR4B |= (1<<CS41); // prescaler 8
}
void stop_timer4(){
    TCCR4B &= ~((1<<CS42) | (1<<CS41) | (1<<CS40));
}


// USART STUFF -----------------------------------------------------------------------------------------------------------------------------

void setup_usart3(){
    UCSR3B |= (1<<RXCIE3); // enable receive interrupt. udre interrupt will be only enabled on sending smth
    uint16_t baud_rate = (FOSC/16UL/BAUD) - 1;
    UBRR3H = (baud_rate >> 8) & (0x00FF);
    UBRR3L = (baud_rate & 0x00FF);

    UCSR3C |= ((1<<UCSZ31) | (1<<UCSZ30));
    
    UCSR3B |= ((1<<RXEN3) | (1<<TXEN3));
}

uint8_t get_nxt_idx(uint8_t current_idx){
    return (current_idx + 1) % QLEN;
}

// called by user
void put_txbuf(uint8_t char_to_send, uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }

    if(char_to_send == 0){
        if(isr_safe == 0){
            __enable_interrupt();
        }   
        return;
    }

    uint8_t next_tx_tail = get_nxt_idx(tx_tail);
    if(next_tx_tail == tx_head){
        // queue full
        if(isr_safe == 0){
            __enable_interrupt();
        }  
        return;
    }

    TXQ[tx_tail] = char_to_send;
    tx_tail = next_tx_tail;

    // enable sending intr
    UCSR3B |= (1<<UDRIE3);
    
    if(isr_safe == 0){
        __enable_interrupt();
    }
}

// called by intr
uint8_t get_txbuf(){
    if(tx_tail == tx_head){
        // q empty so the intr is stopped
        UCSR3B &= ~(1<<UDRIE3);
        return 0;
    }
    uint8_t current_char = TXQ[tx_head];
    uint8_t next_tx_head = get_nxt_idx(tx_head);
    tx_head = next_tx_head;
    return current_char;
}

// called by intr
void put_rxbuf(uint8_t char_to_send){
    if(char_to_send == 0){
        return;
    }

    uint8_t next_rx_tail = get_nxt_idx(rx_tail);
    if(next_rx_tail == rx_head){
        // queue full
        return;
    }

    RXQ[rx_tail] = char_to_send;
    rx_tail = next_rx_tail;
}

// called by user
uint8_t get_rxbuf(uint8_t isr_safe){
    if(isr_safe == 0){
        __disable_interrupt();
    }
    
    if(rx_tail == rx_head){
        // q empty
        return 0;
    }
    uint8_t current_char = RXQ[rx_head];
    uint8_t next_rx_head = get_nxt_idx(rx_head);
    rx_head = next_rx_head;

    if(isr_safe == 0){
        __enable_interrupt();
    }

    return current_char;

}

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe){
    if(string_to_send == NULL){
        return;
    }
    int i;
    for(i = 0;i<string_length;i++){
        put_txbuf(string_to_send[i], isr_safe);
    }
}

```