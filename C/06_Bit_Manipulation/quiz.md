# Quiz: Bit Manipulation

[← Back to notes](README.md)

> Unsigned 32-bit unless stated. Draw the bits!

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz: Bit manipulation (20 questions)


**Q1.**

```c
unsigned x = 0x0F;
x |= (1u << 5);
printf("0x%X", x);
```

- a) `0x1F`
- b) `0x2F`
- c) `0x4F`
- d) `0x0F`


**Q2.**

```c
unsigned x = 0xFF;
x &= ~(1u << 3);
printf("0x%X", x);
```

- a) `0xF7`
- b) `0xFB`
- c) `0x08`
- d) `0xEF`


**Q3.**

```c
unsigned x = 0x0A;
x ^= (1u << 1);
printf("0x%X", x);
```

- a) `0x0B`
- b) `0x0E`
- c) `0x08`
- d) `0x02`


**Q4.**

```c
unsigned x = 0x10;
printf("%u", (x >> 4) & 1);
```

- a) `0`
- b) `16`
- c) `1`
- d) `4`


**Q5.**

```c
unsigned x = 0xABCD;
(x >> 8) & 0xF = ?
```

- a) `0xA`
- b) `0xB`
- c) `0xC`
- d) `0xD`


**Q6.**

```c
unsigned x = 12;
printf("%u", x & (x - 1));
```

- a) `11`
- b) `4`
- c) `8`
- d) `0`


**Q7.**

Which expression correctly checks if x is a power of 2?

- a) `x & (x - 1)`
- b) `x && !(x & (x - 1))`
- c) `x % 2 == 0`
- d) `(x & 1) == 0`


**Q8.**

```c
unsigned x = 12;
printf("%u", x & -x);
```

- a) `12`
- b) `8`
- c) `4`
- d) `1`


**Q9.**

```c
unsigned x = 0xF0; int c = 0;
while (x) { x &= x - 1; c++; }
printf("%d", c);
```

- a) `8`
- b) `4`
- c) `240`
- d) `1`


**Q10.**

(1u << 5) - 1 = ?

- a) `0x20`
- b) `0x1F`
- c) `0x3F`
- d) `0x0F`


**Q11.**

```c
unsigned reg = 0xFFFF;
reg = (reg & ~(0xFu << 4)) | (0x3u << 4);
printf("0x%X", reg);
```

- a) `0xFF3F`
- b) `0xFFF3`
- c) `0x003F`
- d) `0xFF30`


**Q12.**

Which checks that BOTH bit 2 and bit 5 are set?

- a) `x & 0x24`
- b) `(x & 0x24) == 0x24`
- c) `x | 0x24`
- d) `x == 0x24`


**Q13.**

```c
unsigned x = 200;
printf("%u", x >> 3);
```

- a) `1600`
- b) `25`
- c) `24`
- d) `66`


**Q14.**

```c
printf("%c", 'a' ^ 0x20);
```

- a) a
- b) A
- c) !
- d) B


**Q15.**

```c
unsigned char x = 0x0F;
printf("0x%X", (unsigned char)~x);
```

- a) `0xFFFFFFF0`
- b) `0xF0`
- c) `0x0F`
- d) `0x00`


**Q16.**

```c
unsigned char x = 0xAB;
x = ((x & 0x0F) << 4) | ((x & 0xF0) >> 4);
printf("0x%X", x);
```

- a) `0xAB`
- b) `0xBA`
- c) `0x0B`
- d) `0xA0`


**Q17.**

To set bit 31 of a uint32_t register, which one is WRONG?

- a) `reg |= 1u << 31;`
- b) `reg |= 0x80000000u;`
- c) `reg |= 1 << 31;`
- d) `reg |= (uint32_t)1 << 31;`


**Q18.**

```c
int a = 5, b = 9;
a ^= b; b ^= a; a ^= b;
printf("%d %d", a, b);
```

- a) `5 9`
- b) `9 5`
- c) `0 0`
- d) `12 9`


**Q19.**

```c
unsigned x = 0x0000F000;
Position of the highest set bit?
```

- a) `12`
- b) `15`
- c) `16`
- d) `4`


**Q20.**

```c
GPIO->ODR |= BIT(3);  // main loop
GPIO->ODR |= BIT(5);  // in an ISR
What can go wrong?
```

- a) `Nothing: |= is atomic`
- b) The ISR runs between main's read and write → bit 5 is lost
- c) Compile error
- d) Bits swap


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
