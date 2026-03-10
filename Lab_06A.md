[//]: # (Lab_06A.md)
[//]: # (Copyright © 2026 Joel A Mussman. All rights reserved.)
[//]: #

# Explore1553 Lab 6A: Build a bus controller and an application launches the bus controller

\[ [Link to Lab contents](./readme.md#labs) \]

## Hardware Required

1. The breadboard setup from the completion of Lab 5.
    *Do not touch this without anti-static protection!*

## Lab Steps

This focuses on the interface provided by the MIL1553 library from the FLex1553 project.
In order to manage the signal on the wire, the application uses this interface.

### Part 1: Build up the application

1. In the Arduino IDE use the menu item **File > New Sketch** to create a new, empty sketch file.

1. Use **File > Save As...* and name the sketch *BC*.
1. Wait for the window to close and reopen with the newly named sketch.
1. "Include" header files in C++ have the definitions of the library functions and object-oriented classes
    used by client applications.
    Start the program code in the IDE by including the header files for the Arduino environment, the Flex1553 library, and the MIL1553 library to interface
    the application to the microcontroller:
    ```cpp
    #include <Arduino.h>
    #include <Flex1553.h>
    #include <MIL1553.h>
    ```

1. The remote terminal address and subaddress, as well as the word count of data
    expected to be sent, would be placed into the "schedule" the bus controller runs.
    For the moment, these values are hardwired into the application as the only command to
    send instead of building a full schedule loop (// begins a comment in the code, and
    the blank line preceding it is only to enhance readability of the application code):
    ```cpp

    // Send to RT 5 subaddress 2 (no particular reason why these numbers)
    #define REMOTE_TERMINAL_ADDRESS 6
    #define SUBADDRESS 2
    ```

1. The rx1553Pin variable will hold which pin on the microcontroller is connected to the
    simulated bus to receive data:
    ```cpp

    const int rx1553Pin = 6;
    ```

1. Recall that the embedded FlexIO controller is passed a stream of bits, and it handles sending each
    bit in a µ-long space.
    Configure an instance of the Flex1553 transmit class to transmit the signal on pings 2 and 3:
    ```cpp

    FlexIO_1553TX flex1553TX(FLEXIO1, FLEX1553_PINPAIR_3);
    ```

1. Add an instance of the Flex1553 receive class to process the response from the remote terminal:
    ```cpp
    FlexIO_1553RX flex1553RX(FLEXIO2, rx1553Pin);
    ```

1. The Flex1553 transmit and receive class instances are low level objects and interface to the embedded FlexIO controller.
    The purpose is to initialize an instance of *MIL_1553_BC* bus controller class:
    ```cpp
    MIL_1553_BC  myBusController(&flex1553TX, &flex1553RX);
    ```

1. A *MIL_1553_packet* instance will encapsulate the information about the command to the RT and the data:
    ```cpp

    MIL_1553_packet myPacket;
    ```

1. The Arduino environment has two functions: *setup()* to initialize the microcontroller, and *loop()* to repeat
    the execution of a section of program in the microcontroller *forever*.
    The first line of the *setup()* function initializes the LED on the microcontroller as an output channel:
    ```cpp
    void setup() {
        pinMode(LED_BUILTIN, OUTPUT);
    ```

1. In the setup the BC object instance needs to be told to start up in order to send data on the wire:
    ```cpp
        myBusController.begin();
    ```

1. The packet encapsulates the information that will be used for the transmission.
    Clear it and initialize the RT address, subaddress, word count, and receive or transmit, and close
    the function body with a *}*:
    ```cpp

        myPacket.clear();
        myPacket.setRta(REMOTE_TERMINAL_ADDRESS);
        myPacket.setSubAddress(SUBADDRESS);
        myPacket.setWordCount(6);
        myPacket.setTrDir((trDir_t)TRANSMIT);
    }
    ```

1. The loop repeats a block of code forever:
    ```cpp
    void loop()
    {
    ```

1. Execute a command to turn the board LED on:
    ```cpp
        digitalWrite(LED_BUILTIN, HIGH);
    ```

1. Use the MIL1553 library bus controller *interface* to send the command word on the wire:
    ```cpp
        myBusController.send(&myPacket, FLEX1553_BUS_A);
    ```

1. Execute a command to turn off the board LED.
    This will happen very fast, so visually the LED will show a very slight flicker or remain on dimly:
    ```cpp
        digitalWrite(LED_BUILTIN, LOW);
    ```

1. Finish up the *loop()* function with a four-µ delay and the end of the loop block:
    ```cpp
        delay(4);
    }
    ```

1. To verify the work, the whole program in the editor should look like this:
    ```cpp
    #include <Arduino.h>
    #include <Flex1553.h>
    #include <MIL1553.h>

    // Send to RT 5 subaddress 2 (no particular reason why these numbers)
    #define REMOTE_TERMINAL_ADDRESS 6
    #define SUBADDRESS 2

    const int rx1553Pin = 6;

    FlexIO_1553TX flex1553TX(FLEXIO1, FLEX1553_PINPAIR_3);
    FlexIO_1553RX flex1553RX(FLEXIO2, rx1553Pin);
    MIL_1553_BC  myBusController(&flex1553TX, &flex1553RX);
    MIL_1553_packet myPacket;

    void setup() {
        pinMode(LED_BUILTIN, OUTPUT);
        myBusController.begin();

        myPacket.clear();
        myPacket.setRta(REMOTE_TERMINAL_ADDRESS);
        myPacket.setSubAddress(SUBADDRESS);
        myPacket.setWordCount(6);
        myPacket.setTrDir((trDir_t)TRANSMIT);
    }

    void loop()
    {
        digitalWrite(LED_BUILTIN, HIGH);
        myBusController.send(&myPacket, FLEX1553_BUS_A);
        digitalWrite(LED_BUILTIN, LOW);
        delay(4);
    }
    ```

### Part 2: Compile, load, and test the program:

1. The microcontroller and logic analyzer were left connected to the USB and powered up in the previous lab.
    Verify this is still true, if not connect and power up the components.

1. Use the green arrow *Run* button on the IDE toolbar to compile and load the program
    (fix any errors that may occur compiling the code).
1. Run a capture in the Logic 2 program.
1. Zoom in and decipher the results.
    The command word should look the same as the RT to BC command from the previous labs.

<br><br>![Stop](./.assets/stop_small.png) **Congratulations, you have completed this lab!**
