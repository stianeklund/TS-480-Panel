## Kenwood TS-480 transceiver power-on sequence

Data dumped from the transceiver (radio) RX UART to the panel:

Some modification of this data has been made to improve readability, trailing carriage return etc has been stripped out.
This is all data on the RX channel from the transceiver to the panel, i.e. panel "input" commands are not shown here.

```
01 01                response to display radio is turning on, or on?
:R0                  : <- NB/T button (R is 0x52, could be setting entry 63, Mic PF4 Key Assignment?)
20 61 71             Some response, 20 464 is mic gain 
80 95                Modes? 94 FM mode, 96 FSK, 93 CW, 95 AM, 91 LSB, 92 USB
; VFO A Frequency <  Frequency response from radio is always prefixed with ;

=0000 >              =NUMERIC_VALUE is transceiver's response to a mode change?
73                   Show SWR meter (also will show if setting mic gain as AGC will be shown in stead of SWR on the display)
?.                   ATT/PRE button transceiver response?
@.                   Transceiver response to antenna change? Reply to 0x21?
A.B.C.D.E.F...       Could be VFO options or simply NR, Filter values, A/B split etc? Can't assume panel values mean the same from the radio..
L.                   0x4D 0x2E QMR?

Q0                   Q0 TF-Set (Down in frequency) non pressed. Maybe 

These could be just panel S meter values?
=2000                
=1200                Can change value on startup
=0700
=0300

This is repeated quite a bit, not sure what this really means.
Most frequency changes end with <

=0000                
:20 61 71
;VFO A Frequency <

=0000                Can be a different value, e.g 1200
:20 61 71
;VFO A Frequency <

=0000
:20 61 71
;VFO A Frequency <

=0000
:20 61 71
;VFO A Frequency <

=0000
:20 61 71
;VFO A Frequency <

=0000
:20 61 71
;VFO A Frequency <

=0000
:20 61 71
;VFO A Frequency <

=0000
:20 61 71
;VFO A Frequency <

=0000
:20 61 71
;VFO A Frequency <

=0000
:20 61 71
;VFO A Frequency<

=0000
:20 61 71
;VFO A Frequency <

=0000
:20 61 71
;VFO A Frequency <

=0000
O 31 81              O (ON)? 0x30 is power button, 0x31 pressed state
S0
```
Panel reply:

```
               IF Shift value
V2A     X01FF              Volume value
W00
```

Panel power on sequence:
```
name ascii hex
TX   \r    0x0D  
TX   0     0x30
TX   1     0x31
TX   \r    0x0D
TX   \r    0x0D
TX   xFF   0xFF
TX   \r    0x0D
TX   X     0x58
TX   0     0x30
TX   2     0x32
TX   0     0x30
TX   1     0x31
TX   \r    0x0D
TX   V     0x56
TX   1     0x31
TX   B     0x42
TX   \r    0x0D
TX   W     0x57
TX   0     0x30
TX   0     0x30
TX   \r    0x0D
TX   xFF   0xFF
TX   \r    0x0D
```

## Differences in power on sequence with serial connected

If the TS-480 is connected to a PC via RS232, for example through ARCP-480, and power on is initiated from the PC side, the sequence is a little different.
From what I can tell the RX line  (Radio to Panel) is the same, but TX (Panel to Radio) is slightly different.

Normally when pressing power on from the panel the RX line would see:
```hex
hex: 0x30 0x31 0x0D
ascii: 0 1 \r
```
However if the radio is turned _on_ from the PC side, i.e. initiated over RS232 the RX line will see:
```hex
hex: 0x30 0x32 0x0D
ascii: 0 2 \r
```
