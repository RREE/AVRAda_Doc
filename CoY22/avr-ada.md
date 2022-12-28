Title: [2022][AVR-Ada] Compiler Environment for Building Ada Programs on AVR Microcontrollers
# AVR-Ada
![badge](https://img.shields.io/endpoint?url=https://alire.ada.dev/badges/avrada_rts.json) ![badge](https://img.shields.io/endpoint?url=https://alire.ada.dev/badges/avrada_mcu.json) ![badge](https://img.shields.io/endpoint?url=https://alire.ada.dev/badges/avrada_lib.json) ![badge](https://img.shields.io/endpoint?url=https://alire.ada.dev/badges/avrada_examples.json)



## History

The development for AVR-Ada started 2004 (or even before, I don't remember). I read somewhere around that time that the new AVR processors have many registers (32) to support high level languages.  And then the article continued to use C. Having used Ada in the 1990ies I considered Ada a much more appropriate choice for a high level language.

I plunged into building gcc as a cross compiler and activating the Ada frontend. I learned a lot about gcc's internals and GNAT's run-time-system. I started to write scripts for building the compiler and the binutils (linker, assembler, and other tools), developed a minimalistic run-time-system, and a support library for the on-chip or on-board peripherals. Providing all the different parts to other users was always the most tedious part.

AVR-Ada was once part of the popular WinAVR distribution in 2009. That was the most user friendly installation. It was limited to Windows only but everything was included and it was nicely integrated in the development environment of that time AVR-Studio. Before and after that the installation was quite awkward.

AdaCore also distributed an AVR cross-compiler in 2011 and 2012.  There were only pin and register definitions for a single target and no driver library at all. The compiler itself 

## Purpose

AVR-Ada strives to provide a more or less complete compilation environment targeting the 8-bit AVR micro-controllers. It is **not** a development environment like Emacs, GNAT-Studio, AVR-Studio or the newer VS Code but integrates well into each of them.

More specifically the project provides  
- a GNAT compiler based on the existing AVR and Ada support in gcc  
- a minimalistic Ada runtime system  
- register and pin definitions for many (almost 100) AVR micro-controllers
- an extensiv and useful AVR specific support library  
- support packages for accessing LCDs, Dallas' 1-wire sensors, or several humidity and temperature sensors.
- some sample programs that show how to use the compiler and the library.

Some applications that had been built using AVR-Ada:  
- a data logger for a weather station  
- a closed loop heating control system  
- an astronomical "GoTo" mount for a telescope on an AVR90USB128  
- a small robot based on the Asuro platform  
- a limited IP stack with ARP, ICMP and UDP (no TCP)  
- sample programs for the very popular Arduino platform
 

## Usage

The simplest things to get you started are an Arduino board and a USB cable to connect it to your computer.

The typical "Hello World" program is a blinking LED in the embedded world.  The standard Arduino boards already have a LED between B5 (pin 5 of port B, labeled "D13" ) and ground.  You have to set the pin to high level to switch it on.

#### Check out the Examples

The easiest way to get you going is to get the examples.  They will install the necessary compiler and environment with the help of Alire. 

So, first get the examples: 
```
alr get avrada_examples
```
step into the crate and then go into `delays`.  You now have to tell Alire about the dependencies. 

####  Install the Compiler Environment

That is done issuing the command 
```
alr update
```
It proposes to install 

- the cross compiler (`gnat_avr_elf`), 
- the compiler's minimal run time system (`avrada_rts`), 
- the register definitions of the various supported AVR processors (`avrada_mcu`),
- and a library of drivers for the on-board peripherals like timers and useful functions like string manipulation (`avrada_lib`)

That already lists the four building blocks of AVR-Ada (see below). Simply hit the Enter key to confirm the choice and Alire will install the mentioned toolchain and the other crates.

#### Compilation 

Simply run `alr build` or `make`. Alire and the GNAT project manager will compile, bind and link all the necessary files. It will show some warnings in `avr-real_time.adb` ('warning: unrecognized pragma "NOT_IMPLEMENTED"') as some procedures are not yet implemented. In the end you have built three programs `blink_busy.elf`, `blink_clock.elf`, and `blink_rel.elf`.

#### Upload
After setting the correct port for the Arduino board in the `Makefile` (line 67, set `AVRDUDE_PORT` to `com1:`/`/dev/ttyUSB0` or whatever the OS choose) you can upload your program to the Arduino by
```
make blink_busy.prog
```




## Design

`Tell us what how your crate is designed, what implementation choices you made.`

Originally all in one, crates separated into compiler / toolchain plus three support crates (RTS, MCU, Lib)

### Code Re-Use Across Different Micro-Processors
- Use of `avr-mcu.ads`
- Preprocessor, `mcu_capabilities.gpr`, and `avrada_lib.gpr`

### Alire configuration variables

### Higher Level Abstraction in Ada Code

### Use of `Makefile`
Despite the rich possibilities of the GNAT project manager (i.e. `gpr`-files) and now Alire in `alire.toml` there are still quite some recurring functionalities missing. Since the first days we therefor provide a `Makefile` with appropriate targets. 

If your program is called `sample` you can call the following commands:
- `make sample.prog`: that is probably the most important one, uploading the resulting binary to the target after generating appropriate ihex and eep files for flash and EEPROM.
- `make sample.size`: show the absolute and relative use of flash and eeprom memory.
- `make sample.s`: generate a commented AVR assembler file from `sample.adb`.  That proves to be very helpful for debugging or when you suspect problems in the pin definitions or in the driver code
- `make`: the standard make target simply calls `alr build`to rebuild your application

Most tasks of the Makefile can be delegated to Alire. Some commands, however, require the name of the target processor. A simple AWK one-liner extracts that information from the configuration in `alire.toml`.


## Authors

Some contributions in the time frame 2010 - 2013 from Warren Gay and Tero Koskinen

## License

GPL-2.0-or-later WITH GCC-exception-3.1
