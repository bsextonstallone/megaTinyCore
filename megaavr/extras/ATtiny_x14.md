# ATtiny 214/414/814/1614
![x14 Pin Mapping](ATtiny_x14.gif "Arduino Pin Mapping for ATtiny x14")

 Specifications |  ATtiny214 |  ATtiny414 |  ATtiny814 |  ATtiny1614
----------------|------------|------------|------------|--------------
Flash           | 2048 bytes | 4096 bytes | 8192 bytes | 16384 bytes
Flash w/Optiboot| 1536 bytes | 3584 bytes | 7680 bytes | 15872 bytes
RAM             |  128 bytes |  256 bytes |  512 bytes |  2048 bytes
EEPROM          |   64 bytes |  128 bytes |  128 bytes |   256 bytes
Bootloader (optional) | Optiboot (absolutely not recommended) | Optiboot (not recommended)| Optiboot (awkard but viable, not recommended) | Optiboot (awkard but viable, not recommended)
GPIO Pins       |  11 usable |  11 usable |  11 usable |   11 usable
ADC Channels    |   9 usable |   9 usable |   9 usable |    9 usable
DAC             |        Yes |        Yes |        Yes |         Yes
PWM Channels *  |          6 |          6 |          6 |           6
Timer Type B ** |          1 |          1 |          1 |           2
Timer Type D    |        Yes |        Yes |        Yes |         Yes
CCL Logic Blocks| 2 (no int) | 2 (no int) | 2 (no int) |  2 (no int)
Event Channels  | 6, 2 sync<br/> 4 async | 6, 2 sync<br/> 4 async | 6, 2 sync<br/> 4 async | 6, 2 sync<br/> 4 async |
Interfaces | UART, SPI, I2C | UART, SPI, I2C | UART, SPI, I2C | UART, SPI, I2C

The type D timer is not used for PWM, but is the default millis timekeeping source, since it is the hardest to reconfigure and thus the least likely to be wanted for other purposes in a projexct,

Users who need to lean heavily on event channels or the CCL are advised to use the 2-series parts instead. The CCL here has a different set of options, the D-type latch doesn't work,  and the event system is the wacky 0/1-series version with two kinds of channels and a markedly inferiordistribution of generator options.

## Clock Options
These parts do not support an external HF crystal, only an external clock, and/or a watch crystak for the RTC.
 MHz | Source          | Notes
 ----|-----------------|-------
  20 | Internal        |
  16 | Internal        |
  10 | Internal        |
   8 | Internal        |
   5 | Internal        |
   4 | Internal        |
   1 | Internal        |
  20 | Internal, tuned |
  16 | Internal, tuned |
  12 | Internal, tuned |
  20 | External Clock  | External clock goes to CLKI (PA3). Minimize any load on this pin, including even short wires. HF stuff is very picky.
  16 | External Clock  | As above.
  10 | External Clock  | As above.
   8 | External Clock  | As above.
  24 | Internal, tuned | OVERCLOCKED, usually fine @ 5v and room temperature.
  25 | Internal, tuned | OVERCLOCKED, usually fine @ 5v and room temperature.
  30 | Internal, tuned | OVERCLOCKED, may be unstable.
  24 | External Clock  | OVERCLOCKED, usually fine @ 5v and room temperature. Uses CLKI/PA3 as above.
  25 | External Clock  | OVERCLOCKED, usually fine @ 5v and room temperature. Uses CLKI/PA3 as above.
  30 | External Clock  | OVERCLOCKED, may be unstable. Uses CLKI/PA3 as above.
  32 | External Clock  | OVERCLOCKED, may be unstable. Uses CLKI/PA3 as above.
When external clock is used as system clock source, it cannot be used for any other purpose (obviously) - all control over that pin is taken by CLKCTRL.

`*` The overclocked options at 24/25 MHz have been found to generally work around room temperature when running at 5v. The faster ones - while they can be stable with solid 5v supply at room temperature, this is right on the edge of what these parts can do. I have specimens that will run at 5.0v but not at 4.8, for example, meaning that it would work powered by some USB ports, but not others (they range from 4.7 to 5.3v) and they are of course extremely sensitive to noise on power rails. External oscillators work more reliably than the internal one when overclocking, but they generally cost about as much as the microcontroller itself and are gross overkill (in terms of accuracy) for what most arduino folks want from them.

The tuned options are new in 2.4.0 - see the [tuned internal oscillator guide](Ref_Tuning.md) for more information before using these options.

## External crystal
These parts support an external 32.768 kHz crystal clock source for the RTC. Conveniently, the pins for this are... PB2 and PB3, the same pins used by default for the USART. These parts expect a crystal with a very low load capacitance, on the order of 6pf. A pair of loading capacitors of a few pF each is necessary for it to oscillate, and traces between chip and crystal must be kept very short. There is rarely a need to use it. At room temperature and 3-5V, the internal oscillator and oscillator error stored in the SIGROW can be used to calculate the clock speed of the (inaccurate, but stable) 32 kHz internal RTC oscillator accurately enough to use it to tune the chip as described above, a sketch for this will be provided with a future version of this core.

## The issue with bootloader
There's no dedicated reset pin. So there is no way to do the traditional autoreset circuit to reset the chip to upload with a bootloader unless you disable UPDI (requiring HV UPDI to undo - I've got a half dozen boards that are bricked until I have time to get an hvupdi programming setup together to resurrect them). Either you manually power cycle it just prior to trying to upload, or you have some sort of ersatz-reset solution coupled to an autoreset circuit, or handle it in some other bespoke way. Regardless of the approach, short of disabling UPDI, none of them are as convenient a development cycle as we're used to. In most cases, the most convenient development configuration is to simply use UPDI programming, and leave any serial connection open while programming via UPDI using a programmer on a different port. Note that the 2-series 20 and 24 pin parts have an alernate reset pin feature which makes a better developer experience possible with a bootloader.

On parts with less than 8k, in addition to the woes surrounding bootloader entry, the 512b of flash the bootloader requires is no longer negligible.

## Buy official megaTinyCore breakouts and support continued development
[ATtiny1614 assembled](https://www.tindie.com/products/17598/)

[ATtiny3224/1624/824/424/1614/814/414/214/1604/804/404/204 bare board](https://www.tindie.com/products/17748/)

## Notes on Tables
`*` PWM channels exposed with default configuration via analogWrite(); TCA0 is in split mode, TCD is used for millis by default (can change with tools menu) but is not used for PWM (it shares pins with TCA0) and the type B timer(s) are not used for PWM.
`**` The type B timers on the 0/1-series lack the CASCADE (32-bit input capture) and count-on-event option of later parts.

## Datasheets and Errata
See [Datasheet Listing](Datasheets.md)
