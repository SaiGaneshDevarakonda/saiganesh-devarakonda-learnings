# Answers: Structs, Unions, Endianness

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz: Struct, union, padding, endianness (20 questions)

1. **c)** `12`: c@0, pad 1–3, i@4–7, d@8, pad to 12.
2. **b)** `8`: Same members as A, reordered → 8.
3. **c)** `16`: d must start at a multiple of 8.
4. **b)** `8`: a@0, pad@1, b@2–3, c@4–7.
5. **b)** `16`: d@0–7, c@8, pad 9–11, i@12–15.
6. **b)** `5`: No padding → 5. Access to i is now unaligned.
7. **c)** `8`: Largest member 5, rounded up to int alignment → 8.
8. **b)** `78`: c overlaps the first byte; little endian stores the LSB first.
9. **c)** `The bits of 2.5f read as an int (1075838976)`: Same memory: 2.5f = 0x40200000.
10. **b)** `1`: 3 bits keep 9 & 7 = 1.
11. **c)** `4`: One unsigned int storage unit.
12. **c)** Compile error: Bit-fields have no address.
13. **b)** `6 3`: A=0, B=5, C=6, D=2, E=3.
14. **c)** Compile error: C has no == for structs.
15. **b)** `1`: Assignment copies; b is independent.
16. **b)** Compile error: incomplete type: Must be struct Node *next;.
17. **b)** `9`: Shallow copy: both point to the same int.
18. **b)** `0x12`: Big endian = MSB at the lowest address.
19. **b)** So each member is naturally aligned: faster access, no alignment faults: Unaligned access can take two bus cycles or fault.
20. **b)** `4`: 3 pad bytes after c. offsetof is used by container_of in Linux.


<a id="practice-solutions"></a>
## Practice solutions

### 1. Endianness check
```c
#include <stdio.h>
#include <stdint.h>
int is_little_union(void) { union { uint32_t i; uint8_t c[4]; } u = { .i = 1 }; return u.c[0] == 1; }
int is_little_ptr(void)   { uint32_t x = 1; return *(uint8_t *)&x == 1; }
int main(void) {
    printf("%s %s\n", is_little_union() ? "little" : "big", is_little_ptr() ? "little" : "big");
    return 0;
}
```

### 2. Byte swap
```c
#include <stdio.h>
#include <stdint.h>
uint32_t swap32(uint32_t x) {
    return ((x & 0x000000FFu) << 24) |
           ((x & 0x0000FF00u) << 8)  |
           ((x & 0x00FF0000u) >> 8)  |
           ((x & 0xFF000000u) >> 24);
}
int main(void) { printf("0x%08X\n", swap32(0x12345678u)); return 0; }   /* 0x78563412 */
```

### 3. Size by hand, then reorder
```
 struct { char a; double b; char c; int d; short e; }
   a  @0        pad 1..7
   b  @8..15
   c  @16       pad 17..19
   d  @20..23
   e  @24..25   pad 26..31   → total 32 (multiple of 8)

 Reordered: { double b; int d; short e; char a; char c; }
   b 0..7, d 8..11, e 12..13, a 14, c 15 → 16 bytes
```
```c
#include <stdio.h>
struct bad  { char a; double b; char c; int d; short e; };
struct good { double b; int d; short e; char a; char c; };
int main(void) { printf("%zu %zu\n", sizeof(struct bad), sizeof(struct good)); return 0; }   /* 32 16 */
```

### 4. UART register map
```c
#include <stdint.h>
typedef struct {
    volatile uint32_t DATA;     /* +0x00 */
    volatile uint32_t STATUS;   /* +0x04 */
    volatile uint32_t CTRL;     /* +0x08 */
    volatile uint32_t BAUD;     /* +0x0C */
} uart_regs_t;
#define UART0          ((uart_regs_t *)0x40001000u)
#define UART_TX_READY  (1u << 0)
void uart_putc(char c) {
    while ((UART0->STATUS & UART_TX_READY) == 0u) { }   /* poll: volatile forces a re-read */
    UART0->DATA = (uint32_t)(unsigned char)c;
}
```
This is bare-metal only: it compiles anywhere, but only runs on hardware that has this address mapped.

### 5. `container_of`
```c
#include <stdio.h>
#include <stddef.h>
#define container_of(ptr, type, member) ((type *)((char *)(ptr) - offsetof(type, member)))
struct dev { int id; int irq; };
int main(void) {
    struct dev d = { 7, 42 };
    int *pirq = &d.irq;                                   /* we only have a pointer to a member */
    struct dev *back = container_of(pirq, struct dev, irq);
    printf("%d\n", back->id);                             /* 7 */
    return 0;
}
```
The Linux kernel uses this everywhere. For example, a driver gets a pointer to an embedded `struct list_head` or `struct device` and gets back to its own private struct.

### 6. Topper from an array of structs
```c
#include <stdio.h>
struct student { char name[20]; int marks; };
int main(void) {
    struct student s[] = { {"Ravi", 78}, {"Anu", 92}, {"Kiran", 85} };
    int n = sizeof s / sizeof s[0], best = 0;
    for (int i = 1; i < n; i++) if (s[i].marks > s[best].marks) best = i;
    printf("%s %d\n", s[best].name, s[best].marks);   /* Anu 92 */
    return 0;
}
```

### 7. Bit-fields for registers
The order of bits, the size of the storage unit, and whether a field can straddle units are all implementation-defined. The layout may not match the datasheet or another compiler. Writing a field also does a hidden read-modify-write of the whole unit, which can trigger side effects on clear-on-read or write-1-to-clear registers. Masks and shifts are explicit and portable.

### 8. `memcmp` on structs
The padding bytes hold indeterminate values, so two structs with equal members can still compare unequal. Compare member by member, or `memset` both structs to 0 before filling them.
