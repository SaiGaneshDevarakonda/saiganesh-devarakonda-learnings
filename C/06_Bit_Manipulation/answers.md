# Answers: Bit Manipulation

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz: Bit manipulation (20 questions)

1. **b)** `0x2F`: Bit 5 = 0x20.
2. **a)** `0xF7`: Clear bit 3.
3. **c)** `0x08`: 1010 → toggle bit 1 → 1000.
4. **c)** `1`: Bit 4 is set.
5. **b)** `0xB`: Each hex digit is 4 bits: [11:8] = B.
6. **c)** `8`: Clears the lowest set bit: 1100 → 1000.
7. **b)** `x && !(x & (x - 1))`: One 1-bit only; x && rejects 0.
8. **c)** `4`: Isolates the lowest set bit.
9. **b)** `4`: Kernighan: one loop per set bit.
10. **b)** `0x1F`: Mask of the lowest 5 bits.
11. **a)** `0xFF3F`: Clear [7:4], insert 3.
12. **b)** `(x & 0x24) == 0x24`: x & mask is non-zero if EITHER is set.
13. **b)** `25`: >> 3 = divide by 8.
14. **b)** A: Bit 5 is the case bit.
15. **b)** `0xF0`: ~ promotes to int; the cast keeps 8 bits.
16. **b)** `0xBA`: Nibble swap.
17. **c)** `reg |= 1 << 31;`: Signed 1 shifted into the sign bit is UB.
18. **b)** `9 5`: XOR swap.
19. **b)** `15`: Bits 12–15 set → 15.
20. **b)** The ISR runs between main's read and write → bit 5 is lost: Read-modify-write is not atomic. Use SET/CLR registers or disable IRQs.


<a id="practice-solutions"></a>
## Practice solutions

### 1. Bit macros
```c
#include <stdio.h>
#define SET_BIT(x, n)    ((x) |=  (1u << (n)))
#define CLR_BIT(x, n)    ((x) &= ~(1u << (n)))
#define TOGGLE_BIT(x, n) ((x) ^=  (1u << (n)))
#define CHECK_BIT(x, n)  (((x) >> (n)) & 1u)
int main(void) {
    unsigned x = 0x10;
    SET_BIT(x, 1);    printf("0x%X %u\n", x, CHECK_BIT(x, 1));   /* 0x12 1 */
    CLR_BIT(x, 1);    printf("0x%X\n", x);                       /* 0x10 */
    TOGGLE_BIT(x, 4); printf("0x%X\n", x);                       /* 0x0 */
    return 0;
}
```
Every argument is bracketed, so `SET_BIT(x, i + 1)` works correctly.

### 2. Write a register field
```c
#include <stdio.h>
#include <stdint.h>
uint32_t reg_write_field(uint32_t reg, int lsb, int width, uint32_t val) {
    uint32_t mask = (width >= 32) ? 0xFFFFFFFFu : ((1u << width) - 1u);
    return (reg & ~(mask << lsb)) | ((val & mask) << lsb);
}
int main(void) { printf("0x%X\n", reg_write_field(0xFFFF, 4, 4, 0x3)); return 0; }   /* 0xFF3F */
```

### 3. Count set bits
```c
#include <stdio.h>
#include <stdint.h>
int count_loop(uint32_t x) { int c = 0; while (x) { c += x & 1u; x >>= 1; } return c; }   /* 32 iterations max */
int count_kernighan(uint32_t x) { int c = 0; while (x) { x &= x - 1; c++; } return c; } /* one per set bit */
int main(void) { printf("%d %d\n", count_loop(0xF0), count_kernighan(0xF0)); return 0; }  /* 4 4 */
```

### 4. Reverse 32 bits
```c
#include <stdio.h>
#include <stdint.h>
uint32_t reverse_bits(uint32_t x) {
    uint32_t r = 0;
    for (int i = 0; i < 32; i++) { r = (r << 1) | (x & 1u); x >>= 1; }
    return r;
}
int main(void) { printf("0x%08X\n", reverse_bits(1u)); return 0; }   /* 0x80000000 */
```

### 5. Swap odd and even bits
```c
#include <stdio.h>
#include <stdint.h>
uint32_t swap_odd_even(uint32_t x) {
    return ((x & 0xAAAAAAAAu) >> 1) | ((x & 0x55555555u) << 1);
}
int main(void) { printf("0x%X\n", swap_odd_even(0x2u)); return 0; }   /* 0x1 */
```
`0xA` = 1010 picks the odd bit positions and `0x5` = 0101 picks the even ones. Each group shifts one place toward the other.

### 6. Rotate left
```c
#include <stdio.h>
#include <stdint.h>
uint32_t rotl(uint32_t x, unsigned n) {
    n &= 31u;
    if (n == 0) return x;                 /* x >> 32 would be UB */
    return (x << n) | (x >> (32 - n));
}
int main(void) { printf("0x%08X\n", rotl(0x80000001u, 1)); return 0; }   /* 0x00000003 */
```
With n = 0, the naive version computes `x >> 32`, and shifting by the full width is **UB**. Handle it separately, or use `(x << n) | (x >> ((32 - n) & 31))`.

### 7. Highest set bit
```c
#include <stdio.h>
#include <stdint.h>
int highest_bit(uint32_t x) {
    if (x == 0) return -1;
    int pos = 0;
    while (x >>= 1) pos++;
    return pos;
}
int main(void) { printf("%d\n", highest_bit(0xF000)); return 0; }   /* 15 */
```

### 8. Power of 2
```c
#include <stdio.h>
#include <stdint.h>
int is_pow2(uint32_t x) { return x && !(x & (x - 1)); }
int main(void) { printf("%d %d %d\n", is_pow2(64), is_pow2(0), is_pow2(12)); return 0; }   /* 1 0 0 */
```

### 9. Nibble swap
```c
#include <stdio.h>
#include <stdint.h>
uint8_t swap_nibbles(uint8_t x) { return (uint8_t)(((x & 0x0F) << 4) | ((x & 0xF0) >> 4)); }
int main(void) { printf("0x%X\n", swap_nibbles(0xAB)); return 0; }   /* 0xBA */
```

### 10. `1u` vs `1`
`1` is a signed `int`, and `1 << 31` shifts into the sign bit, which is UB. `1u` is unsigned, so every shift from 0 to 31 is well-defined, and masks combine cleanly with `uint32_t` registers.

### 11. Read-modify-write and ISRs
`REG |= BIT(n)` is load, OR, store. If an ISR changes the same register between the load and the store, the store writes back the old value and the ISR's change is lost. With a SET/CLR register you write a mask with **only** your bit set: the hardware sets or clears that bit and leaves the rest alone. It's a single write, so nothing can interrupt it.
