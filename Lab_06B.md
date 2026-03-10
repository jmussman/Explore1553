[//]: # (Lab_06B.md)
[//]: # (Copyright © 2026 Joel A Mussman. All rights reserved.)
[//]: #

# Explore1553 Lab 4: Build an application that interfaces with the bus controller

\[ [Link to Lab contents](./README.md#labs) \]

Be aware that this lab has the potential to create a bus where the impedance is so high that the 3.3 volt signal
is not recognizable on the receiving end.
This will be indicated by a failure of the logic analyzer to read the expected data,
or by the signal and voltage on an oscilloscope if one is available.

## Hardware Required

1. Two breadboard setups from the completion of Lab 6A.
    In a classroom scenario the second will be another lab team, by yourself you need to build a second copy of the setup.
    If there are an odd number of teams in the classroom, three teams will be connected together with one in the middle.
    *Do not touch this without anti-static protection!*

## Lab Steps

### Part 1: Rebuild and extend the simulated MIL-STD-1553 bus

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

### Part 2: Clear the *possible* third breadboard:

1. If there is a third breadboard in the middle of the bus, on the computer in the Arduino IDE create
    a new sketch.

1. Verify the new sketch shows this code, if it does not replace it with this code:
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

### Part 3: Load the RT program and initiate the message

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

    mailBox1.setData(0, (uint16_t)0x1234);
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
