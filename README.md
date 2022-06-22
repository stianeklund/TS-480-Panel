# TS-480-Panel
This is an attempt at documenting the Kenwood TS-480 Transceiver's front panel behavior &amp; reverse engineering of the serial protocol.


### TS-480 Serial Communications

Between panel and the transceiver:
* UART (idle high), 5V TTL.
* 57600bps 8N1 (8 bits, no parity, 1 stop bit.
* The majority of the data exchanged between the panel & radio look to be ASCII.

#### Panel Pinout:
```
Pin  Name    Description
1    GND     Speaker ground
2    8V      Control panel power (measured 9v..)
3    GND
4    RX      TTL UART Serial (radio to control head / panel)
5    TX      TTL UART Serial (from control head / panel to radio)
6    AF      Speaker Audio
```

### Communication example between the radio and control panel
Documentation is split into two "chunks", the panel serial data and the transceiver / radio's responses etc.
Primary focus for now is to reverse engineer the panel so it can be interfaced with a "insert radio here" ;)


#### COMMAND -> DATA VALUE -> ACK

Keepalive / ACK sequence:

Panel / Radio

![ACK Sequence](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/ACK%20Sequence.png)

* "ACK to ACK" interval is 5 seconds.
* Internal "ACK" happening on the panel every 2 seconds.
* Radio ACK in poweroff state is `0xFF`, vs normal `0x0D` ACK.
* Panel will respond with `0xFF` & `0x0D` if radio ACK response is `0xFF` & `0x0D`
 
### Knobs
TODO

Values here seem to be made up of:
1. Direction.
2. Speed or increment? Or just value
3. Value


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

Volume:
```hexdump
0x56, 0x30, 0x31, 0x0D
0x56, 0x30, 0x32, 0x0D
0x56, 0x30, 0x33, 0x0D
0x56, 0x30, 0x34, 0x0D
0x56, 0x30, 0x35, 0x0D
0x56, 0x30, 0x36, 0x0D
```

SQL (Squelch): `0x57`

**RIT/XIT Knob**

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
```
0x54 0x46 0x46 0x0D

```
Right:
```
0x54 0x30 0x31 0x0D
```

If Shift:

"value" is dependent the physical speed the knob is adjusted.

**VFO knob**

From the radio side of things, the VFO knob values are really simple, they're just ASCII values of the VFO frequency.
Example moving the VFO knob, to the right, from `28.530.00` to `28.530.05`

![Frequency Change](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/Frequency%20Change.png)
```
Panel (TX)   Radio (RX)
S0001\r      ;2853001\r
S0004\r      ;2853002\r
             ;2853003\r
S0003\r      ;2853004\r
S0002\r      ;2853005\r
```

Going the other way from `28.530.05` to `28.530.00`:
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

### Buttons:

Most of the panel buttons have 3 values / states: up: `0x30`  down: `0x31` long press: `0x33`
These are coincidentally also ASCII values for 0, 1 and 3, logical right?
All button presses or commands are always suffixed with a `0x0D` or ASCII: `\r` (carriage return)

Buttons that are "single action" still will produce a button pressed & released sequence.

```hexdump
Button     Byte  Comment
CL         0x4D
XIT        0x4F
RIT        0x4E
TF-SET-U   0x51
TF-SET-D   0x50
Multi      0x54  
IF-Shift   0x58 
QMI        0x4B  Long press: M.IN
QMR        0x4C  Long press: M>VFO
A/B        0x4A  Long press: M/V
A=B        0x49  Long press: Split
Menu       0x47  Long press: F.Lock
MHz        0x48  Long press: M.STP
FIL        0x42  Long press: NAR
BC         0x43  CW.T in CW mode.
DNL        0x40
NR         0x41
Fine       0x44  Long Press: STEP
CH3        0x36
KEY        0x39
PROC       0x3B
ENT        0x3F
CH2        0x35
MIC        0x38
VOX        0x3C
AGC        0x3E
CH1        0x34
PWR        0x37
NB/T       0x3A
MTR        0x3D
On/Off     0x30
PF         0x32 
ATT/PRE    0x32
AT         0x33 Has long press state also, but not used for anything.
```

S Meter values (UART RX, from radio):
![S Meter](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/S-meter%20example.png)

Meter values are prefixed with, guess what, `0x3D` (same as the meter button, nice).
Or, alternatively in ASCII, prefixed with `=` and of course as usual ends in a carriage return `\r`
The panel doesn't do anything other than just receive the value and display it.

```
CMD   Signal Level          Carriage Return
0x3D  0x30 0x38 0x30 x30    0x0D
```
In ASCII:
```
= 0800 \r
```

Listening to CW it's possible to get short burst of strong signals and look at the logic analyzer output to compare.

S0 signal to S9 signal:
```
S0 -> 0000
S1 -> 0100
S2 -> 0200
S3 -> 0300
S4 -> 0400
S5 -> 0500
S6 -> 0600
S7 -> 0700
S8 -> 0800
S9 -> 0900
```

S9 and up:
```
1000 to 1400
```
```
S9 +    -> 1100
S9+20dB -> 1400
S9+30dB -> 1500
```
