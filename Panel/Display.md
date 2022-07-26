
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


### Showing all segments

See Service menu comments for more info.
```
0x3B 0x05 0xFF 0x00 0xFF 0xF4 0xF7 0x0d
0x3A 0x37 0x38 0x0D
0x50 0x34 0x45 0x0D
0x3B 0x00 0xFF 0xDC 0x02 0xFD 0xDF 0x01 0x0D
0x3D 0x31 0x32 0x30 0x30 0x0D
0x3D 0x31 0x31 0x30 0x30 0x0D <-- some sort of "keepalive"

```
