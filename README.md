# Kenwood TS-480 Control Panel (Remote Head)

This is my attempt at reverse engineering and documenting the serial protocol used between the radio and the control panel.
I've split documentation into different folders in an attempt to keep things organized.

## Input and Output 
* [Buttons: byte & ASCII values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\Buttons.md)
* [Radio to Panel: S Meter / SWR Meter values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\Display.md)
* [S Meter Display Values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\S-Meter_UART-RX.md)


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

### Communication example between the radio and control panel
Documentation is split into two "chunks", the panel serial data and the transceiver / radio's responses etc.
Primary focus for now is to reverse engineer the panel so it can be interfaced with a "insert radio here" ;)

#### COMMAND -> DATA VALUE -> ACK

Keepalive / ACK sequence:

Panel / Radio

![ACK Sequence](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/ACK%20Sequence.png)

* "ACK to ACK" interval is 3 seconds.
* Panel has it's own keepalive / timeout logic.
* Radio ACK is `0xFF` when powered, `0x00` when off, followed by `0x0D` (carriage return)
* Panel keepalive / ack is `0xFF` `0x0D`


[Panel_Disconnect_Reconnect](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/Logic_AgxXCBxsCz.png)

When disconnecting the panel it will attempt to turn back on, assuming the radio is on.
```
0x0D 0x0D 0x0D 
0x0D 0x0D 0x0D 
0x0D 0x0D 0x0D 
0x0D 0x0D 0x0D 
0x0D 0x00 0x0D 
0x0D 0x0D 0x01 
0x0D 0x01 0x0D
```
0D 0D 0D 0D 0D 0D 0D 0D 0D 0D 0D 0D 0D 00 0D 0D 0D 01 0D 01 0D
### Knobs
TODO

Values here seem to be made up of:
1. Direction.
2. Speed or increment? Or just value
3. Value

A justing AF output (Volume) is contained purely to the panel, no additional serial info is exchanged.

```
0x58 0x30 0x31 0x36 0x33 0x0D
0x58 0x30 0x32 0x30 0x34 0x0D
0x58 0x30 0x32 0x30 0x43 0x0D
0x58 0x30 0x32 0x31 0x35 0x0D

```

Knob adjustments have the following known valid values:
Left: `0x46 0x46`
Right: `0x30, 0x31`

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
**RIT/XIT Knob**

ASCII value: U0 (speed): 1 (right) / UF (speed): F (left)

Left: 
```
Cmd  State Value Ack
0x55 0x46  0x46  0x0D
0x55 0x46  0x44  0x0D
0x55 0x46  0x45  0x0D
0x55 0x46  0x46  0x0D
```
Right:
```
Cmd  State Value Ack
0x55 0x30  0x31  0x0D
0x55 0x30  0x32  0x0D
0x55 0x30  0x34  0x0D
0x55 0x30  0x35  0x0D
0x55 0x30  0x33  0x0D
```

**Multi & IF-Shift knob**

Multi left:
In ASCII T+hexvalue (but sent as ascii..), e.g T01, T20
```
Command  Value
0x54     0x46 0x46
```
Right:
```
Command  Value
0x54     0x30 0x31 
```

If Shift:

"value" is dependent the physical speed the knob is adjusted.

**VFO knob**

Example moving the VFO knob, to the right, from `28.530.00` to `28.530.05`

![Frequency Change](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/Frequency%20Change.png)

Panel "commands" to adjust VFO frequency:

They are prefixed with S and then contains a hex byte value

```
Panel (TX)   Radio (RX)
S0001\r      ;2853001\r
```



TODO work out increments here, it makes more sense to use the multi knob for faster frequency changes anyways..

```
Panel CMD  Increment
S8000      -163.84kHz
S7999      155.65kHz
S7000      143.36 kHz
S5800      112.65 kHz
S2400      46.08 KHz
S1900      32.00 KHz
S1400      25.60 KHz
S1000      20.48 KHz
S0900      11.52 KHz
S0800      10.24 KHz
S0700      8.96 KHz
S0600      7.69 KHz
S0500      6.41 KHz
S0400      5.12 KHz
S0300      3.85 KHz
S0200      2.56 KHz
S0100      1.28 KHz
S0090      72
S0080      64
S0060      48
S0050      41
S0020      16
S0017      12
S0016      11
S0015      11
S0014      10
S0013      9.9?
S0012      9
S0011      9
S0010      8
S0009      5
S0008      4
S0007      4
S0006      3
S0005      3
S0004
S0003      
S0002
S0001     0 (need to execute twice to increment 1)
```

Going the other way from `28.530.05` to `28.530.00`:a

Valid range seems to be `SFFF` to `SFE1?`
Looks like there are two `SFFF`'s for each frequency change?
```
Panel (TX)   Radio (RX)
SFFFF \r
SFFFF \r
SFFFF \r     ;2853004\r
SFFFF \r

SFFFF \r
SFFFF \r     ;2853003\r

SFFFF \r
SFFFF \r     ;2853002\r
SFFFF \r
SFFFF \r     ;2853001\r
SFFFF \r
SFFFF \r     ;2853000\r
SFFFF \r
```

I suspect the values simply indicate _how_ fast / how much the frequency will change, spinning the dial quickly produces
`SFFEF` instead and reply has 6 frequencies in it's reply.
`SFFC8` -> 9 frequency changes.
`SFF91` -> 9
`SFF74` -> 11
`SFFC0` -> 10
`SFFFE` -> 2

There is likely _some_ cumulative effect here without causing a massive "queue" of pending updates; could have some interrupt logic..
The "poll" rate of the VFO knob / encoder from the panel side is pretty constant ~28-31ms.

![VFO Frequency](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/VFO%20Frequency.png)
