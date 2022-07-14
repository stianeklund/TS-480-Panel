# Kenwood TS-480 Control Panel (Remote Head)


This is my attempt at reverse engineering and documenting the serial protocol used between the radio and the control panel.
Documentation is split into chunks please see the sections below.

## Overview
* [Panel: Readme](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/README.md)
* [Panel: Buttons: byte & ASCII values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/Buttons.md)
* [Panel: Display segments](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/Display.md)
* [Panel: S Meter Display Values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/S-Meter_UART-RX.md)
* [Radio: Power up sequence](https://github.com/stianeklund/TS-480-Panel/blob/main/Radio/Power%20on%20sequence.md)
 
### Panel <--> Radio Serial Communications
* UART (idle high), 5V TTL: RXO <- TXO. TXO -> RXO
* 57600bps 8N1 (8 bits, no parity, 1 stop bit) for both lines
* A lot of values are ASCII.

#### RJ12 Connector Pinout:
```
Pin  Name    Description
1    GND     Speaker ground
2    8V      Control panel power (measured 9v..)
3    GND
4    RX      5v TTL UART (radio to control head / panel)
5    TX      5v TTL UART (from control head / panel to radio)
6    AF      Speaker Audio
```

*COMMAND -> DATA VALUE -> ACK*

#### Keepalive / ACK sequence:

![ACK Sequence](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/ACK%20Sequence.png)

####  Interval
![ACK_Interval](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/ACK_Interval.png)

#### Timing:
![ACK_Timing](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/ACK_Timing.png)

* The radio and panel have their own ACK intervals, both have timeout logic.
* Interval on the radio is ACK every 5 seconds.
* Panel interval is every 2 seconds but received "interrupt ACK" from radio on third second, responds immediately after.

#### Changing frequency:

![Frequency Change](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/Frequency%20Change.png)

-- 

### Volume and Squelch

Adjusting AF output (Volume) is contained purely to the panel, no additional serial info is exchanged.
ARCP-480 can adjust volume as well but this is probably done by changing the voltage the speaker sees.

Valid volume values are from `V00` to `VFF`
