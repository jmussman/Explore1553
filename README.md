[//]: # (readme.md)
[//]: # (Copyright © 2026 Joel A Mussman. All rights reserved.)
[//]: #

![Banner Light](./.assets/banner-Explore1553-light.png#gh-light-mode-only)
![banner Dark](./.assets/banner-Explore1553-dark.png#gh-dark-mode-only)

# Explore1553

## Overview

Fortunately practicing or experimenting with the electronics and protocols of MIL-STD-1553 can be accomplished
in a sandbox lab using off-the-shelf components.
Practicing with real MIL-STD-1553 hardware can be prohibitively expensive, and adding real components to a real
network unnecessarily increases the level of difficulty.

*Arduino* microcontrollers are sometimes suggested as a choice for simulating a MIL-STD-1533 component, but there are some problems:
1. They only run at 16Mhz
1. The clock resolution provided by the *delay()* function only has microsecond (µ) resolution
1. Trying to calculate the time used in loops, accessing data, etc. is difficult, must be
    subtracted from the 0.5µs timing for 1/2 of a bit signal, and the only way to do that
    is to execute an instruction with known timing (NOP) and repeat it X times

The *Teensy 4.0/4.1* development board series was chosen for this lab because:

1. It runs at 600 MHz
2. The *delaynanosecond()* function allows the 0.5µs resolution
3. Even better, the NXP i.MX RT1062 microcontroller chip has an integrated FlexIO controller
    that may be fed a stream of bits and keeps the signal low or high for exactly 0.5µs.

Shout-out to Paul Stoffregen at [PJRC](https://www.pjrc.com/) for the Teensy board and the integration into Arduino IDE :)

The sandbox defined by this project is derived from the [Flex1553](https://github.com/bsundahl1/Flex1553) project at GitHub by Bill Sundahl (*bsundahl1*).
Bill's project is focused on using transformers and amplifiers to integrate the Teensy as a bus controller or remote terminal into a real MIL-STD-1553 network.
The goal for this sandbox is the timing of the signals and interpreting them, so it is not necessary to go too deep into the Flex1553 project.

## Hardware & Software Requirements

This sandbox may be used with multiple teams in a classroom environment, or by an individual for their own experimentation.

### Hardware
1. Anti-static mat and wrist-strap, this one from Amazon is used in class and has both a wrist-strap and a grounding wire:
    [Anti-Static Mat](https://www.amazon.com/Anti-Static-Mat-ESD-Soldering-Electronics/dp/B0CB81S8VV).
    Another advantage of this mat is the size; you can put it on a desk in front of a monitor work with the electronics and the
    computer at the same time.
    Tip: in class the grounding wire was connected to a grounded electrical plug, so plugging it into the same electrical
    circuit as the computer grounds everything properly.
    These plugs came from Amazon, but Lowe's, Home Depot, Walmart, etc. will have them: [Electrical Plug](https://www.amazon.com/dp/B0CJ55LTZN).
1. One six-inch breadboard and jumper wires for mocking up circuits.
1. A second breadboard if you are working by yourself; multiple teams in a class will link their boards.
1. A Teensy 4.1 microcontroller board with male headers to plug into the breadboard [Search on Amazon](https://www.amazon.com/s?k=Teensy+4.1+with+headers).
1. A second Teensy microcontroller if you are working by yourself; again multiple teams in a class each have their own Teensy.
1. A USB-micro data cable to connect the Teensy 4.1 to the computer.
1. A Saleae-type USB logic analyzer [Search on Amazon](https://www.amazon.com/s?k=HiLetgo+USB+Logic+Analyzer) - any logic analyzer should work but you are on your own with others.<sup>*</sup>
1. A small zip-tie if you are working by yourself (used in [Lab 2](./Lab_02.md))
1. A USB data cable to connect the logic analyzer to the computer.
1. Two 81 &Omega;, two 380 &Omega;, and two 1k &Omega; resistors.
1. Needle-nose pliers for placing components on the breadboard.
1. In a multi-team environment 20-foot Cat-6 Ethernet cable to connect team buses together.
1. A multimeter, preferably with fine-tip (for breadboard) or alligator-clip tipped probes.
1. An entry-level 100MHz, dual channel oscilloscope if available (but not necessary).

### Software

This software is all free and all support Microsoft Windows, Apple MacOS, and Linux.

1. Arduino IDE 2.X+ for compiling programs and uploading to Arduino/Teensy microcontrollers, download from [Arduino Software](https://www.arduino.cc/en/software/)
1. Saleae Logic 2 for capturing and analyzing signals from the microcontroller, download from [Saleae](https://www.saleae.com/downloads)
1. Microsoft Visual Studio Code for creating and editing programs outside of the IDE, download from [Microsoft](https://code.visualstudio.com/download)

<sup>*</sup> If you are questioning why Sigrok PulseView was not chosen, being less proprietary:

1. On MacOS this worked fine.
1. On Microsoft Windows you have to use the PulseView ZaDig tool to add the USB driver, that requires admin privileges
1. The recorded signal was really jittery compared to Logic 2, and the signal was fine on a scope
1. No particular reason you could not successfully use it for the labs

## Labs

* [Lab 1: Research the following questions](./Lab_01.md).
* [Lab 2: Build a bus](./Lab_02.md)
* [Lab 3: Simulate a bus network and check its characteristics](./Lab_03.md)
* [Lab 4A: Explore hexadecimal conversions to/from decimal and parity](./Lab_04A.md)
* [Lab 4B: setup and test the microcontroller](./Lab_04B.md)
* [Lab 5: Use the microcontroller to send an RT to BC message](./Lab_05.md)
* [Lab 6A: Build a bus controller and an application launches the bus controller](./Lab_06A.md)
* [Lab 6B: Experiment with two microcontrollers, one provides the BC and the other as the RT](./Lab_06B.md)

## License

The code is licensed under the MIT license. You may use and modify all or part of it as you choose, as long as attribution to the source is provided per the license.
You are also free to incorporate it into training environments.
See the details in the [license file](./LICENSE.md) or at the [Open Source Initiative](https://opensource.org/licenses/MIT).


<hr>
Copyright © 2026 Joel A Mussman. All rights reserved.