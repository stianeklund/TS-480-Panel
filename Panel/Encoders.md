## Encoders & Knobs

### RIT/XIT Knob

ASCII value: U0 (speed): 1 (right) / UF (speed): F (left)

**Left**

| Cmd  | State | Value |
|------|-------|-------|
| 0x55 | 0x46  | 0x46  |
| 0x55 | 0x46  | 0x44  |
| 0x55 | 0x46  | 0x45  |
| 0x55 | 0x46  | 0x46  |

**Right**

| Cmd  | State | Value |
|------|-------|-------|
| 0x55 | 0x30  | 0x31  |
| 0x55 | 0x30  | 0x32  |
| 0x55 | 0x30  | 0x34  |
| 0x55 | 0x30  | 0x35  | 
| 0x55 | 0x30  | 0x33  |


### Multi & IF-Shift knob

Multi left:
In ASCII T+hexvalue (but sent as ascii..), e.g T01, T20

| Command | Value |      |
|---------|-------|------|
| 0x54    | 0x46  | 0x46 |

Right:

| Command | Value |      |
|---------|-------|------|
| 0x54    | 0x30  | 0x31 |


### Volume 
| Command | Value |      |
|---------|-------|------|
| 0x56    | 0x30  | 0x31 | 
| 0x56    | 0x30  | 0x32 |
| 0x56    | 0x30  | 0x33 |
| 0x56    | 0x30  | 0x34 |
| 0x56    | 0x30  | 0x35 |
| 0x56    | 0x30  | 0x36 |

### SQL (Squelch):

* Valid values are `W00`  to `WFF`

| Command | Value    |           |
|---------|----------|-----------|
| 0x57    | 0x30-x46 | 0x31-0x46 | 

---

### IF Shift
Valid range `X0000-X03FE`

Left

| Command | Value       |            |
|---------|-------------|------------|
| 0x58    | 0x30, 0x33, | 0x46, 0x42 | 
| 0x58    | 0x30, 0x33, | 0x46, 0x37 | 
| 0x58    | 0x30, 0x33, | 0x46, 0x33 | 
| 0x58    | 0x30, 0x33, | 0x45, 0x42 | 
| 0x58    | 0x30, 0x30  | 0x30, 0x31 |


The "value" is dependent the physical speed the knob is adjusted?

---

## VFO

Example moving the VFO knob, to the right, from `28.530.00` to `28.530.05`

Changing frequency, panel will notify radio of VFO encoder value, radio will transmit new frequency back
![Frequency Change](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/Frequency%20Change.png)

![VFO Frequency](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/VFO%20Frequency.png)

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
