# 05 · Structs, Unions, Padding, Bit-fields, Endianness

**Quiz:** [quiz.md](quiz.md) · **Answers and solutions:** [answers.md](answers.md)

> Sizes: char 1, short 2, int 4, double/pointer 8 (64-bit Linux GCC).

---

## Theory

### 1. Padding rules
```
 Rule 1: each member starts at an address that is a multiple of ITS OWN size (its alignment)
 Rule 2: the total struct size is a multiple of the LARGEST member's alignment

 struct { char c; int i; char d; };          struct { int i; char c; char d; };
 ┌─┬───┬────┬─┬───┐                          ┌────┬─┬─┬──┐
 │c│pad│ i  │d│pad│ = 12 bytes               │ i  │c│d│pd│ = 8 bytes
 └─┴───┴────┴─┴───┘                          └────┴─┴─┴──┘
  0 1   4    8 9                               0    4 5 6
 Trick: order members LARGEST → SMALLEST to minimise padding
```
| Struct | Size | Why |
|---|---|---|
| `{char c; double d;}` | 16 | d must start at 8 |
| `{char a; short b; int c;}` | 8 | a@0, pad@1, b@2, c@4 |
| `{double d; char c; int i;}` | 16 | 8 + 1 + 3 pad + 4 |
| `{double d; int i;}` | 16 | 12 rounded up to a multiple of 8 (so arrays stay aligned) |
| `{short s; char c;}` | 4 | 3 rounded up to a multiple of 2 |
| `__attribute__((packed)) {char c; int i;}` | 5 | no padding: access is slower, and unaligned access can fault on ARM |

`offsetof(type, member)` (in `<stddef.h>`) gives a member's byte offset. Linux's `container_of` is built on it.

**Why padding exists:** an aligned load takes one bus access. An unaligned one may take two, or raise an alignment fault (on strict-alignment ARM, or on device memory).

### 2. Struct vs union
```
 struct: members side by side     size ≈ SUM (+ padding)
 union : members OVERLAP          size = LARGEST member, rounded up to alignment
         writing one member overwrites the others
 union { int i; char c[5]; }  → 8   (5 rounded up to int alignment 4)
 union { char c[3]; short s; } → 4
```

### 3. Endianness (storing `0x12345678` at address 0x100)
```
 addr:            0x100  0x101  0x102  0x103
 Little endian:    78     56     34     12     ← LSB first  (ARM default, x86)
 Big endian:       12     34     56     78     ← MSB first  (network byte order)

 Check:  int x = 1;  if (*(char *)&x == 1) → little endian
```

### 4. Bit-fields
```
 struct { unsigned a:3; unsigned b:5; } s;    → fits in one unsigned int = 4 bytes
 s.a = 9;   → only 3 bits kept → 9 & 0b111 = 1
 &s.a       → compile error (bits have no address)
```
Bit order and packing are **implementation-defined**, so avoid bit-fields for hardware registers that must match a datasheet. Use masks and shifts instead.

### 5. Quick facts
- `enum {A, B = 5, C, D = 2, E}` gives C = 6 and E = 3. Each value is the previous one + 1, and the values are `int`.
- `s1 = s2` copies all bytes. `s1 == s2` is a **compile error**. `memcmp` is risky because the padding bytes may differ.
- Struct copy is **shallow**: pointer members are copied, so both structs point to the same memory.
- `struct S { struct S next; };` is an error (incomplete type). Use `struct S *next;`.

### 6. Register map as a struct (embedded pattern)
```c
typedef struct {
    volatile uint32_t DATA;    /* +0x00 */
    volatile uint32_t STATUS;  /* +0x04 */
    volatile uint32_t CTRL;    /* +0x08 */
    volatile uint32_t BAUD;    /* +0x0C */
} uart_regs_t;
#define UART0 ((uart_regs_t *)0x40001000u)
UART0->CTRL |= 1u;
```

---

## Practice Questions
Solutions are in [answers.md](answers.md#practice-solutions).

1. Check endianness two ways: with a `union`, and with a `char *` cast.
2. `uint32_t swap32(uint32_t x)`: 0x12345678 → 0x78563412 using shifts and masks.
3. Work out `sizeof(struct { char a; double b; char c; int d; short e; })` by hand, then reorder the members to the minimum size.
4. A UART register map `struct` (DATA, STATUS, CTRL, BAUD) at `0x40001000`, plus `uart_putc(char c)`, which waits on the TX-ready bit.
5. A `container_of(ptr, type, member)` macro using `offsetof`.
6. An array of `struct student {char name[20]; int marks;}`: print the topper.

**Explain out loud:**
7. Why avoid bit-fields for hardware registers?
8. Why is `memcmp` on two structs risky?

---

## Interview One-liner
*"Each member aligns to its own size and the struct to its largest member, so sort members big to small. A union overlaps them. Little endian puts the LSB at the lowest address."*
