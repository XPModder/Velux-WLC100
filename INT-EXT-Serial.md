# Velux Serial protocol analysis
After originally looking at the IR protocol used by the Velux WLR 100 remote control, I later also looked at the protocol used for communication between the WLI 100 IR receiver and the WLC 100 control unit that controls the motors of the blinds.

## Physical Layer
The WLC 100 controller can be connected to button panels and IR receiver units via a 3 wire interface on both the EXT and INT connections on the controller.

Multiple devices can be connected in parallel to the same controller and even multiple WLC 100 controllers can be connected in parallel to allow a single button panel or IR receiver to control more then 3 blinds.

The official Velux wires are red, yellow and blue wires in a white outer sleeving. The terminals on the WLC 100 are also marked with these colors.

The Red wire provides the ground (GND) connection, while the Yellow wire provides a 5V supply from the WLC 100 and the Blue wire is used for the signal.

The signal wire has a pullup resistor inside the WLC 100 and devices appear to use open collector outputs to pull the line low. This allows for easy paralleling of devices.

While I am unable to test this theory as I only have a single IR receiver to connect to my WLC 100, I believe that there is essentially no protection against bus collisions, should multiple devices attempt to send data simultaneously. Most like the controller will simply recognize the malformed data and ignore it. It is also understandable to me that this was not addressed as these devices were intended for consumer use and it is unlikely multiple people would be attempting to control the same window/blinds at the same time with different button panels or remotes.

The WLC 100 appears to be using a regular 7805 or similar linear regulator to create the 5V supply, so it should easily be possible to pull a few hundred mA on these wires without causing any issues.

## Protocol Layer
I have analyzed the serial protocol used on the blue signal wire and it appears to be relatively simple.

A message consists of either 2 or 6 packets, depending on message data. In most cases a 2 packet message is sent, but the 'All Up' and 'All Down' commands use a 6 packet message.
<img width="800" height="480" alt="DS0005" src="https://github.com/user-attachments/assets/1136352d-ebfb-4b44-875a-2a9045fe6dbf" />

Packets are separated by a 29.7ms idle HIGH time.
<img width="800" height="480" alt="DS0006" src="https://github.com/user-attachments/assets/a59b5582-7df9-4b6e-9080-7d45f542ce73" />

Each message consists of 12 bits, the first 4 of which identify the type of message and the remaining 8 bits contain the data. There is no error handling, CRC or checksum. There also appears to be no feedback or acknowledgement from the controller.<br>
<img width="800" height="480" alt="DS0007" src="https://github.com/user-attachments/assets/e9ae2582-d8cc-49a4-9651-f14d3f3e3cfd" />

Each bit is 1.75ms long (interestingly this is the same as the bit time for the IR protocol, though that is also where the commonalty ends), beginning with a LOW period, followed by a HIGH period. A 1-bit consists of a 1.35ms LOW followed by a 0.4ms HIGH time while a 0-bit has a 0.5ms LOW followed by a 1.25ms HIGH time.
It appears that solely the LOW time is used to determine the data, as at the end of a packet, the line simply enters the idle HIGH state after the LOW time of the last bit. Therefor there is no possibility to determine the end of the HIGH time of the last bit and the beginning of the idle state.<br>
<img width="800" height="480" alt="DS0008" src="https://github.com/user-attachments/assets/0a7f8d7e-07b1-44cb-9357-42f265371610" />
<img width="800" height="480" alt="DS0009" src="https://github.com/user-attachments/assets/8ab71be9-e6c7-405d-a7ce-598bb8ece495" />
<img width="800" height="480" alt="DS0010" src="https://github.com/user-attachments/assets/5a830feb-3ea9-4d2d-8dfb-3dd59fe94033" />

In the following I will use letters D, U, SS and GGGG in packet structure notation. If the command is a DOWN command, D bits will be 1 and U bits will be 0. 
If the command is a STOP command the SS bits will be 01, otherwise they are 10.
The 4 bits GGGG are the binary encoded motor group number. When the command addresses all motors, these bits are 0000. Otherwise they are a simple binary representation of the group number.

### 'All up' and 'All down' commands
I will discuss the 'All up' and 'All down' commands first as they use a different message structure then all other commands.

These commands consist of 6 packets and the entire message is sent twice. Really there are just 3 different packets, each of which is sent twice in a row, followed by the next set.

The first and second packet have the structure 1010100D00U0, the thrid and fourth packet have the structure 10101000DU00 and the fifth and sixth packet have the format 101010D0000U

### Stop commands
The stop command seems to be the same regardless of which motor and/or group is selected.

The stop command consists of 2 packets, the first of which is 101001100000 and the second is 11110000GGGG

### Other commands
Other commands consist of two packets. 

The first packet has the structure 101010DDDUUU and the second has the structure 11110000GGGG<br>
The down and up bits are for the motors as follows: D D D U U U -> M3 M1 M2 M3 M1 M2

From this structure it appears that it could be possible to command multiple motors in a single command, though I have not seen this ever happen and have not tested sending such a command.

## Usage
This protocol is relatively simple and should be very straight forward to implement in software on a microcontroller by bit-banging with a simple timer.

I have only done very little experimenting with this, but plan to use this information to create a small wireless or ethernet capable board to control my blinds over the local network.

If I remember I will update this repo with my project.
