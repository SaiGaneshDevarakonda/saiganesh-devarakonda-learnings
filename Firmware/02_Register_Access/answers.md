# Answers: Register Access

[← Notes](README.md) · [Quiz](quiz.md)

## Quiz answer key
1. **b)** 3 bits wide → `0b111` = 7, shifted to bit 4.
2. **a)** `0xFF` → clear [3:2] → `0xF3` → OR `0x4` → `0xF7`.
3. **b)** Write 1 only to that bit; zeros do nothing.
4. **b)** Signed overflow is undefined.
5. **b)** Cast + dereference = the register itself.
6. **b)** The macro expands to a pointer.
7. **a)** Read-only status register updated by hardware.
8. **b)** `(0xA55 >> 8) & 0xF = 0xA`.
9. **b)** 1 byte + 3 padding → everything after moves by 4.
10. **b)** The ISR can run between the read and the write; its change is overwritten.

## Practice question solutions
1. Mask `0x3U << 8 = 0x300`. `0xFFFFFFFF & ~0x300 = 0xFFFFFCFF`, OR `0x300` → **`0xFFFFFFFF`** (unchanged, the bits were already 11).
2. shift = **4**, mask = **`0x7U << 4`** (3 bits wide), value = **`0x5U << 4`**. `reg = (reg & ~(0x7U << 4)) | (0x5U << 4);`
3. `(0xABCD1234 >> 12) & 0xF` → **`0x1`**.
4. `STATUS = (1U << 4);`. A read-modify-write reads back 1s for other pending bits and writing them back clears those too, losing errors.
5. `volatile`: hardware may change it, so always re-read. `const`: software must never write it. A read-only status register needs both.
6. **`GPIO->ODR`**. The cast makes the expression a pointer; `.` needs an actual struct.
