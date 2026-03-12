[//]: # (Lab_06A.md)
[//]: # (Copyright © 2026 Joel A Mussman. All rights reserved.)
[//]: #

# Lab 6: Application Layer

\[ [Lab Table of Contents](./README.md#labs) \]

## Section A: Implement an application that embeds a Bus Controller

### Hardware Required

1. The breadboard setup from the completion of Lab 5.
    *Do not touch this without anti-static protection!*

### Lab Steps

This focuses on the interface provided by the MIL1553 library from the FLex1553 project.
In order to manage the signal on the wire, the application uses this interface.

#### Part 1: Build up the application

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
    #define REMOTE_TERMINAL_ADDRESS 5
    #define SUBADDRESS 2
    #define WC 5
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
        myPacket.setWordCount(WC);
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

1. Use the MIL1553 library bus controller *interface* to send the command word on the wire to
    ask the RT to send data (a request on the BC side is a "send data" request):
    ```cpp
        myBusController.request(&myPacket, FLEX1553_BUS_A);
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
    #define WC 5

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
        myPacket.setWordCount(WC);
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

#### Part 2: Compile, load, and test the program:

1. The microcontroller and logic analyzer were left connected to the USB and powered up in the previous lab.
    Verify this is still true, if not connect and power up the components.

1. Use the green arrow *Run* button on the IDE toolbar to compile and load the program
    (fix any errors that may occur compiling the code).
1. Run a capture in the Logic 2 program.
1. Zoom in and decipher the results.
    The command word should look the same as the RT to BC command from the previous labs.

## Section B: Complete the message cycle betwen a Bus Controller and a Remote Terminal

This lab has the potential to create a bus where the impedance is so high that the 3.3 volt signal
is not recognizable on the receiving end.
This will be indicated by a failure of the logic analyzer to read the expected data,
or by the signal and voltage on an oscilloscope if one is available.

### Hardware Required

1. Two breadboard setups from the completion of Lab 6 Section A.
    In a classroom scenario the second will be another lab team, by yourself you need to build a second copy of the setup.
    If there are an odd number of teams in the classroom, three teams will be connected together with one in the middle.
    *Do not touch this without anti-static protection!*

### Lab Steps

#### Part 1: Rebuild and extend the simulated MIL-STD-1553 bus

1. Make sure the microcontroller and the logic analyzer are not connected to USB, and are not powered.

1. Make sure of proper grounding before following these steps to reconfigure the breadboard.

1. Remove the 82 &Omega; resistor on the side of the breadboard that will connect to the other breadboard.
    If three breadboards are being connected, remove both 82 &Omega; resistors.
1. If the breadboard is one end of the bus, change the remaining 82 &Omega; resistor to a 1k &Omega; resistor.
    The 1K resistor has bands is Brown, Black, Black, Brown, Brown.
    If the breadboard is in the middle of the bus it has no terminating resistors.
1. Add a jumper wire to connect both sides of row 15 together.
1. Add a connection from the ground pin on the microcontroller to an available socket in row 15.
1. A 330 &Omega; resistor needs to be placed between the microcontroller transmit lines and the simulated bus.
    Put one resistor across two empty rows on the breadboard; the rows do not need to be adjacent, just empty.
    Connect a jumper wire from pin 2 on the microcontroller to one of the rows the resistor is bridging.
    Connect a jumper wire from the other row the resistor is bridging to first row in the simulated bus.
1. Place another 330 &Omega; resistor bridging another pair of empty rows.
    Connect a jumper wire from pin 3 on the microcomputer to one of the rows the resistor is bridging.
    Connect a jumper wire from the other row the resistor is bridging to the second row in the simulated bus.
1. Place a jumper wire from pin 6 on the microcomputer (the receive (RX) pin) and connect it to the first
    wire in the simulated bus.
    No resistor is required for this connection because the RX impedance is already so high at the pin there is
    no need to protect it.
1. Repeat steps 1-7 on the other breadboard that will be connected.
1. Bridge the two lines and the ground line between the two boards using the Cat-6 ethernet cable.

#### Part 2: If there is a third breadboard (three lab teams) clear it:

**Only complete this part on the breadboard in the middle of the bus:**

1. If there is a third breadboard in the middle of the bus a running BC on that micrcontroller will interfere
    if it connected to the bus.
    To ensure this will not be a problem, the microcontroller program should be cleared.
    On the computer connected to that microcontroller, in the Arduino IDE use the menu option
    **File > New Sketch** to create a new sketch.
    This will open a new window with the new sketch.

1. In the new window verify the new sketch shows the following code.
    If it does not, replace the window contents with this code:
    ```cpp
    void setup() {
    // put your setup code here, to run once:

    }

    void loop() {
    // put your main code here, to run repeatedly:

    }
    ```
1. Use the green arrow "Run" button to compile and load the program to keep the microcontroller from
    interfering with the simulated bus.

#### Part 3: Load the RT program and initiate the message

1. Connect the microcontrollers and the logic analyzers to the respective computers.
1. Load and run the BC program from Lab 6A on one of the boards.
1. On the other board (or boards) create a new sketch and run this receive program.
    The code is the RT_send_packet.cpp example from the FLex1553 project, without the comments
    (you can read them in the code in the *examples* folder):
    ```cpp
    #include <Arduino.h>
    #include <Flex1553.h>
    #include <MIL1553.h>

    #define RTA       5 
    #define MB1_SA    1 
    #define MB1_WC    1 
    #define MB2_SA    2 
    #define MB2_WC    5 

    void printPacket(MIL_1553_packet *packet);

    const int ledPin = 13;
    const int rx1553Pin = 6;
    unsigned long loopCount = 0;
    bool ledState  = false;

    FlexIO_1553TX flex1553TX(FLEXIO1, FLEX1553_PINPAIR_3);
    FlexIO_1553RX flex1553RX(FLEXIO2, rx1553Pin);
    MIL_1553_RT  myRemoteTemrinal(&flex1553TX, &flex1553RX, FLEX1553_BUS_A);
    MIL_1553_packet mailBox1;
    MIL_1553_packet mailBox2;

    void setup() {
        Serial.begin(115200);
        while (!Serial)
            ;

        pinMode(ledPin, OUTPUT);

        if(!myRemoteTemrinal.begin(RTA))
            Serial.println( "myRemoteTemrinal.begin() failed" );

        Serial.println();
        Serial.println("1553 RT Example");

        mailBox1.setData(0, 0);
        mailBox1.newMail = true; 
        ;

        if(!myRemoteTemrinal.openMailbox(MB1_SA, MB1_WC, MIL_1553_RT_OUTGOING, &mailBox1))
            Serial.println("Open mailbox1 failed");

        uint16_t data[] = {0x0012, 0x0034, 0x0056, 0x0078, 0x0910};
        mailBox2.setData(data, MB2_WC);
        if(!myRemoteTemrinal.openMailbox(MB2_SA, MB2_WC, MIL_1553_RT_OUTGOING, &mailBox2))
            Serial.println("Open mailbox2 failed");
    }

    void loop()
    {
        MIL_1553_packet* packet = myRemoteTemrinal.mailSent();
        if (packet != NULL) {
            Serial.print("Data was read from SA ");
            Serial.println(packet->getSubAddress());
            packet->newMail = true;
        }

        if(myRemoteTemrinal.errorAvailable())
            myRemoteTemrinal.printErrReport();

        if (Serial.available() > 0) {
            char c = Serial.read();
            if(c == 'n') {
                uint16_t newData = Serial.parseInt();
                Serial.print("new data = ");
                Serial.print(newData, DEC);
                Serial.print(", 0x");
                Serial.println(newData, HEX);
                mailBox1.setData(0, newData);
            }

            if(c == 'd')
                myRemoteTemrinal.dumpInternalState();

            if(c == 'p')
                myRemoteTemrinal.dumpMailboxAssigments();
        }

        if( (loopCount % 0x0400000L) == 0 ) { 
            ledState = !ledState;
            digitalWrite(ledPin, ledState);
        }

        loopCount++;
    }

    void printPacket(MIL_1553_packet *packet)
    {
        int wc = packet->getWordCount();

        Serial.print(" RTA:");
        Serial.print(packet->getRta());
        Serial.print(" SA:");
        Serial.print(packet->getSubAddress());

        // print status word
        Serial.print(", STATUS:0x");
        Serial.print(packet->getStatusWord(), HEX);

        // Dump out the packet data
        Serial.print(", Payload[");
        Serial.print(wc);
        Serial.print("]:");
        for (int i=0; i<wc; i++)
        {
            Serial.print(packet->getData(i), HEX);
            Serial.print(" ,");
        }
        Serial.println();
    }
    ```

1. Use the green arrow "Run" button in the Arduino IDE on the *second* microcontroller and computer to compile and load
    the RT side of the scenario.
1. Capture the signal in the Logic 2 analyzer program on both computers:
    * Verify it is the same capture data.
    * Look for and decode the data from the response side of the message; the data sent by the second board in
        response to the command from the first board (the BC).

<br><br>![Stop](./.assets/stop_small.png) **Congratulations, you have completed this lab!**
