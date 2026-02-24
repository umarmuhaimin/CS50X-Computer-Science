🔡 ASCII

1) Overview
 - Just as numbers are binary patterns of ones and zeros, letters are represented using ones and zeros, too!
 - Since there is an overlap between the ones and zeros that represent numbers and letters, the ASCII standard was created to map specific letters to specific numbers.

2) Example: Letter A in Binary
 - For example, the letter A was decided to map to the number 65. 01000001 represents the number 65 in binary. You can visualize this as follows:

| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|-----|----|----|----|---|---|---|---|
|  0  |  1 |  0 |  0 | 0 | 0 | 0 | 1 |

3) Example: Text Message Bits to Characters
 - If you received a text message, the binary under that message might represent the numbers 72, 73, and 33. Mapping these out to ASCII, your message would look as follows:

| 72 | 73 | 33 |
|----|----|----|
| H  | I  | !  |

 - Thank goodness for standards like ASCII that allow us to agree upon these values!

4) Expanded ASCII Map
Here is an expanded map of ASCII values:

| 0 | NUL | 16 | DLE | 32 | SP | 48 | 0 | 64 | @ | 80 | P | 96 | ` | 112 | p |
|---|-----|----|-----|----|----|----|---|----|---|----|---|----|---|-----|---|
| 1 | SOH | 17 | DC1 | 33 | !  | 49 | 1 | 65 | A | 81 | Q | 97 | a | 113 | q |
| 2 | STX | 18 | DC2 | 34 | ”  | 50 | 2 | 66 | B | 82 | R | 98 | b | 114 | r |
| 3 | ETX | 19 | DC3 | 35 | #  | 51 | 3 | 67 | C | 83 | S | 99 | c | 115 | s |
| 4 | EOT | 20 | DC4 | 36 | $  | 52 | 4 | 68 | D | 84 | T | 100 | d | 116 | t |
| 5 | ENQ | 21 | NAK | 37 | %  | 53 | 5 | 69 | E | 85 | U | 101 | e | 117 | u |
| 6 | ACK | 22 | SYN | 38 | &  | 54 | 6 | 70 | F | 86 | V | 102 | f | 118 | v |
| 7 | BEL | 23 | ETB | 39 | ’  | 55 | 7 | 71 | G | 87 | W | 103 | g | 119 | w |
| 8 | BS  | 24 | CAN | 40 | (  | 56 | 8 | 72 | H | 88 | X | 104 | h | 120 | x |
| 9 | HT  | 25 | EM  | 41 | )  | 57 | 9 | 73 | I | 89 | Y | 105 | i | 121 | y |
| 10 | LF | 26 | SUB | 42 | *  | 58 | : | 74 | J | 90 | Z | 106 | j | 122 | z |
| 11 | VT | 27 | ESC | 43 | +  | 59 | ; | 75 | K | 91 | [ | 107 | k | 123 | { |
| 12 | FF | 28 | FS  | 44 | ,  | 60 | < | 76 | L | 92 | \ | 108 | l | 124 | | |
| 13 | CR | 29 | GS  | 45 | -  | 61 | = | 77 | M | 93 | ] | 109 | m | 125 | } |
| 14 | SO | 30 | RS  | 46 | .  | 62 | > | 78 | N | 94 | ^ | 110 | n | 126 | ~ |
| 15 | SI | 31 | US  | 47 | /  | 63 | ? | 79 | O | 95 | _ | 111 | o | 127 | DEL |

5) Capacity Note
 - If you wish, you can learn more about ASCII.
 - If each character is stored in exactly one 8-bit byte, you can encode at most 256 distinct character codes. ASCII uses only 128 of those (0-127).
