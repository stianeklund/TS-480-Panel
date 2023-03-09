## Encoders & Knobs

![Frequency Change](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/Frequency%20Change.png)

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

---

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

# VFO Encoder values


#### Changing frequency, panel will notify radio of VFO encoder value, radio will transmit new frequency back

![VFO Frequency](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/VFO%20Frequency.png)

Panel "commands" to adjust VFO frequency:

Could these values be grey code?

For example, counter clockwise rotation value `SFFFE`

Value: `0x53 0x46 0x46, 0x46, 0x45`

If we break down this value and look 

| Value | Binary       |      |
|-------|--------------|------|
| 0x53  | 0b_0011_0101 | 0x35 |
| 0x46  | 0b_0010_1110 | 0x2e |
| 0x46  | 0b_0010_1110 | 0x2e |
| 0x46  | 0b_0010_1110 | 0x2e |
| 0x45  | 0b_0010_1101 | 0x2d |


Bit 4 also is not set if clockwise, but set for counter clockwise movement

Clockwise rotation:

Value:`S000C` `0x53, 0x30, 0x30, 0x30, 0x43`

| Value | Binary       |      |
|-------|--------------|------|
| 0x53  | 0b_0011_0101 | 0x35 |
| 0x30  | 0b_0001_1110 | 0x1e | 
| 0x30  | 0b_0001_1110 | 0x1e | 
| 0x30  | 0b_0001_1110 | 0x1e | 
| 0x43  | 0b_0010_1011 | 0x2b | 


### Clockwise rotation with a base frequency of `28.500.000`

| Panel (To Radio) | Increment (Decimal) | *Resulting Frequency |
|------------------|---------------------|:---------------------|
| S0010            | 80                  | 28.500.080           |
| S0020            | 160                 | 28.500.160           |
| S0030            | 240                 | 28.500.240           |
| S0040            | 360                 | 28.500.360           |
| S0050            | 400                 | 28.500.400           |
| S0060            | 480                 | 28.500.480           |
| S0070            | 560                 | 28.500.560           |

*assuming start from base frequency above.

The amount of values received from the radio depends, however there is a possibility that injecting data like I am doing here can cause data loss.
The panel VFO values are octal? (or at least seemingly base 10), and the resulting increment is in decimal.
The octal (base 10) values might be a coincidence, that the increments simply fit..

Counter clockwise rotation is a bit more difficult, it may be that this is stored in a 16 bit register, and the resulting values may be signed?


| Panel (To Radio) | Decrement (Decimal) | *Resulting Frequency |
|------------------|---------------------|----------------------|
| SFFF0            | 80                  | 28.499.920           |
| SFFE1            | 160                 | 28.499.840           |
| SFFD1            | 240                 | 28.499.760           |
| SFFC1            | 360                 | 28.499.680           |
| SFFB1            | 400                 | 28.499.600           |
| SFFA1            | 480                 | 28.499.520           |
| SFF91            | 560                 | 28.499.440           |


It's possible to go crazy with the encoder values, indicating that it's tied to a 16-bit register, not 8-bit:

| Encoder value | Resulting increment |
|---------------|---------------------|
| S8000         | -163.84kHz          |
| S7999         | 155.65kHz           |
| S7000         | 143.36 kHz          |
| S5800         | 112.65 kHz          |
| S2400         | 46.08 KHz           |
| S1900         | 32.00 KHz           |
| S1400         | 25.60 KHz           |
| S1000         | 20.48 KHz           |
| S0900         | 11.52 KHz           |
| S0800         | 10.24 KHz           |
| S0700         | 8.96 KHz            |
| S0600         | 7.69 KHz            |
| S0500         | 6.41 KHz            |
| S0400         | 5.12 KHz            |
| S0300         | 3.85 KHz            |
| S035F         | 432                 |
| S034F         | 432                 |
| S034F         | 424                 |
| S033F         | 416                 |
| S032F         | 408                 |
| S031F         | 400                 |
| S030F         | 392                 |
| S02FF         | 384                 |
| S02EF         | 376                 |
| S02DF         | 368                 |
| S02CF         | 360                 |
| S02BF         | 352                 |
| S02AF         | 344                 |
| S029F         | 336                 |
| S028F         | 328                 |
| S027F         | 320                 |
| S026F         | 312                 |
| S025F         | 304                 |
| S024F         | 296                 |
| S023F         | 288                 |
| S022F         | 280                 |
| S021F         | 272                 |
| S020F         | 264                 |
| S0200         | 2.56 KHz            |
| S0100         | 1.28 KHz            |
| S01FF         | 256                 |
| S01EF         | 248                 |
| S01DF         | 240                 |
| S01CF         | 232                 |
| S01BF         | 224                 |
| S01AF         | 216                 |
| S019F         | 208                 |
| S018F         | 200                 |
| S017F         | 192                 |
| S016F         | 184                 |
| S015F         | 176                 |
| S014F         | 168                 |
| S013F         | 160                 |
| S012F         | 160                 |
| S012F         | 152                 |
| S011F         | 144                 |
| S010F         | 136                 |
| S00FF         | 128                 |
| S00EF         | 120                 |
| S00DF         | 112                 |
| S00CF         | 104                 |
| S00BF         | 96                  |
| S00AF         | 88                  |
| S00A7         | 84                  |
| S009F         | 80                  |
| S0090         | 72                  |
| S0080         | 64                  |
| S0070         | 56                  |
| S0060         | 48                  |
| S0050         | 40                  |
| S0040         | 32                  |
| S0030         | 24                  |
| S0020         | 16                  |
| S0017         | 12                  |
| S0016         | 11                  |
| S0015         | 11                  |
| S0014         | 10                  |
| S0013         | 9.9?                |
| S0012         | 9                   |
| S0011         | 9                   |
| S0010         | 8                   |
| S0009         | 5                   |
| S0008         | 4                   |
| S0007         | 4                   |
| S0006         | 3                   |
| S0005         | 3                   |
| S0004         | 2                   |
| S0003         | 2                   |
| S0002         | 1**                 |
| S0001         | 1*                  |


1* (doesn't always register, even when manually rotating the vfo slowly)
** always increments 1.
