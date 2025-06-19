## 6/19/2025 - Began Reverse Engineering Communication Protocols
- TourBox seems to communicate using serial; shows up on my windows desktop as COM3 and on Linux as ACM0.
- Using WireShark to snoop the data being exchanged. 4 packets are exchanged when a button is pressed (perhaps press event and release event?), 2 packets are exchanged when a dial moves by a single increment.
- In the process of decoding/determining the "handshake" data exchanged between the computer and the tourbox. Perhaps that will result in some responses back from the TourBox if sent from Linux.

It seems that when the TourBox windows driver starts and establishes contact with the TourBox, it sends two packets to the TourBox. Here's the data from those two packets:

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

More updates coming soon as I make more progress.
