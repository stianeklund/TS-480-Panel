# Kenwood TS-480 Control Panel (Remote Head)


This is my attempt at reverse engineering and documenting the serial protocol used between the radio and the control panel.
Documentation is split into chunks please see the sections below.

## Input and Output 
* [Buttons: byte & ASCII values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\Buttons.md)
* [Radio to Panel: S Meter / SWR Meter values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\Display.md)
* [S Meter Display Values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\S-Meter_UART-RX.md)
* [Encoders & Knobs](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\Encoders.md)

### Panel <--> Radio Serial Communications

* UART (idle high), 5V TTL.
* 57600bps 8N1 (8 bits, no parity, 1 stop bit.
* A lot of values are ASCII, with the usual Kenwood oddities.

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

#### COMMAND -> DATA VALUE -> ACK

Keepalive / ACK sequence:

Panel / Radio

![ACK Sequence](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/ACK%20Sequence.png)

* "ACK" interval is 3 seconds.
* Panel has it's own keepalive / timeout logic, radio also separately.
* Radio ACK is `0xFF` when powered, `0x00` when off, followed by `0x0D` (carriage return)
* Panel keepalive / ack is `0xFF` `0x0D`
* Panel will reply 02 from 01 inquiry.

Changing frequency:

![Frequency Change](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/Frequency%20Change.png)

-- 

### Volume and Squelch

Adjusting AF output (Volume) is contained purely to the panel, no additional serial info is exchanged.

Valid volume values are from `V00` to `VFF`

```hexdump
Command  Values 
0x56,    0x30, 0x31
0x56,    0x30, 0x32
0x56,    0x30, 0x33
0x56,    0x30, 0x34
0x56,    0x30, 0x35
0x56,    0x30, 0x36
```

SQL (Squelch): 
Valid values are `W00`  to `WFF`
```
Command(ASCII) Valid values
0x57 (W)       W00 .. WFF
```

```
30 31 0d 30 32 0d

Frequency prefix:
39 31 0D 3B 31 34 31 35 30 30 30 0d <-- Set frequency
Byte 0x81, 0x82 etc are the arrow indicators
3D 30 30 30 30 0D 44 81 0D 45 81 0D <-- apply to VFO
Just changing VFO A/B without updating frequency:
3D 30 30 30 30 0D 44 81 0D 45 81 0D <-- VFO A
3D 30 30 30 30 0D 44 82 0D 45 82 0D <-- VFO B


92 0x0D ;1409200 0x0D =0000 0x0D 0x0D D 0x0D E x0D
92 0x0D ;1409200 0x0D =0000 0x0D 0x0D D 0x0D E x0D
Set VFO A to Mode USB frequency:
Mode 
92 0x0D ;1409200 0x0D =0000 0x0D D 0x0D 0x0D E x0D
VFO B:
92 0x0D ;1409200 0x0D =0000 0x0D D 0x0D 0x0D E x0D
92 0x0D ;1530706 0x0D =0000 0x0D D, 0x0D E,0x0D 
92 0x0D ;15311100x0D =0000 0x0DD‚0x0D E‚0x0D

92$0D;1409200$0D=0000 $0D $0D $0D E $0D

Hex:     
39 32 0D <-- Command to change frequency
3B 32 38 30 32 35 30 30 0D  //;FREQUENCY \r
3D 30 30 30 30 0D           // =0000 (not sure what this means but it's def related to the display value)
40 84 0D                    // @,, (Set ANT1?)
41 81 0D                    // A 
42 81 0D                    // B 

Set frequency on display, this is just the radio saying, hey display this frequency
;2802500{0D}=0000{0D}
;700500{0D}=0000{0D}D‚{0D}E‚{0D}

```