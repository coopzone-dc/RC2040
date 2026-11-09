# GPIO pin definitions 
### and where to change them 

The RC2040 was never intended to be board agnostic, but people have asked so....

## Where the GPIO pins are defined. 
Yes this should all be in one place....

## SD Card
The SD card uses the pico_fatfs library on SPI0. The pins are defined near the top of RC2040.c

#define SD_PIN_MISO 16
#define SD_PIN_CS   17
#define SD_PIN_SCK  18
#define SD_PIN_MOSI 19

and are passed to pico_fatfs_set_config() just before f_mount().
MISO, SCK and MOSI must be valid pins for the SPI module used (spi0 here),
CS can be any GPIO. If the pins are not valid for the SPI module the library
silently falls back to PIO SPI.

## Sound
The speaker pins, have two defines near the top of RC2040.c 
#define soundIO1 15
#define soundIO2 14

These must be on the same PWMslice

## 8 bit Port
The pins for the 8 bit port are defined in an array near the top of RC2040.c

uint8_t PIOAp[]={2,3,4,5,20,21,26,27};

(GPIO16-19 are used by the SD card.)

## Serial
near the top of the RC2040.c
/* Real UART setup*/

#define UART_TX_PIN 0
#define UART_RX_PIN 1

these ofcourse need to be valid Tx/Rx pins to work. and 
#define UART_ID uart0

needs to point to the serial module used.

