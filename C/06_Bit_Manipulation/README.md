# 06 · Bit Manipulation

**Quiz:** [quiz.md](quiz.md) · **Answers and solutions:** [answers.md](answers.md)

---

## Theory

### 1. The 4 core operations (learn them as macros)
```
 #define BIT(n)            (1u << (n))
 SET     x |=  BIT(n)      0000 1010 | 0010 0000 → 0010 1010
 CLEAR   x &= ~BIT(n)      1111 1111 & 1111 0111 → 1111 0111
 TOGGLE  x ^=  BIT(n)      flips only bit n
 CHECK   (x >> n) & 1      → 0 or 1
```
Always write `1u`, never `1`: `1 << 31` is UB because a 1 is shifted into the sign bit of a signed int.

### 2. Multi-bit fields (register fields)
```
 MASK of k bits        ((1u << k) - 1)           k = 4 → 0xF
 READ  bits [h:l]      (x >> l) & MASK(h - l + 1)

 WRITE value v into bits [h:l]   (read-modify-write):
   x = (x & ~(MASK << l))  |  ((v & MASK) << l)
        └ clear the field ┘    └ insert new value ┘

 reg = 0xFFFF, write 0x3 into [7:4]:
   1111 1111 1111 1111
 & 1111 1111 0000 1111   (clear)
 | 0000 0000 0011 0000   (insert)
 = 1111 1111 0011 1111 = 0xFF3F
```
Each hex digit is 4 bits, so `0xABCD` has [15:12] = A, [11:8] = B, [7:4] = C, [3:0] = D.

### 3. Tricks interviewers love
| Trick | Expression | x = 12 = 1100 |
|---|---|---|
| Clear the lowest set bit | `x & (x - 1)` | 1000 = 8 |
| Isolate the lowest set bit | `x & -x` | 0100 = 4 |
| Is it a power of 2? | `x && !(x & (x - 1))` | no |
| Count set bits (Kernighan) | `while (x) { x &= x - 1; c++; }` | 2 loops |
| Odd? | `x & 1` | 0 → even |
| Multiply / divide by 2ⁿ | `x << n` / `x >> n` (unsigned) | 12 >> 2 = 3 |
| Toggle letter case | `c ^ 0x20` | 'a' ↔ 'A' |
| XOR swap | `a ^= b; b ^= a; a ^= b;` | breaks if a and b are the same variable |
| Nibble swap | `((x & 0x0F) << 4) \| ((x & 0xF0) >> 4)` | 0xAB → 0xBA |
| Both bits 2 and 5 set? | `(x & 0x24) == 0x24` | `x & 0x24` alone means "either" |

`~` on a small type promotes it to `int` first: `~(unsigned char)0x0F` is 0xFFFFFFF0. Cast back with `(unsigned char)~x` to get 0xF0.

### 4. Embedded trap: read-modify-write is not atomic
```
 main:  REG |= BIT(3);   →  load REG ─┐               ┌─ store (old | bit3)
                                      │   ISR runs:   │   ← overwrites the ISR's write!
                                      └─ REG |= BIT(5)┘
```
Fixes: hardware **SET/CLR registers** (write 1 to set or clear only that bit), disabling interrupts around the RMW, or exclusive load/store (LDREX/STREX on ARM).

---

## Practice Questions
Solutions are in [answers.md](answers.md#practice-solutions).

| # | Problem | Input → Output |
|---|---|---|
| 1 | `SET_BIT`, `CLR_BIT`, `TOGGLE_BIT`, `CHECK_BIT` macros with safe brackets | `x=0x10, bit 1` → set `0x12`, check `1` |
| 2 | `uint32_t reg_write_field(uint32_t reg, int lsb, int width, uint32_t val)` | `0xFFFF, 4, 4, 0x3` → `0xFF3F` |
| 3 | Count set bits: a simple loop and Kernighan's method | `0xF0` → `4` |
| 4 | Reverse the bits of a 32-bit number | `0x00000001` → `0x80000000` |
| 5 | Swap odd and even bits | `0b10` → `0b01` |
| 6 | Rotate left by n. What breaks when n = 0? | `0x80000001, 1` → `0x00000003` |
| 7 | Position of the highest set bit (no `__builtin_clz`) | `0xF000` → `15` |
| 8 | Is it a power of 2? | `64` → yes, `0` → no |
| 9 | Swap the nibbles of a byte | `0xAB` → `0xBA` |

**Explain out loud:**
10. Why `1u` instead of `1` in masks?
11. Why is `REG |= BIT(n)` unsafe when an ISR touches the same register, and how does a SET/CLR register fix it?

---

## Interview One-liner
*"OR sets, AND-NOT clears, XOR toggles, shift and AND checks. Fields are clear then insert, always with unsigned 1u."*
