```c
// enter a character in usart -- wdr
// on reset, sent "RST" through usart
// reset every 4 ish seconds
// on WD interrupt send "\nWARNING\n"


#include <inavr>
#include <ioavr>
#include <string.h>

#define FOSC 16000000UL
#define BAUD 9600UL
#define QLEN 256

uint8_t TXQ[QLEN];
volatile uint8_t tx_head;
volatile uint8_t tx_tail;

uint8_t RXQ[QLEN];
volatile uint8_t rx_head;
volatile uint8_t rx_tail;



void setup_usart3();
void setup_WD();
void disable_WD();

void put_txbuf(uint8_t char_to_send, uint8_t isr_safe);
uint8_t get_txbuf();
void put_rxbuf(uint8_t char_to_send);
uint8_t get_rxbuf(uint8_t isr_safe);
uint8_t get_nxt_idx(uint8_t current_idx);

void sendString(uint8_t string_to_send[], uint8_t string_length, uint8_t isr_safe);


#pragma vector WDT_vect
__interrupt void WDT_ISR(void){
    // se consuma bitul de WDIE

    uint8_t string_to_send[] = "\nWARNING\n";
    sendString(string_to_send, strlen(string_to_send), 1);
    // se vor trimite dupa ce se iese idn isr-ul asta si se pune inapoi bitul de global interrupt enable

}

#pragma vector USART3_RX_vect
__interrupt void USART3_RX_ISR(void){
    uint8_t char_received = UDR3;
    
    asm("WDR"); // reset on any received char
    uint8_t string_to_send[] = "\nWD RESTART\n";
    sendString(string_to_send, strlen(string_to_send), 1);
    
    if(char_received!=0){
        put_rxbuf(char_received); //  the buffer won't really be read
    }
}

#pragma vector USART3_UDRE_vect
__interrupt void USART3_UDRE_ISR(void){
    uint8_t char_to_send = get_txbuf();
    if(char_to_send!=0){
        UDR3 = char_to_send;
    }
}

int main(){
    uint8_t MCUSR_backup = MCUSR;
    MCUSR = 0;
    uint8_t resetByWD = (MCUSR_backup & (1<<WDRF)) !=0;

    disable_WD();

    
    setup_usart3();
    __enable_interrupt();

    if(resetByWD == 1){
        uint8_t string_to_send[] = "\nWatchdog reset!!\n";
        sendString(string_to_send, strlen(string_to_send), 0);
    
    }

    
    setup_WD();

    while(1){
        ;
    }
    return 0;
}





// WD STUFF -----------------------------------------------------------------------------------------------------------------------------

void setup_WD(){
    __disable_interrupt();

    WDTCSR |= ((1<<WDTCE) | (1<<WDE)); // latch
    //prescaler 512k cycles, 4.0sec +- 30%
    WDTCSR |= ((1<<WDE) | (1<<WDIE) | (1<<WDP3)); // enable interrupt and then system reset

}   

void disable_WD(){
    uint8_t sreg_backup = SREG;
    __disable_interrupt();

    asm("WDR");

    WDTCSR = ((1<<WDTCE) | (1<<WDE));
    WDTCSR = 0;

    SREG = sreg_backup;
}






// USART STUFF -----------------------------------------------------------------------------------------------------------------------------

void setup_usart3(){
    UCSR3B |= (1<<RXCIE3); // enable receive interrupt. udre interrupt will be only enabled on sending smth
    uint16_t baud_rate = (FOSC>>4)/BAUD - 1UL;
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