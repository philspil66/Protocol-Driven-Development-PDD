21 0C 80    ; LD HL,$800C      (address of message, if code starts at $8000)
7E          ; LD A,(HL)
B7          ; OR A             (sets Z if A=0)
C8          ; RET Z            (done)
D7          ; RST $10          (print A)
23          ; INC HL
18 F8       ; JR -8            (back to LD A,(HL))

48 65 6C 6C 6F 2C 20 57 6F 72 6C 64 21 0D 00
            ; "Hello, World!" + CR + 0 terminator
