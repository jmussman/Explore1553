[//]: # (Lab_02.md)
[//]: # (Copyright © 2026 Joel A Mussman. All rights reserved.)
[//]: #

# Lab 2: Bus Architecture

## Build a bus

\[ [Link to Lab contents](./README.md#labs) \]

### Part 1: Sketch a compliant A/B bus for 6 RTs

On paper design a robust topology for a medium-sized subsystem with 6 Remote Terminals and 1 Bus Controller.
Label each component in the architecture.
Ensure full MIL-STD-1553 compliance.

### Part 2: Map the bus traffic for the altitude display process

The *Flight Computer* is often the bus controller and also responsible for the instrument display.
The *altimeter* is a sensor usually embedded in the Air Data Computer (ADC)
On paper map the traffic on the bus to initiate and complete an update of the altitude display.
This is not intended to be specific bit patterns or data sent on the bus, just a high-level view of who sends what and expects what information.

### Part 3: Map the bus traffic to arm and launch a Sidewinder missile

Weapons are under the control of the Store Management System (SMS).
The SMS itself is always on, but individual weapons must be powered up (armed) in order to launch.
The Sidewinder missile is a heat seeking missile.
When the pilot selects a target the radar (its own RT) must lock and follow the target as it moves in relationship to the attacking aircraft (this one).
When the pilot chooses to fire the missile the SMS needs to know where the target is in order to have the Sidewinder lock on to the heat signature of that particular
target, and then the weapon must be physically launched.
As in part 2, this is not intended to be specific bit patterns or data sent on the bus but just a high-level view of who sends what and expects what information.

<br><br>![Stop](./.assets/stop_small.png) **Congratulations, you have completed this lab!**
