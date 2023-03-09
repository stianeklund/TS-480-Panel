# Kenwood TS-480 Panel (Control Head)

## Input & Output
* [Panel buttons: byte & ASCII values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/Buttons.md)
* [Meter values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/S-Meter_UART-RX.md)
* [Encoder & Knobs](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/Encoders.md)
* [Display Segments etc..](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel/Display.md)


# Panel Commands

| Hex Value  | ASCII Value | Panel Reply        | Comment                                                    |
|------------|-------------|--------------------|------------------------------------------------------------|
| 0x3F       | ?           | None               | Set NB (display segment) on: `0x3F 0x90`, off: `0x3F 0x80` |
| 0x4F       | O           | X01FF V23 W00 0xFF | Reads out encoder values, IF-Shift, Volume & Squelch value |
| 0x56       | S           | None               | S0 clears any errors, S1 to S7                             |
| Long Press | 0x33        | 3                  |                                                            |
| Hold       | 0x34        | 4                  |                                                            |

---

### Menu and menu items

Menu entries look like this: `:00.P00.2E.402.@”.E€.`

Reverse engineering these values are not so simple, not knowing the memory layout or what bytes really go where..

Exiting the menu for example looks like this: `3A 20 20 0D 32 30 0D 40 84 0D 45 81 0D`

Setting the menu number segment:

| Command | Menu number (2 bytes*) | Binary    | Comment                                                               |
|---------|------------------------|-----------|-----------------------------------------------------------------------|
| 0x3a    | 0x20 0x20 0x20         | 0010_0000 | Turns off the segment entirely. *If menu item <= 10 only need 2 bytes |
| 0x3a    | 0x30 0x30              | 0011_0000 | Menu segment on, display: 00                                          |
| 0x3a    | 0x30 0x31              | 0011_0001 | Menu segment on, display: 01                                          |
| 0x3a    | 0x30 0x32              | 0011_0010 | Menu segment on, display: 01                                          |

It's pretty clear the menu items are set in firmware ase they don't rely on data from the actual radio to access.

| Entry | Command     | Menu number | Menu text      | Comment                                                          |
|-------|-------------|-------------|----------------|------------------------------------------------------------------|
| P00   | 3a 30 30 0d | 50 30 30 0d | 32 45 0d       | The last two bytes (except 0x0D ACK) is the text memory location |
| P01   | 3a 30 30 0d | 50 30 31 0d |                |                                                                  |
| P01   | 3a 30 30 0d | 52 30 0d    | 32 33 0d       |                                                                  |
| Q0    | 3a 30 30 0d | 51 30 0d    | Doesn't apply? | Q1 for twin power, this                                          |
| Q1    | 3a 30 30 0d | 51 31 0d    | Doesn't apply? | Q1 for twin power, this                                          |
| R0    | 3a 30 30 0d | 52 30 0d    | Doesn't apply  | See the commented startup section below                          |

---

### RX / TX lamp

| Address | Value | Comment     |
|---------|-------|-------------|
| 0x38    | 0x30  | RX lamp off |
| 0x38    | 0x31  | RX lamp on  |
| 0x38    | 0x32  | TX Lamp on  |


### Key Backlight illumination

| Address | Byte Value | Key Illumination |
|---------|------------|------------------|
| 0x37    | 0x30       | Off              |
| 0x37    | 0x31       | On               |


### Display brightness

Can be written without any prefixes, display brightness is just stored in memory at 36h; 0x0D needs to be suffixed after the byte value

| Address | Value | Display brightness |
|---------|-------|--------------------|
| 0x36    | 0x30  | Off                |
| 0x36    | 0x31  | 1                  |
| 0x36    | 0x32  | 2                  |
| 0x36    | 0x33  | 3                  |
| 0x36    | 0x34  | 4                  |

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
:        <-- 0x3A Menu items might be prefixed by this?
R0       <-- Read memory / entry 0
20       <-- Clear menu item number segment, turn segment off
61       <-- Set display brightness on panel (60 to 64)
71       <-- Key backlight on
80       <-- Set RX / TX LED to RX (Green)
94       <-- Set mode (USB LSB, CW etc)
;4077620 <-- Set VFO A / display frequency
<        <-- Clear XIT / SPLIT frequency on display
=0000    <-- Clear meters etc.
>73      <-- Show SWR meter

// TODO Document display segments
Set display segments:
?ˆ       <-- 0x63 Dispay Segment ATT/ PRE etc..
@„       <-- 0x64 (VOX ON / @ˆ (VOX OFF)
A€       <-- Address: 0x41: Set AGC trailing value is bit value for AGC, e.g 0x41 0x80 (off)
B     <-- Address: 0x42: for BC / NC segments
CÀ       <-- Address: 0x43: Segments for PC symbol, rightmost column
D     <-- Address: 0x44: VFO A/B arrow indicator, split etc
E     <-- Address: 0x45: Also VFO A/B indicator arrows
F‰€€     <-- Address: 0x46: Sets the little frequency decimals dots both for VFO and XIT / Split frequency e.g: 46 89 80 80 0d to clear
L€       <-- Address: 0x4c: Not sure what this does, some of these values cause the VFO A indicator to blink but could be overflowing

Meter values:
=0300
=0000

:  
20  <-- Exits the menu, clear the area menu items are shown // Exit menu? / VFO mode
61  <-- Display brightness
71  <-- Backlight
;4077620 <- VFO Freq
<        
=0000 <-- Clear Meters                                                                         * <-- bits 0 and 1 indicate pwr status on HX model
Q0   <-- Model / status? Text shown on bootup: Q0 = RX Only, Q1 = Twin PWR. 0b0101_0000 0011_0000
O  <-- Read out XIT, Volume & Squelch                                                 ^-- last bit set for HX models?         
31 <--  Unit seperator?
81 <-- Turn LED on (Green, RX), 82 = TX, e.g: 38 32 0D 33 33 0D 33 31 0D to turn on
S0  <-- Segment for error codes Error codes etc, S1 to S7, clear error code
ÿ
ÿ
```