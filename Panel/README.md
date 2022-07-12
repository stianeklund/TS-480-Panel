# Kenwood TS-480 Panel (Control Head)

## Input & Output
* [Panel buttons: byte & ASCII values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\Buttons.md)
* [Panel Meter values](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\S-Meter_UART-RX.md)
* [Encoder & Knobs](https://github.com/stianeklund/TS-480-Panel/blob/main/Panel\Encoders.md)

Set VFO info / memory channel xit rit values?
`=0000$0D` indicates that VFO A = B or is command VFO A=B?

```
.20.61.71.;5025935.<        .=0000.CÈ.:  
.20.61.71.;5025935.<        .=0000.:  
.20.61.71.;5025935.<        .=0000.:  
.20.61.71.;5025935.<        .=0000.Q0.:  
.20.61.71.;5025935.<        .=0000.:  
.20.61.71.;5025935.<        .=0000.:  
.20.61.71.;5025935.<        .=
```

### Display Segments:
ANT1 / ANT2 select: `0x40 0x84 0x0D / 0x40 0x88 0x0D`
```
ANT1: @„. 0x40 0x84 0x0D
ANT2: @ˆ. 0x40 0x88 0x0D
NR1 / NR2: 0x42 0x82 0x0D / 0x42 0x84 0x0D (off 0x80)
LOCK     : 0x43 0x80 ..= 0x8
```

# Init / startup sequence

This is data that the panel receives from the radio.
```
01       <-- Turn on 
01       <-- Turn on  <-- some time needs to pass before executing RO etc below
:        <-- 0x3A NB?
R0       <-- Read memory? Definitely memory related
It repeats this sequence quite a bit, could be loading VFO's and memories with different values?
20       <-- Exit Memory mode
61       <-- Read XIT
71       <-- Show ALC Meter 
80       <-- Read squelch value?
94       <-- USB/LSB etc... Mode
;4077620 <-- Frequency
<        
=0000    <-- Clear meters etc.
>73

Set display segments:
?ˆ  <-- 0x63 Dispay Segment ATT/ PRE etc..
@„  <-- 0x64 (VOX ON / @ˆ (VOX OFF)
A€  <-- A 0x80 (Section of display where AGC etc is shown)
B <-- B 0x81 (Can also be B 0x80)
CÀ   <-- C 0x83 (Can also be C 0xC8
D <-- D 0x81
E <-- E 0x81
F‰€€ <-- F 0x89 0x80 0x80
L€   <-- L 0x80
Meter values:
=0300
=0000
Read? / set XIT RIT values
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
Q0  <-- RX Only
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
:  
20
61
71
;4077620
<        
=0000
O
31
81
S0  <-- Segment for error codes Error codes etc, S1 to S8?
ÿ
ÿ
```