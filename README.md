Circle
======

> If you read this file in an editor you should switch line wrapping on.

Overview
--------

Circle is a C++ bare metal programming environment for the Raspberry Pi. It should be useable on all existing models (tested on model A+, B, B+, on Raspberry Pi 2 and 3 and on Raspberry Pi Zero). It provides several ready-tested C++ classes which can be used to control different hardware features of the Raspberry Pi. Together with Circle there are delivered some samples which demonstrate the use of its classes. Circle can be used to create 32-bit or 64-bit bare metal applications.

Circle includes bigger (optional) third-party C-libraries for specific purposes in addon/ now. This is the reason why GitHub rates the project as a C-language-project. The main Circle libraries are written in C++ using classes instead. That's why it is named a C++ programming environment.

The 38th Step
-------------

The entire Circle project has been ported to the AArch64 architecture. Please see the *AArch64* section below for details! The 32-bit support is still available and will be maintained and developed further.

Moreover a driver for the BMP180 digital pressure sensor has been added to *addon/sensor/*.

Circle creates a short beautified build log now.

The options to be used for *cmdline.txt* are described in *doc/cmdline.txt*.

Features
--------

Circle supports the following features:

| Group                 | Features                                            |
|-----------------------|-----------------------------------------------------|
| C++ build environment | AArch32 and AArch64 support                         |
|                       | Basic library functions (e.g. new and delete)       |
|                       | Enables all CPU caches using the MMU                |
|                       | Interrupt support (IRQ and FIQ)                     |
|                       | Multi-core support (Raspberry Pi 2 and 3)           |
|                       | Cooperative non-preemtive scheduler                 |
|                       | CPU clock rate management                           |
|                       |                                                     |
| Debug support         | Kernel logging to screen, UART and/or syslog server |
|                       | C-assertions with stack trace                       |
|                       | Hardware exception handler with stack trace         |
|                       | GDB support using rpi_stub (Raspberry Pi 2 and 3)   |
|                       | Serial bootloader (by David Welch) included         |
|                       | QEMU support (tested with AArch32 only)             |
|                       |                                                     |
| Legacy devices        | GPIO pins (with interrupt, Act LED) and clocks      |
|                       | Frame buffer (screen driver with escape sequences)  |
|                       | UART (Polling and interrupt driver)                 |
|                       | System timer (with kernel timers)                   |
|                       | Platform DMA controller                             |
|                       | EMMC SD card interface driver                       |
|                       | PWM output (2 channels)                             |
|                       | PWM sound output (on headphone jack)                |
|                       | I2C master and slave                                |
|                       | SPI0 master (Polling and DMA driver)                |
|                       | SPI1 auxiliary master (Polling)                     |
|                       | I2S sound output                                    |
|                       | Hardware random number generator                    |
|                       | Official Raspberry Pi touch screen                  |
|                       | VCHIQ interface and audio service drivers           |
|                       |                                                     |
| USB                   | Host controller interface (HCI) driver              |
|                       | Standard hub driver                                 |
|                       | HID class device drivers (keyboard, mouse, gamepad) |
|                       | Driver for on-board Ethernet device (SMSC951x)      |
|                       | Driver for on-board Ethernet device (LAN7800)       |
|                       | Driver for USB mass storage devices (bulk only)     |
|                       | Audio class MIDI input support                      |
|                       | Printer driver                                      |
|                       |                                                     |
| File systems          | Internal FAT driver (reduced function)              |
|                       | FatFs driver (full function, by ChaN)               |
|                       |                                                     |
| TCP/IP networking     | Protocols: ARP, IP, ICMP, UDP, TCP                  |
|                       | Clients: DHCP, DNS, NTP, HTTP, Syslog, MQTT         |
|                       | Servers: HTTP, TFTP                                 |
|                       | BSD-like C++ socket API                             |
|                       |                                                     |
| GUI                   | uGUI (by Achim Doebler)                             |
|                       |                                                     |
| Bluetooth             | Device inquiry support only                         |
|                       | USB BR/EDR dongle driver                            |
|                       | Internal controller of Raspberry Pi 3 B             |

Building
--------

> For building 64-bit applications (AArch64) see the next section.

Building is normally done on PC Linux. If building for the Raspberry Pi 1 you need a [toolchain](http://elinux.org/Rpi_Software#ARM) for the ARM1176JZF core (with EABI support). For Raspberry Pi 2/3 you need a toolchain with Cortex-A7/-A53 support. A toolchain, which works for all of these, can be downloaded [here](https://developer.arm.com/open-source/gnu-toolchain/gnu-rm/downloads). Circle has been tested with the version *7-2018-q2-update* from this website.

First edit the file *Rules.mk* and set the Raspberry Pi version (*RASPPI*, 1, 2 or 3) and the *PREFIX* of your toolchain commands. Alternatively you can create a *Config.mk* file (which is ignored by git) and set the Raspberry Pi version and the *PREFIX* variable to the prefix of your compiler like this (don't forget the dash at the end):

`RASPPI = 1`  
`PREFIX = arm-none-eabi-`

The following table gives support for selecting the right *RASPPI* value:

| RASPPI | Target         | Models                   | Optimized for |
| ------ | -------------- | ------------------------ | ------------- |
|      1 | kernel.img     | A, B, A+, B+, Zero, (CM) | ARM1176JZF-S  |
|      2 | kernel7.img    | 2, 3, (CM3)              | ARMv7-A       |
|      3 | kernel8-32.img | 3, (CM3)                 | Cortex-A53    |

For a binary distribution you should do one build with *RASPPI = 1* and one with *RASPPI = 2* and include the created files *kernel.img* and *kernel7.img*. Optionally you can do a build with *RASPPI = 3* and add the created file *kernel8-32.img* to provide an optimized version for the Raspberry Pi 3.

Then go to the build root of Circle and do:

`./makeall clean`  
`./makeall`

By default only the latest sample (with the highest number) is build. The ready build *kernel.img* file should be in its subdirectory of sample/. If you want to build another sample after `makeall` go to its subdirectory and do `make`.

You can also build Circle on the Raspberry Pi itself on Raspbian but you need some method to put the *kernel.img* file onto the SD(HC) card. With an external USB card reader on model B+ or Raspberry Pi 2/3 model B (4 USB ports) this should be no problem.

Building Circle from a non-Linux host is possible too. Maybe you have to adapt the shell scripts in this case. You need a cross compiler targetting (for example) *arm-none-eabi*. OSDev.org has an [excellent document on the subject](http://wiki.osdev.org/GCC_Cross-Compiler) that you can follow if you have no idea of what a cross compiler is, or how to make one.

AArch64
-------

Circle supports building 64-bit applications, which can be run on the Raspberry Pi 3. There are also Raspberry Pi 2 versions, which are based on the BCM2837 SoC. These Raspberry Pi versions can be used too.

The recommended toolchain to build 64-bit applications with Circle can be downloaded [here](https://releases.linaro.org/components/toolchain/binaries/latest-7/aarch64-elf/). It is based on GCC 7.3.1 at the moment.

First edit the file *Rules.mk* and set the Raspberry Pi architecture (*AARCH*, 32 or 64) and the *PREFIX64* of your toolchain commands. The *RASPPI* variable is set automatically to 3 for `AARCH = 64` and does not need to be set here. Alternatively you can create a *Config.mk* file (which is ignored by git) and set the Raspberry Pi architecture and the *PREFIX64* variable to the prefix of your compiler like this (don't forget the dash at the end):

```
AARCH = 64
PREFIX64 = aarch64-elf-
```

Then go to the build root of Circle and do:

```
./makeall clean
./makeall
```

By default only the latest sample (with the highest number) is build. The ready build *kernel8.img* file should be in its subdirectory of sample/. If you want to build another sample after `makeall` go to its subdirectory and do `make`.

Installation
------------

Copy the Raspberry Pi firmware (from boot/ directory, do *make* there to get them) files along with the kernel.img (from sample/ subdirectory) to a SD(HC) card with FAT file system. Put the SD(HC) card into the Raspberry Pi.

Directories
-----------

* include: The common header files, most class headers are in the include/circle/ subdirectory.
* lib: The Circle class implementation and support files (other libraries are in subdirectories of lib/).
* sample: Several sample applications using Circle in different subdirectories. The main function is implemented in the CKernel class.
* addon: Contains contributed libraries and samples (has to be build manually).
* app: Place your own applications here. If you have own libraries put them into app/lib/.
* boot: Do *make* in this directory to get the Raspberry Pi firmware files required to boot.
* doc: Additional documentation files.
* tools: Some tools for using Circle more comfortable (e.g. a serial bootloader).

Classes
-------

The following C++ classes were added to Circle:

Base library

* CTranslationTable: Encapsulates a translation table to be used by MMU (AArch64)

The available Circle classes are listed in the file *doc/classes.txt*. If you have doxygen installed on your computer you can build a class documentation in *doc/html/* using:

`./makedoc`

At the moment there are only a few classes described in detail for doxygen.

Trademarks
----------

Raspberry Pi is a trademark of the Raspberry Pi Foundation.

Linux is a trademark of Linus Torvalds.

PS3 and PS4 are registered trademarks of Sony Computer Entertainment Inc.

Xbox 360 and Xbox One are trademarks of the Microsoft group of companies.

Nintendo Switch is a trademark of Nintendo.
