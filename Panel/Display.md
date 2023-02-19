
### Display Segments:

These values have been found by capturing data sent from the radio to the display unit / panel.
Need to work on this because the layout of this isn't very easy to read.
Furthermore depending on the segment that is being activated previous row values affect the byte value needed to turn on or clear an icon.
(Should reference the service manual here..)


The display is driven by two LCD drivers: IC1 and IC2 which both are: `LC758237W` by Sanyo.
The display has a 7 segment and a 13 segment panel according to the service manual.


#### Segment `0x32` (ASCII: `2`)
| Byte Value | Comment  | Setting                                             |
|------------|----------|-----------------------------------------------------|
| 0x4E       | MIC      | `401-464` (0-100)                                   |
| 0x35       | FIL      | `400-40D` (1000-5000) 0x34, 0x30, 0x30              |
| 0x4F       | MIC GAIN | `400-464` (0-100)                                   |
| 0x50       | KEY      | `400-432` (10-60)                                   |
| 0x51       | PWR      | `0x40` `0x405-4C8` (increments of 5), `464` is 100W |
| 0x52       | Delay    | `400-414` (0-100)                                   |
| 0x5E       | TX MONI  | `401-464` (0-100)                                   |


#### Filter values
| Address | Select byte | Filter   | Value range                                                              |
|---------|-------------|----------|--------------------------------------------------------------------------|
| 0x32    | 0x34        | WDH      | `401-464` (0-100) Note this address line is also used for reading menu's |
| 0x32    | 0x35        | HI       | `401-464` (0-100)                                                        |
| 0x35    | 0x36        | LO       | `400-40D` (1000-5000) 0x34, 0x30, 0x30                                   |

#### Menu numeric values, e.g: filter width etc
| Address    | Select byte | Filter   | Value range                                                              |
|------------|-------------|----------|--------------------------------------------------------------------------|
| 0x34, 0x30 | 0x34        | WDH      | `401-464` (0-100) Note this address line is also used for reading menu's |
| 0x32, 0x30 | 0x35        | HI       | `401-464` (0-100)                                                        |
| 0x35, 0x30 | 0x36        | LO       | `400-40D` (1000-5000) 0x34, 0x30, 0x30                                   |

#### Segment `0x40` `0100_0000`

Please see the Kenwood TS-480 manual page 13 for reference
Each name of these icons are named by the number they are referenced by in the manual.

`0100_0000`

| Location | Value | Binary    | Comment                            | Ref |
|----------|:------|-----------|------------------------------------|-----|
| 0x40     | 0x80  | 1000_0000 | Off                                | _   |
| 0x40     | 0x81  | 1000_0001 | Inverse star: Quick Menu List (16) | 16  |
| 0x40     | 0x82  | 1000_0010 | Star: no function (9)              | 9   |
| 0x40     | 0x84  | 1000_0100 | ANT 1                              | 3   |
| 0x40     | 0x88  | 1000_1000 | ANT 2                              | 3   |
| 0x40     | 0x90  | 1001_0000 | Menu                               | 8   |
| 0x40     | 0xA0  | 1010_0000 | M.CH                               | 23  |
| 0x40     | 0xC0  | 1100_0000 | M.SCR                              | 24  |


#### Segment `0x3E` (ASCII: `>`)
    

SWR / METER segment is two bytes

``` 
0x3E      0x37      0x31
0011_1110 0011_0111 0011_0001
```

| Location | Offset | Value | Comment               |
|----------|--------|-------|-----------------------|
| 0x3E     | 0x37   | 0x30  | SWR / ALC OFF         |
| 0x3E     | 0x37   | 0x31  | ALC                   |
| 0x3E     | 0x37   | 0x30  | COMP                  |
| 0x3E     | 0x37   | 0x30  | SWR                   |
| 0x3E     | 0x30   | 0x30  | Turns off ANT segment |


#### Segment `0x3F` (Binary: 0011_1111):

| Location | Value | Binary    | Comment  |
|----------|:------|-----------|----------|
| 0x3F     | 0x80  | 1000_0000 | Off      |
| 0x3F     | 0x81  | 1000_0001 | PRE      |
| 0x3F     | 0x82  | 1000_0010 | ATT      |
| 0x3F     | 0x84  | 1000_0100 | PROC     |
| 0x3F     | 0x88  | 1000_1000 | VOX      |
| 0x3F     | 0x90  | 1001_0000 | NB       |
| 0x3F     | 0xA0  | 1010_0000 | MHz (On) |
| 0x3F     | 0xC0  | 1100_0000 | Fine     |

#### Segment `0x3B` (Ascii: `;` Binary: 0011_1011):
Address location of the main frequency display, or in menu mode it shows the parameters.
7 segment display.

| Location | Value        | Comment                                               |
|----------|:-------------|-------------------------------------------------------|
| 0x3B     | Alphanumeric | 7 Characters, but doesn't support every character A-Z |

E.g: ";0000000\r" will set the display to all 0's.


SEND Button:  `0x38` `0x32`

* Send ON: `38 32 0D 33 33 0D 3D 30 33 30 31 0D 33 31 0D`
* Send OFF: `38 30 33 33 0D 3D 30 30 30 30 0D 33 31 0D 38 31 0D`

---

#### Segment `0x41` (Binary: 0100_0001):

| Location | Value | Binary    | Comment (Manual ref)                                           |
|----------|:------|-----------|----------------------------------------------------------------|
| 0x41     | 0x80  | 1000_0000 | Off                                                            |
| 0x41     | 0x81  | 1000_0001 | AGC (12)                                                       |
| 0x41     | 0x82  | 1000_0010 | or (unknown haven't seen any reference to this in the manuals) |
| 0x41     | 0x84  | 1000_0100 | -F for AGC-F but AGC flag needs to be set (12)                 |
| 0x41     | 0x88  | 1000_1000 | NAR (13)                                                       |
| 0x41     | 0x90  | 1001_0000 | 2   (13)                                                       |
| 0x41     | 0xA0  | 1010_0000 | LOCK - Tuning control lock (18)                                |
| 0x41     | 0xC0  | 1100_0000 | Constant recording (10)                                        |


#### Address: `0x43` (ASCII: `C`):

| Location | Value | Binary    | Comment |
|----------|-------|-----------|---------|
| 0x43     | 0x80  | 1000_0000 | Off     |
| 0x43     | 0x81  | 1000_0001 | T (36)  |
| 0x43     | 0x82  | 1000_0010 | CT      |
| 0x43     | 0x84  | 1000_0100 | LOCK    |
| 0x43     | 0x88  | 1000_1000 | PC      |
| 0x43     | 0x90  | 1001_0000 | RIT     |
| 0x43     | 0xA0  | 1010_0000 | XIT     |
| 0x43     | 0xC0  | 1100_0000 | Auto    |

#### Address: `0x44`

| Location | Value | Binary    | Comment |
|----------|-------|-----------|---------|
| 0x44     | 0x80  | 1000_0000 | Off     |
| 0x44     | 0x81  | 1000_0001 | A       |
| 0x44     | 0x82  | 1000_0010 | B       |
| 0x44     | 0x84  | 1000_0100 | M       |
| 0x44     | 0x88  | 1000_1000 | SPLIT   |
| 0x44     | 0x90  | 1001_0000 | >T      |
| 0x44     | 0xA0  | 1010_0000 | <R      |
| 0x44     | 0xC0  | 1100_0000 | AT      |

#### Address: `0x45`

| Location | Value | Binary    | Comment         |
|----------|-------|-----------|-----------------|
| 0x45     | 0x80  | 1000_0000 | Arrow Off       |
| 0x45     | 0x81  | 1000_0001 | <- Top arrow    |
| 0x45     | 0x82  | 1000_0010 | <- Middle arrow |
| 0x45     | 0x84  | 1000_0100 | <- Bottom arrow |
| 0x45     | 0x88  | 1000_1000 | -> Top arrow    |
| 0x45     | 0x90  | 1001_0000 | -> Middle arrow |
| 0x45     | 0xA0  | 1010_0000 | -> Bottom arrow |
| 0x45     | 0xC0  | 1100_0000 | Unknown         |

Turning XIT on to 1760 above QRG, `<  0  0  0  0  1  7  6  0`
```
3C 20 20 20 20 31 37 36 30 0D  <-- Frequency first
43 E8 0D                       <-- Enables XIT icon
46 89 81 80 0D                 <-- Turns on decimal
```

#### Address: `0x45`

| Location | Value | Binary    | Comment         |
|----------|-------|-----------|-----------------|
| 0x45     | 0x80  | 1000_0000 | Arrow Off       |
| 0x45     | 0x81  | 1000_0001 | <- Top arrow    |
| 0x45     | 0x82  | 1000_0010 | <- Middle arrow |
| 0x45     | 0x84  | 1000_0100 | <- Bottom arrow |
| 0x45     | 0x88  | 1000_1000 | -> Top arrow    |
| 0x45     | 0x90  | 1001_0000 | -> Middle arrow |
| 0x45     | 0xA0  | 1010_0000 | -> Bottom arrow |
| 0x45     | 0xC0  | 1100_0000 | Unknown         |


---


### Showing all segments

Section below sets up everything to show the service menu but it isn't shown.
Maybe the service menu is stored on the actual radio not the panel?

See Service menu comments for more info.
```
0x3B 0x05 0xFF 0x00 0xFF 0xF4 0xF7 0x0d
0x3A 0x37 0x38 0x0D
0x50 0x34 0x45 0x0D
0x3B 0x00 0xFF 0xDC 0x02 0xFD 0xDF 0x01 0x0D
0x3D 0x31 0x32 0x30 0x30 0x0D
0x3D 0x31 0x31 0x30 0x30 0x0D <-- some sort of "keepalive"

```
