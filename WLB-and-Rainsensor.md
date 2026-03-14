## WLF/WLB connection
The WLC 100 station also has a 2-wire connection labelled WLF/WLB. This connection is for connecting a WLF 111 interface or a UPS (WLB - exact model name is unknown to me).<br>
The connections are labelled x and y.<br>
x is a ground connection and y is a 24V connection 
that can be used either (for WLF) to supply external devices with 24V DC or (for WLB) to supply the WLC 100 controller with power in case the mains power supply fails.

## Rain sensor connection
**Disclaimer:** I do not currently own a WLA 510 rain sensor, so I cannot analyse exactly how this functions. 
The content of this section is therefor solely based on observations, measurements and analysis of the WLC 100 controller.

The WLC 100 also features a 3-wire connection for a WLA 510 rain sensor. 
It is the 3 connections between the EXT button panel connection and the M1 motor connection and its connections are marked with purple, gray and black.<br>
There is also a black drop-like symbol above the color markings that I now believe to be an attempt at a rain drop symbol, though for years I did not recognize it for this...<br>
The Purple connection is ground (GND)<br>
The Gray connection is +24V DC<br>
The Black connection the signal connection.

As stated above, I do not have a rain sensor to test this with, so I do not know how communication with the rain sensor works exactly. 
While measuring the connections with a multimeter I however noticed some odd reading on the signal connection (Black) and decided to hook up my oscilloscope to see what is happening there.<br>
I found that at perfectly regular intervals of 2.58 seconds a single approximately 2.5V pulse with a pulse length of 8.3ms is sent out by the WLC 100 on this connection.<br>
I suspect that this might be a sampling signal, telling the rain sensor to send back information or similar.<br>
For a rain sensor it makes sense to only sample how wet the sensor is every so often, as one would want to avoid triggering the window motors every time eg a single drop flows over the sensor.<br>
The pulse could be used to wake up a microcontroller in the rain sensor, or more directly measure the wetness of the sensor.


If someone who reads this has a WLC 100 and matching rain sensor and has the ability to capture the signals, please get in touch as I would love to know exactly how this works and add more information here!
