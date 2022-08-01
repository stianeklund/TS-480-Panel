
### S Meter
S Meter values (UART RX, from radio):
![S Meter](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/S-meter%20example.png)

Meter values are prefixed with, guess what, `0x3D` (same as the meter button, nice).
Or, alternatively in ASCII, prefixed with `=` and of course as usual ends in a carriage return `\r`
The panel doesn't do anything other than just receive the value and display it.

Valid range is `0000` to `2000` ASCII, S-Meter has a total of 20 "bars".
`0100` is _one_ bar, `0200` two bars etc, s1 is 3 bars.

![S Meter](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/s-meter-pic.png)

Example

```
CMD   Signal Level          Carriage Return
0x3D  0x30 0x38 0x30 x30    0x0D
```
In ASCII:
```
= 0800 \r
```

![SWR Meter](https://raw.githubusercontent.com/stianeklund/TS-480-Panel/main/screenshots/swr-meter-pic.png)

SWR meter values are the last digit in `=0000` where the digit represents the amount of bars lit for the SWR meter.
Valid range is `=0001` to `=0010`

Please see S-Meter picture above for reference.

S0 signal to S9 signal:
```
S-Level        ASCII Values
S0          -> 0000 .. 0200 (range)?
S1          -> 0100 .. 0300 (range)?
S2          -> 0400
S3          -> 0500
S4          -> 0600
S5          -> 0700
S6          -> 0800
S7          -> 0900
S8          -> 1000
S9          -> 1100
S9 +10dB    -> 1200
S9 +20dB    -> 1300
S9 +25dB    -> 1400
S9 +30dB    -> 1500
S9 +40dB    -> 1600
S9 +45dB    -> 1700
S9 +50dB    -> 1800
S9 +55dB    -> 1900
S9 +60dB    -> 2000
```

SWR Meter:
Valid range `=2F01..=2FFF`

VFO A/B:
;1572910 <-- VFO A Frequency
=0000
D⸮
E⸮
;1527000 <-- VFO B Frequency
=0000
D⸮
E⸮
⸮
