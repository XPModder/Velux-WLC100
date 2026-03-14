# Velux-WLC100
Analysis and reverse engineering of protocols used by the Velux WLC 100 controller

This repo contains my findings on analysing and reverse engineering the infrared and serial protocols of the Velux WLC 100 controller for electric window blinds.

Disclaimer: The name Velux and the names of its products are property of the company Velux. This project is created for fun and out of interest to interface with the blinds by different platforms. It is not used for profit in any way and is intended for personal use only.

I had previously created a repo specifically for the IR protocol and am including the content of that here.

Shoutout to @ArminJo who used my analysis of the IR protocol to add support for it to the Arduino IRremote library [here](https://github.com/Arduino-IRremote/Arduino-IRremote/blob/988fb012014b9089746d2a380fd83368334cffd2/src/ir_Others.hpp#L110)!

## PlatformIO Library released

I have now created a PlatformIO library for sending commands using the INT/EXT serial interface on the WLC 100 controller.<br>
The source for the library can be found [here on my personal gitlab](https://gitlab.xpmodder.com/XPModder/VeluxSerial).<br>
The library is also in the PlatformIO Registry and can be found there under the name VeluxSerial.
