# Kenwood TS-480 Panel (Control Head)

## Input & Output
* [Panel buttons: byte & ASCII values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\Buttons.md)
* [Panel Meter values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\S-Meter_UART-RX.md)
* [Encoder & Knobs](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\Encoders.md)

Set VFO info / memory channel xit rit values?
`=0000$0D` indicates that VFO A = B or is command VFO A=B?

# Panel Commands

| Hex Value  | ASCII Value | Panel Reply        | Comment                                                    |
|------------|-------------|--------------------|------------------------------------------------------------|
| 0x3F       | ?           | None               | Set NB (display segment) on: `0x3F 0x90`, off: `0x3F 0x80` |
| 0x4F       | O           | X01FF V23 W00 0xFF | Reads out encoder values, IF-Shift, Volume & Squelch value |
| 0x56       | S           | None               | S0 clears any errors, S1 to S7                             |
| Long Press | 0x33        | 3                  |                                                            |
| Hold       | 0x34        | 4                  |                                                            |

---

### Menu
Menu entries look like this: 

| Menu Entry | Radio Response                       | Comment | 
|------------|--------------------------------------|---------|
| *          | Read memory at position .402.@”.E€.` | :00     | 
| 00         | `:00.P00.2E.402.@”.E€.`              | :00     | 
| 01         | ANT 2 -> 1                           | 01      | 
| 02         | Turns off ANT segment                | 02      | 
`:00.P00.2E.402.@”.E€.`

Exit menu: 3A 20 20 0D 32 30 0D 40 84 0D 45 81 0D

```
Entry       | Description text | 
:07         | P07              | 2  E  4  0  0  @  ”     | E €     |
3A 30 37 0D | 50 30 37 0D      | 32 45 34 30 30 40 94 0D | 45 80 0D

```

---


### Display Segments:

These values have been found by capturing data sent from the radio to the display unit / panel.
Need to work on this because the layout of this isn't very easy to read.
Furthermore depending on the segment that is being activated previous row values affect the byte value needed to turn on or clear an icon.
(Should reference the service manual here..)

Columns seem to be increments of 2 bits within sections, passing sections the increment is 8 bits.
TODO: It's seems fairly standard.. Just don't recall, multiplexing may play into this.

#### Segment `0x32` (ASCII: `2`)
| Byte Value | Comment  | Setting                                             |
|------------|----------|-----------------------------------------------------|
| 0x4E       | MIC      | `401-464` (0-100)                                   |
| 0x4F       | MIC GAIN | `400-464` (0-100)                                   |
| 0x50       | KEY      | `400-432` (10-60)                                   |
| 0x51       | PWR      | `0x40` `0x405-4C8` (increments of 5), `464` is 100W |
| 0x52       | Delay    | `400-414` (0-100)                                   |
| 0x5E       | TX MONI  | `401-464` (0-100)                                   |


34 30 35

#### Segment `0x3A` (ASCII: `I`)
| Byte Value | Comment               |
|------------|-----------------------|
| 0x30       | Menu 2                |
| 0x88       | ANT 1 -> 2            |
| 0x84       | ANT 2 -> 1            |
| 0x80       | Turns off ANT segment |


#### Segment `0x3E` (ASCII: `>`)
| Byte Value | Comment               |
|------------|-----------------------|
| 0x37 0x30  | SWR / ALC OFF         |
| 0x37 0x31  | MTR (ALC)             |
| 0x37 0x32  | MTR (COMP)            |
| 0x37 0x33  | MTR (SWR)             |
| 0x88       | ANT 1 -> 2            |
| 0x84       | ANT 2 -> 1            |
| 0x80       | Turns off ANT segment |

#### Segment `0x3F` (ASCII `?`):

| Byte Value | Comment  |
|:-----------|----------|
| 0x80       | Off      |
| 0x81       | PRE      |
| 0x82       | ATT      |
| 0x85       | PROC     |
| 0x88       | VOX (On) |
| 0xB8       | VOX      |
| 0xA8       | NB (Off) |
| 0xA0       | NB (On)  |
| 0xA2       | MHz (On) |
| 0xB0       | NB       |
| 0xB8       | NB       |
| 0xC2       | Fine     |

* Long press: `0x32 0x4D 0x0D 0x34 0x38 0x37 0x0D`
    * Deselect `0x32 0x30`

SEND Button:  `0x38` `0x32`

* Send ON: `38 32 0D 33 33 0D 3D 30 33 30 31 0D 33 31 0D`
* Send OFF: `38 30 33 33 0D 3D 30 30 30 30 0D 33 31 0D 38 31 0D`

This is probably redundant, it's covered elsewhere, this doesn't engage the TX relay. 
```
38 32 0D           <-- Enable TX lamp
33 33 0D  
3D 30 33 30 31 0D  <-- S Meter
33 31 0D           <-- 
38 0D 
30 0D
33 33 0D
3D 30 30 30 30 0D <-- Clear S meter & SWR meter
33 31 38 31 0D  <-- 
```

---

#### Address: `0x40` (ASCII: `@`):

| Byte Value | Comment               |
|------------|-----------------------|
| 0x88       | ANT 1 -> 2            |
| 0x84       | ANT 2 -> 1            |
| 0x80       | Turns off ANT segment |
| 0x82       | NR1                   |
| 0x84       | NR2                   |

#### Address: `0x41` (ASCII: `A`):

| Byte Value | Comment |
|------------|---------|
| 0x87       | AGC OFF |
| 0x85       | AGC-F   |
| 0x81       | AGC     |
| 0x82       | NR1     |
| 0x84       | NR2     |

#### Address: `0x43` (ASCII: `C`):

| Byte Value | Comment               |
|------------|-----------------------|
| 0x80       | Column? off           |
| 0xC0       | Off                   |
| 0xC8       | PC                    |
| 0xD0       | XIT Off, leave RIT on |
| 0xE0       | RIT Off, leave XIT on |
| 0x82       | CT                    |
| 0x83       | CT                    |
| 0x84       | LOCK on               |
| 0x84       | LOCK on               |
| 0xC8       | XIT (Off)             |
| 0xE8       | XIT (On)              |
| 0xE8       | XIT (On)              |

Turning XIT on to 1760 above QRG, `<  0  0  0  0  1  7  6  0`
```
3C 20 20 20 20 31 37 36 30 0D  <-- Frequency first
43 E8 0D                       <-- Enables XIT icon
46 89 81 80 0D                 <-- Turns on decimal
```

#### Address: `0x46` (ASCII: `.`):

| Byte Value | Comment               |
|------------|-----------------------|
| 0x88       | ANT 1 -> 2            |
| 0x84       | ANT 2 -> 1            |
| 0x80       | Turns off ANT segment |
| 0x82       | NR1                   |
| 0x84       | NR2                   |


---

# Init / startup sequence
This is data that the panel receives from the radio. 
Time between power on (01), until the radio starts sending the rest of the data, `: R0..etc` always takes 5 seconds. No indication that it relies on any ACK.
Might just be a hardcoded 5 second timer value to make sure the display is ready to receive? (I've checked with with a logic analyzer to be sure).

At the very tail end of the transmission:
`02` is used to reconnect, e.g disconnecting the panel from the radio & reconnecting the panel will send 02 and receive a `00` if off `01` if powered, `01` will then resend the display info.

```
.00...01.01.
:  .R0.20.64.71.80.93.
;2491113
.<        .=0000.
>73.
?ˆ
.@ˆ

.A‡.B€.CÀ.Dƒ.E.F‰€€.L€.
:  .20.64.71.;2491113.<        .=0000.
:  .20.64.71.;2491113.<        .=0000.
:  .20.64.71.;2491113.<        .=0000.CÈ.
:  .20.64.71.;2491113.<        .=0000.
:  .20.64.71.;2491113.<        .=0000.
:  .20.64.71.;2491113.<        .=0000.
:  .20.64.71.;2491113.<        .=0000.Q1.
:  .20.64.71.;2491113.<        .=0000.
:  .20.64.71.;2491113.<        .=0000.
:  .20.64.71.;2491113.<        .=0000.O.31.81.S0.ÿ.ÿ.
```

### Commented startup:
```
01       <-- Turn on. 
:        <-- 0x3A NB?
R0       <-- Read memory / entry 0
20       <-- Exit Menu / Memory Mode or enter VFO mode? (Seems to be used to exit a submenu or long press state)
61       <-- Set display brightness on panel
71       <-- Unknown
80       <-- Set squelch value
94       <-- Set mode (USB LSB, CW etc)
;4077620 <-- Set display frequency
<         
=0000    <-- Clear meters etc.
>73      <-- Show SWR meter

Set display segments:
?ˆ       <-- 0x63 Dispay Segment ATT/ PRE etc..
@„       <-- 0x64 (VOX ON / @ˆ (VOX OFF)
A€       <-- A 0x80 (Section of display where AGC etc is shown)
B     <-- B 0x81 (Can also be B 0x80)
CÀ       <-- C 0x83 (Can also be C 0xC8
D     <-- D 0x81
E     <-- E 0x81
F‰€€     <-- F 0x89 0x80 0x80 <-- Squelch
L€       <-- L 0x80

Meter values:
=0300
=0000

:  
20  <-- Exit menu? / VFO mode
61  <-- Display brightness
71  <-- unknown
;4077620 <- VFO Freq
<        
=0000 <-- Clear Meters
Q0    <-- RX Only, Q1 = Twin Power (Not sure what is valid for 480-SAT, probably the same but different txt stored in firmware?)
O  <-- Read out XIT, Volume & Squelch
31 <--  Unit seperator?
81 <-- Turn LED on (Green, RX), 82 = TX
S0  <-- Segment for error codes Error codes etc, S1 to S7, clear error code
ÿ
ÿ
```