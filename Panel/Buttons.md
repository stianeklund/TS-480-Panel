
### Buttons:

Most of the panel's buttons have 3 states.

| State      | Button State Value | ASCII value |
|------------|--------------------|-------------|
| Up         | 0x30               | 0           |
| Down       | 0x31               | 1           |
| Long Press | 0x33               | 2           |

All button presses or commands are always suffixed with a `0x0D` or ASCII: `\r` (carriage return), but not necessarily sequentially.
Buttons without a long press state will mention this in the comment section below.

A button press looks like this: `Button Byte Value` `Button State Value` `Carriage Return`

| Button   | Byte Value | ASCII value | Comment                   |
|----------|------------|-------------|---------------------------|
| CL       | 0x4D       | M           |                           |
| XIT      | 0x4F       | O           |                           |
| RIT      | 0x4E       | N           |                           |
| TF-SET   | 0x50       | Q           | TF-SET (Left button) Down |
| TF-SET   | 0x51       | P           | TF-SET (Right button)     |
| Multi    | 0x54       | T           | T01 (right) TFF (left)    |
| IF Shift | 0x58       | X           | X0001 - X03FE             |
| QMI      | 0x4B       | K           |                           |

```hexdump
Button     Byte   ASCII, Comment
CL         0x4D   M
XIT        0x4F   O
RIT        0x4E   N
TF-SET-U   0x51   P
TF-SET-D   0x50   Q
Multi      0x54   T        T01 (right) / TFF (left)
IF-Shift   0x58   X        (X0203 etc)
QMI        0x4B   K        Long press: M.IN
QMR        0x4C   L        Long press: M>VFO
A/B        0x4A   J        Long press M/V
A=B        0x49   I        Long press: Split
Menu       0x47   G        Long press: F.Lock
MHz        0x48   H        Long press: M.STP
FIL        0x42   B        Long press: NAR
BC         0x43   C        CW.T in CW mode.
DNL        0x40   @
NR         0x41   A
Fine       0x44   D        Long Press: STEP
CH3        0x36   6
KEY        0x39   9
PROC       0x3B   ;
ENT        0x3F   ?
CH2        0x35   5
MIC        0x38   8
VOX        0x3C   <
AGC        0x3E   >
CH1        0x34   4
PWR        0x37   7
NB/T       0x3A   :
MTR        0x3D   =
On/Off     0x30   0
PF         0x32   2
ATT/PRE    0x21   !
AT         0x33   3        Also has long press
On / Off   0x30 (0x31)  0 / 1  Not connected to same button logic as the rest of the panel
```
