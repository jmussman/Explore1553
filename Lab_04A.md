[//]: # (Lab_04B.md)
[//]: # (Copyright © 2026 Joel A Mussman. All rights reserved.)
[//]: #

# Explore1553 Lab 4: Explore hexadecimal conversions to/from decimal and parity

\[ [Link to Lab contents](./readme.md#labs) \]

## Part 1: The BC needs to send a command to the Flight Computer

* The Flight computer is at address 21
* The subaddress for the command is 02
* What are the hex values for 21 and 22?
* Compare your answer with your lab partner's


## Part 2: We captured a command on the wire

* The address was 11011
* The subaddress was 00110
* What are these values in decimal?
* Compare this answer to your partner's

## Part 3: Build words and calculate parity

1. Build the BC command word from the following values and calculate the parity:
    * RT address 03
    * Receive data
    * Subaddress: 04
    * Word count: 16

1. Build the BC command word from the following values and calculate the parity:
    * RT address 30
    * Transmit data
    * Subaddress: 01
    * Word count: 01
1. Build the BC command word from the following values and calculate the parity:
    * RT address: 07
    * Status: busy Flag Set

<br><br>![Stop](./.assets/stop_small.png) **Congratulations, you have completed this lab!**

Answer key:

3.1: **00011 0 00100 10000, 1**<br>
3.2: **11110 1 00001 00001, 0**<br>
3.3: **00111 0 00000 1 0001, 0**<br>
