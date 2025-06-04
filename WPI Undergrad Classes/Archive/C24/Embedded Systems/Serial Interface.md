UART
- basic two-wire communication
- asyncronous: does not have clock syncronicoty 

Serial Peripheral Interface Bus (SPI)
![[Screenshot 2024-02-13 at 9.04.00 AM.png]]
SIMO: Slave in master out
SOMI: slave out master in
SCLK: clock sycronization from master
CS: Chip Select, enables communication between CPU and peripheral

### Chip Select
- What is its purpose?
- All peripheral serials are connected to the CPU through the same line. CS determines whether the peripheral will pay attention

![[Screenshot 2024-02-13 at 9.11.20 AM.png]]

# SPI on MSP430

Controlling the LCD screen
SPI  CS: P6.6 (I/O)
SPI CLK: P3.2 (functional mode)(USCIB0)
SPI SIMO: P3.0 (functional mode)(USCIB0)
- we dont use SOMI since the LCD doesn't need to send info to CPU
LCD Power: P6.5 (I/O)

LCD Config Code
```c
//***************************************************************  
// HAL_MSP-EXP430F5529_Sharp96x96.h - Prototypes for the  
// Sharp96x96 LCD display driver. There is no output from  
// Sharp96x96 LCD.  
// ***************************************************************  
//// Pin definitions for LCD driver  
//***************************************************************  
/*  
* Power P6.5 // Digital IO  
* DISP P1.6 // Digital IO  
* CS P6.6 // Digital IO  
* MOSI P3.0 // Function mode USCI B0  
* SCLK P3.2 // Function mode USCI B0  
*/  
#define PORT_SPI_SEL P3SEL  
#define PORT_SPI_DIR P3DIR  
#define PORT_DISP_SEL P1SEL  
#define PORT_DISP_DIR P1DIR  
#define PORT_DISP_OUT P1OUT  
#define PORT_CS_SEL P6SEL  
#define PORT_CS_DIR P6DIR  
#define PORT_CS_OUT P6OUT  
#define PORT_PWR_SEL P6SEL  
#define PORT_PWR_DIR P6DIR  
#define PORT_PWR_OUT P6OUT  
#define PIN_SPI_MOSI BIT0  
#define PIN_SPI_SCLK BIT2  
#define PIN_PWR BIT5  
#define PIN_DISP BIT6  
#define PIN_CS BIT6  
#define SPI_REG_CTL0 UCB0CTL0  
#define SPI_REG_CTL1 UCB0CTL1  
#define SPI_REG_BRL UCB0BR0  
#define SPI_REG_BRH UCB0BR1  
#define SPI_REG_IFG UCB0IFG  
#define SPI_REG_STAT UCB0STAT  
#define SPI_REG_TXBUF UCB0TXBUF  
#define SPI_REG_RXBUF UCB0RXBUF  
#define SPI_CLK_SRC (UCSSEL__SMCLK)  
#define SPI_CLK_TICKS 0

void Sharp96x96_Init(void){  
// Configure SCLK and MOSI for peripheral function mode = 1  
PORT_SPI_SEL |= (PIN_SPI_MOSI|PIN_SPI_SCLK);  
// LCD is powered using a GPIO pin, configure it as output  
PORT_PWR_SEL &= ~PIN_PWR;  
PORT_PWR_DIR |= PIN_PWR;  
// Drive the power pin high to power the LCD  
PORT_PWR_OUT |= PIN_PWR;  
// Configure the LCD display enable signal DISP,  
// Drive it high to enable the LCD  
PORT_DISP_SEL &= ~PIN_DISP;  
PORT_DISP_DIR |= PIN_DISP;  
PORT_DISP_OUT |= PIN_DISP;  
// Configure the display Chip Select as an output  
PORT_CS_SEL &= ~PIN_CS;  
PORT_CS_DIR |= PIN_CS;  
// Initialize the chip select in a deasserted state  
PORT_CS_OUT &= ~PIN_CS;  
// Now configure UCSIB0 to function as our SPI controller  
// Disable the module before we can configure it  
SPI_REG_CTL1 |= UCSWRST;  
// Reset the controller config parameters  
SPI_REG_CTL0 &= ~(UCCKPH|UCCKPL|UC7BIT|UCMSB); /  
SPI_REG_CTL1 &= ~UCSSEL_3; // Reset the clock configuration  
// Select SMCLK for our clock source  
SPI_REG_CTL1 |= SPI_CLK_SRC;  
// Set SPI clock frequency (which is the same frequency as  
// SMCLK so this can apparently be 0)  
// Set the low byte then set the high byte  
SPI_REG_BRL = ((uint16_t)SPI_CLK_TICKS) & 0xFF;  
SPI_REG_BRH = (((uint16_t)SPI_CLK_TICKS) >> 8) & 0xFF;  
// Configure for SPI master, synchronous, 3 wire SPI,  
// MSB first, capture data on first edge,  
// and inactive low polarity  
SPI_REG_CTL0 |= (UCMST|UCSYNC|UCMODE_0|UCMSB|UCCKPH);  
// Re-enable the module s0 it can be used  
SPI_REG_CTL1 &= ~UCSWRST;  
SPI_REG_IFG &= ~UCRXIFG; } // clear interrupt flag
```

LCD Sending and Recieving Code
```c
write_spi(unsigned char ucCmdData)  
{  
	// Put byte to send in transmit TX buffer  
	SPI_REG_TXBUF =ucCmdData;  
	/* Wait for the TX buffer to empty */  
	while(!(SPI_REG_IFG & UCTXIFG))  
		__no_operation();
}
```
- it will take a long time for data to completely transfer, so we need to wait
