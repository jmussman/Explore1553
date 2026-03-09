# Explore1553 Lab 3: Set up and test the Teensy 4.1 microcontroller

\[ [Link to Lab contents](./readme.md#labs) \]

## Hardware Required

1. The breadboard setup from the completion of Lab 2.
    *Do not touch this without anti-static protection!*

## Lab Steps

### Part 1: Add the Explore1553 library to Arduino IDE

The Arduino IDE looks for folders under the *Documents/Arduino/libraries* and automatically loads them to support
the programs it is compiling (and loading).

1. Find the green *Code* button at the top of this page,
    or visit [https://github.com/jmussman/Explore1553](https://github.com/jmussman/Explore1553) and
    find the button on that page.

1. Click the green button to expand the *Code dialog*.
1. Make sure the *Local* tab is selected and click on the *Download ZIP* option:
    <br><br>![GitHub Code Button](./.assets/Lab_02/github-code-button.png)
1. Extract the zip file to the *Documents/Arduino/libraries* folder on the computer.
1. Rename the extracted folder from *Explore1553-master* to just *Flex1553*.
1. Open the Viusal Studio Code application.
1. In VS Code, open the *Documents/Arduino/libraries/Explore1553* folder.
1. The repository is missing a properties file that Arduino looks for.
    Use the **File > New File...** menu option to start creating a new file.
1. Select *Text File* as the type.
1. Copy and paste this text into the file:
    ```text
    name=Flex1553
    version=1.0.0
    author=bsundahl1
    maintainer=bsundahl1
    sentence=A library for MIL-STD-1553 communication using Teensy 4.1 FlexIO.
    paragraph=This library enables high-speed 1553 protocol handling by leveraging the specialized FlexIO hardware on the IMXRT1060 processor.
    category=Communication
    url=https://github.com/bsundahl1/Flex1553
    architectures=teensy4
    includes=Flex1553.h
    ```
1. Navigate to and click *File > Save As...*
1. Name file *library.properties* (no other extension).
1. Save the file.

### Part 2: Add a channel to the logic analyz=er

1. Make sure the microcontroller and the logic analyzer are not powered.

1. Add another wire (white?) to H51 on the breadboard, pin 3 on the microcontroller.
1. Connect this to the brown wire on the logic analyzer (channel 2).
1. Power on the microcontroller and the logic analyzer.

### Part 2: Transmit an MIL-STD-1553 Command Word

1. In Arduino IDE navigate and click **File > New Sketch**.

    Programs are called "sketches" in Arduino.
1. New Sketch opens a new window, close the previous window.
1. Copy and paste this content into the new sketch file:
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
        myBusController.request(&myPacket, FLEX1553_BUS_A);
        digitalWrite(LED_BUILTIN, LOW);
        delay(4);
    }
    ```
1. Use **File > Save As...** to save the new file as *RC_BC_Repeat_Command*.
1. When Arduino IDE reopens the window make sure that the correct microcontroller is selected.
1. Use the **Run** button to compile and load the program.
    This program will keep repeating the command word over and over again.
    <br><br>![Flex1553 Single Command](./.assets/Lab_02/Flex1553-one-command.jpg)
1. In Logic 2 run a capture.
1. Zoom in to look at one signal group in the capture; remember the word keeps repeating.
    The FlexIO module is outputting the differential signal on pin 3, notice it is a mirror image of the
    channel 1 signal.
    The microcontroller cannot really handle that, so it is a positive signal when it should be negative,
    but that can be fixed (and the voltage bumped) in electronics around the microcontroller if it was interfacing to a real bus.
    <br><br>![Logic 2 Single Command](./.assets/Lab_02/Logic2-single-command.png)
1. Use the *Range Measurement* tool to check the length of the signal.
    What was found? Is that what was expected?

### Part 4: Decode the Command Word

1. First, What kind of word does the Sync indicate this is?

1. Turn the signal that you captured into a bit pattern.
1. Verify the checksum is correct, if it is not then the word is garbage.
1. Because this is a command or status word, figure out what the bits mean.

### Part 5: BC to RT

1. The previous command was RT to BC, initiated by the BC of course.
    Navigate to **File > Save As...** and save the file with as *BC_RC_Repeat_Command*.

1. When the file reopens, make sure the correct microcontroller is selected.
1. In the Arduino IDE locate the *loop()* function.
1. The second line in the function should be a call to the *request()* method in the myBusController object.
    Change *request* to *send* to initiate a BC to RT command.
    The parameters remain the same.
1. Click the *Run* button to compile and load the program.
1. In Logic 2, run a capture.
    What is different about the signal captured this time?
1. Decode the data words: what values do they carry?

<br><br>![Stop](./.assets/stop_small.png) **Congratulations, you have completed this lab!**
