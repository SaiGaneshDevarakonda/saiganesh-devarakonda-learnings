# Quiz: Register Access

[← Back to notes](README.md)

> 32-bit unsigned registers unless stated. Draw the bits!

Answer every question before you check the [answer sheet](answers.md). Pass mark: **80%**.

**Q1.** Mask for field [6:4]:
- a) `0x5U << 4`
- b) `0x7U << 4`
- c) `0x7U << 6`
- d) `0x3U << 4`

**Q2.** `reg = 0xFF; reg = (reg & ~(0x3U << 2)) | (0x1U << 2);` gives:
- a) `0xF7`
- b) `0xF3`
- c) `0xF4`
- d) `0xFB`

**Q3.** To clear bit 3 of a W1C status register:
- a) `REG &= ~(1U << 3);`
- b) `REG = (1U << 3);`
- c) `REG |= (1U << 3);`
- d) `REG = 0;`

**Q4.** Why use `1U << 31` instead of `1 << 31`?
- a) Faster
- b) `1 << 31` overflows a signed int (undefined behaviour)
- c) Style only
- d) `1U` is 64-bit

**Q5.** `#define R (*(volatile uint32_t *)0x1000U)` — `R` is:
- a) A pointer
- b) The register value at 0x1000
- c) The number 0x1000
- d) A struct

**Q6.** `#define U ((UART_TypeDef *)0x4000U)` — correct access:
- a) `U.CTRL`
- b) `U->CTRL`
- c) `*U->CTRL`
- d) `&U.CTRL`

**Q7.** `volatile const uint32_t *p;` means:
- a) Hardware may change `*p`; software must not write it
- b) Invalid in C
- c) The pointer can't move
- d) Stored in flash only

**Q8.** Field [11:8] in `0x00000A55` reads as:
- a) `0x5`
- b) `0xA`
- c) `0x55`
- d) `0x0`

**Q9.** In a register-map struct, a `uint8_t` inserted after a 32-bit `DATA` causes:
- a) Nothing
- b) Later members shift by 4 bytes and map to the wrong registers
- c) A compile error
- d) Only that byte is wrong

**Q10.** Main risk of a read-modify-write on a register also touched by an ISR:
- a) Slower code
- b) The ISR's update can be lost (not atomic)
- c) Compile error
- d) Flash wear
