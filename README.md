# AVR Demo

Let's NOT use the Arduino IDE. 

---

## Introduction

In this project, we are using the Arduino Uno as our board. We will use `avr-gcc` to build (first without a Makefile) and `avrdude` to *flash* our board. 

These instructions will be written from the standpoint of a Linux user first, but most things can be done the same way in MacOS  using `brew`. 

## Install Toolchain

### Linux

```bash
sudo apt-get update
sudo apt-get upgrade all
sudo apt-get install gcc-avr binutils-avr avr-libc
sudo apt-get install gdb-avr
sudo apt-get install avrdude

```

### MacOS

MacOS users please see [Crosspack](https://www.obdev.at/products/crosspack/index.html)

If you don't already have `brew`, install it:

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Install avr-libc and avrdude:

```bash
brew install avr-libc
brew install avrdude --with-usb
```

## Test Build

blink.c

```C
#include <avr/io.h>
#include <util/delay.h>

#define MS_DELAY 3000

int main (void) {
    /*Set to one the fifth bit of DDRB to one
    **Set digital pin 13 to output mode */
    DDRB |= _BV(DDB5);

    while(1) {
        /*Set to one the fifth bit of PORTB to one
        **Set to HIGH the pin 13 */
        PORTB |= _BV(PORTB5);

        /*Wait 3000 ms */
        _delay_ms(MS_DELAY);

        /*Set to zero the fifth bit of PORTB
        **Set to LOW the pin 13 */
        PORTB &= ~_BV(PORTB5);

        /*Wait 3000 ms */
        _delay_ms(MS_DELAY);
    }
}
```

### Create object file:

```bash
avr-gcc -Os -DF_CPU=16000000UL -mmcu=atmega328p -c -o blink_led.o blink_led.c
```

### Create executable:

```bash
avr-gcc -mmcu=atmega328p blink_led.o -o blink_led
```

### Convert executable to binary:

```bash
avr-objcopy -O ihex -R .eeprom blink_led blink_led.hex
```

### What devices are connected to my coms?

*In my case, it was ``` /dev/ttyACM0 ```*

```
dmesg | grep tty
```

### Upload binary to the board:

```bash
avrdude -F -V -c arduino -p ATMEGA328P -P /dev/ttyACM0 -b 115200 -U flash:w:blink_led.hex
```

----

## AVR Serial Communication Demo

### Use A Makefile To Build:

*Problem with build? ``` make clean ``` .*

```bash
make all
```

### Upload Serial Communication Demo to Uno:

```bash
avrdude -p atmega328p -P /dev/ttyACM0 -c arduino -b 115200 -U flash:w:main.hex
```

### Connect To TTY With Picocom:

```
 sudo picocom /dev/ttyACM0 -b115200
```

## Summary

You should see your TX and RX LEDs light up when you type into the picocom terminal emulator. We've demonstrated *flashing* the Uno with `avrdude`, and building with and without a `Makefile.` 

### Further Reading

https://www.engineersgarage.com/avr-microcontroller/serial-communication-data-receive-using-avr-microcontroller-atmega16-usart-part-12-46/

https://sites.google.com/site/qeewiki/books/avr-guide/usart