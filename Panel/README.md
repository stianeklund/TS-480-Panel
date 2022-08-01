# Kenwood TS-480 Panel (Control Head)

## Input & Output
* [Panel buttons: byte & ASCII values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/Buttons.md)
* [Meter values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/S-Meter_UART-RX.md)
* [Encoder & Knobs](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/Encoders.md)
* [Display Segments etc..](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/Display.md)

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

| Entry       | Description text | Command                 |          |
|-------------|------------------|-------------------------|----------|
| :07         | P07              | 2  E  4  0  0  @  ”     | E €      |
| 3A 30 37 0D | 50 30 37 0D      | 32 45 34 30 30 40 94 0D | 45 80 0D |

---

### Turn on RX / TX lamp

#### Address: `0x38` (ASCII: `8`):

| Byte Value | Comment     |
|------------|-------------|
| 0x30       | RX lamp off |
| 0x31       | RX lamp on  |
| 0x32       | TX Lamp on  |


### Key Backlight illumination

#### Address: `0x37` (ASCII: `7`):

| Byte Value | Comment                        |
|------------|--------------------------------|
| 0x30       | Key backlight illumination off |
| 0x31       | Key backlight illumination on  |
| 0x80       | Turns off ANT segment          |
| 0x82       | NR1                            |
| 0x84       | NR2                            |


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
81 <-- Turn LED on (Green, RX), 82 = TX, e.g: 38 32 0D 33 33 0D 33 31 0D to turn on
S0  <-- Segment for error codes Error codes etc, S1 to S7, clear error code
ÿ
ÿ
```