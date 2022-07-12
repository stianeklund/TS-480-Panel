## Kenwood TS-480 transceiver power-on sequence

Data dumped from the transceiver (radio) RX UART to the panel, this is data being sent to the panel _from_ the radio

```
0D 0D 30 31 0D 30 31 0D 
3A 20 20 0D
52 30 0D 32 30 0D
36 31 0D 
37 31 0D 
38 30 0D 
39 32 0D 
3B 32 38 30 36 36 39 37 0D
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D 3E 37 31 0D
3F 80 0D 
40 84 0D 
41 81 0D 
42 81 0D 
43 C0 0D 
44 81 0D 
45 81 0D 
46 89 80 80 0D
4C 80 0D 
3D 32 30 30 30 0D
3D 31 33 30 30 0D
3D 30 37 30 30 0D
3D 30 34 30 30 0D
3D 30 31 30 30 0D
3D 30 30 30 30 0D
3A 20 20 0D 32 30 0D 
36 31 0D 37 31 0D 3B 
32 38 30 36 36 39 37 0D 
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D 3A 20 20 0D
32 30 0D 36 31 0D 37 31 0D 
3B 32 38 30 36 36 39 37 0D 
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D
3A 20 20 0D
32 30 0D 
36 31 0D 
37 31 0D 
3B 32 38 30 36 36 39 37 0D
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D
43 C8 0D 
3A 20 20 0D
32 30 0D 
36 31 0D 
37 31 0D 
3B 32 38 30 36 36 39 37 0D
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D 
3A 20 20 0D
32 30 0D 
36 31 0D 
37 31 0D 
3B 32 38 30 36 36 39 37 0D
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D
3A 20 20 0D
32 30 0D 
36 31 0D 
37 31 0D 
3B 32 38 30 36 36 39 37 0D
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D
3A 20 20 0D
32 30 0D 
36 31 0D 
37 31 0D 
3B 32 38 30 36 36 39 37 0D
3C 20 20 20 B0 20 20 20 20 0D
3D 30 30 30 30 0D
51 30 0D 
3A 20 20 0D
32 30 0D 
36 31 0D 
37 31 0D 
3B 32 38 30 36 36 39 37 0D
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D
3A 20 20 0D
32 30 0D 
36 31 0D 
37 31 0D 
3B 32 38 30 36 36 39 37 0D
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D
3A 20 20 0D
32 30 0D 
36 31 0D 
37 31 0D 
3B 32 38 30 36 36 39 37 0D
3C 20 20 20 20 20 20 20 20 0D
3D 30 30 30 30 0D
3A 20 20 0D
32 30 0D 
36 31 0D 
37 31 0D 
3B 32 38  30 36 36 39 37 0D
3C 20 20  20 20 20 20 20 20 0D
3D 30 30  30 30 0D
4F 0D 33 31 0D <-- Read XIT value
38 31 0D       <-- Read Volume level
53 30 0D       <-- Read Squelch value
0D FF 0D
```

```
01
01
:  
R0
20
61
71
80
92
;2806697
<        
=0000
>
71
?€@„A 
B
CÀ
D
E
F‰€€
L€
:  
20
61
71
;2806697
<        
=0000:  
61
71;
2806697
<        
=0000
:  
20
61
71
;2806697
<        
=0000
:  
20
61
71
;2806697
<        
=0000
:  20 61 71
;2806697<        
=0000
:  
20
61
71
;2806697
<        
=0000
CÈ
:  
20
61
71
;2806697<        
=0000
Q0
:  
20
61
71
;2806697
<        
=0000
:  
20
61
71
;2806697
<        
=0000
:  
61
71
;2806697
<        
=0000
:  
20
61
71
;2806697<        
=0000
O
31
81
S0
ÿ
ÿ
ÿ
ÿ
ÿ
ÿ
#
0
```


### Panel reply to radio:

```
               IF Shift value
V2A     X01FF              Volume value
W00
```

Panel response / power on sequence:
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