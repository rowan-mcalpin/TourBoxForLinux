# 6/19/2025 - Began Reverse Engineering Communication Protocols
- TourBox seems to communicate using serial; shows up on my windows desktop as COM3 and on Linux as ACM0.
- Using WireShark to snoop the data being exchanged. 4 packets are exchanged when a button is pressed (perhaps press event and release event?), 2 packets are exchanged when a dial moves by a single increment.
- In the process of decoding/determining the "handshake" data exchanged between the computer and the tourbox. Perhaps that will result in some responses back from the TourBox if sent from Linux.

It seems that when the TourBox Console starts and establishes contact with the TourBox, it sends two packets to the TourBox. These packets are not sent when the TourBox Console is not open.
Here's the data from those two packets (this was from a single run, not yet known whether changing settings in the TourBox console changes the packet data):

**Packet 1**
```
0000   1b 00 b0 c9 9f 25 03 e7 ff ff 00 00 00 00 09 00
0010   00 03 00 13 00 02 03 5e 00 00 00 b5 00 5d 04 08
0020   05 08 06 08 07 08 08 08 09 08 0b 08 0c 08 0d 08
0030   0e 08 0f 08 26 08 27 08 28 08 29 08 3b 08 3c 08
0040   3d 08 3e 08 3f 08 40 08 41 08 42 08 43 08 44 08
0050   45 08 46 08 47 08 48 08 49 08 4a 08 4b 08 4c 08
0060   4d 08 4e 08 4f 08 50 08 51 08 52 08 53 08 54 08
0070   a8 08 a9 08 aa 08 ab 08 fe
```

**Packet 2**
```
0000   1b 00 b0 c9 9f 25 03 e7 ff ff 00 00 00 00 09 00
0010   01 03 00 13 00 02 03 00 00 00 00
```

### INFO
When the TourBox is connnected to the computer, pressing a button sends a packet (28 bytes) to the computer. The computer then responds with a 27 byte packet (always the same, regardless of the button
pressed). Releasing the button does the same. Rotating a dial clockwise or counterclockwise does the same.

The only differences in packets for different buttons or dials is the final bit of the packet sent from the TourBox to the computer.

Below is the data from the TourBox to the computer when a button is pressed. Note the final byte, which I've marked with ZZ (as that is not a valid hexadecimal value). That bit depends on which button/dial is pressed/moved.

```
0000    1b 00 10 90 3d 27 03 e7   ff ff 00 00 00 00 09 00
0010    01 03 00 19 00 82 03 01   00 00 00 ZZ
```

Additionally, here is the data sent from the computer back to the TourBox (likely a confirmation that the signal was received):

```
0000    1b 00 10 90 3d 27 03 d7   ff ff 00 00 00 00 09 00
0010    00 03 00 19 00 82 03 00   00 00 00
```

Below is a chart of all of the final bytes sent in the above payload for each button. (In other words, to find the message for a given button, replace the `ZZ` above with the corresponding payload from the chart below.)

#### "IN payload" from each button to computer -- final byte of payload, identified via WireShark

| Button | "Pressed" payload | "Released" payload |
| ------ | ----------------- | ------------------ |
|  Side  |       `01`        |        `81`        |
| Scroll |       `0a`        |        `8a`        |
|  Top   |       `02`        |        `82`        |
|   C1   |       `22`        |        `a2`        |
|   C2   |       `23`        |        `a3`        |
|  Tall  |       `00`        |        `80`        |
| Short  |       `03`        |        `83`        |
|   Up   |       `10`        |        `90`        |
|  Down  |       `11`        |        `91`        |
|  Left  |       `12`        |        `92`        |
| Right  |       `13`        |        `93`        |
|  Knob  |       `77`        |        `b7`        |
|  Tour  |       `2a`        |        `aa`        |
|  Dial  |       `38`        |        `b8`        |

#### "IN payload" from each dial to computer -- final byte of payload, identified via WireShark

|  Knob  | Clockwise/up payload | Counterclockwise/down payload |
| ------ | -------------------- | ----------------------------- |
| Scroll |         `49`         |             `09`              |
|  Knob  |         `44`         |             `04`              |
|  Dial  |         `4f`         |             `0f`              |

### Initialization 

The driver sends 50 packets to the TourBox when it first starts and establishes contact with the device. [This file](https://drive.google.com/file/d/1cOU9LK2oY-8nWSvNlnA_YSq3Auv76m3u/view?usp=sharing) has the data from all 50 packets sent from the computer to the device. That's not all that's needed, yet, though, because some packets had different endpoints. Just logging now so I don't forget. 
