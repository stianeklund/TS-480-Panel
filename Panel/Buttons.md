
### Buttons:

Most of the panel's buttons have 3 or 4 states.

| State      | Button State Value | ASCII value |
|------------|--------------------|-------------|
| Up         | 0x30               | 0           |
| Down       | 0x31               | 1           |
| Long Press | 0x33               | 3           |
| Hold       | 0x34               | 4           |

All button presses or commands are always suffixed with `0x0D` or ASCII: `\r` (carriage return), but not necessarily sequentially; depends on the size of the command.
Buttons without a long press state will mention this in the comment section below.

A button press looks like this: `Button Byte Value`:`Button State Value` `Carriage Return`

| Button   | Byte Value | ASCII value | Comment                                                    |
|----------|------------|-------------|------------------------------------------------------------|
| CL       | 0x4D       | M           |                                                            |
| XIT      | 0x4F       | O           |                                                            |
| RIT      | 0x4E       | N           |                                                            |
| TF-SET   | 0x50       | Q           | TF-SET (Left button) Down                                  |
| TF-SET   | 0x51       | P           | TF-SET (Right button)                                      |
| Multi    | 0x54       | T           | T01 (right) TFF (left)                                     |
| IF Shift | 0x58       | X           | X0001 - X03FE                                              |
| QMI      | 0x4B       | K           | M.IN                                                       |
| QMR      | 0x4C       | L           | M>VFO                                                      |
| A/B      | 0x4A       | J           | M/V                                                        |
| A=B      | 0x49       | I           | Split                                                      |
| Menu     | 0x47       | G           | F.Lock                                                     |
| MHz      | 0x48       | H           | M.STP                                                      |
| FIL      | 0x42       | B           | NAR                                                        |
| BC       | 0x43       | C           | CW.T in CW mode.                                           |
| DNL      | 0x40       | @           | DNL Level                                                  | 
| NR       | 0x41       | A           | NR level                                                   |
| Fine     | 0x44       | D           | STEP                                                       |
| CH3      | 0x36       | 6           | 3 REC                                                      |
| KEY      | 0x39       | 9           | 6 DELAY                                                    |
| PROC     | 0x3B       | ;           | 9                                                          |
| ENT      | 0x3F       | ?           |                                                            |
| CH2      | 0x35       | 5           | 2 REC                                                      |
| MIC      | 0x38       | 8           | 5 RF.G                                                     |
| VOX      | 0x3C       | <           | 8                                                          |
| AGC      | 0x3E       | >           | 0 OFF                                                      |
| CH1      | 0x34       | 4           | 1 REC                                                      |
| PWR      | 0x37       | 7           | 4 TX MONI                                                  |
| NB/T     | 0x3A       | :           | 7                                                          |
| MTR      | 0x3D       | =           | CLR                                                        |
| On/Off   | 0x30       | 0           |                                                            |
| PF       | 0x32       | 2           |                                                            |
| ATT/PRE  | 0x21       | !           | ANT 1/2                                                    |
| AT       | 0x33       | 3           |                                                            |
| On / Off | 0x30       | 0           | No short press but 02 is used to "reconnect" panel / radio |