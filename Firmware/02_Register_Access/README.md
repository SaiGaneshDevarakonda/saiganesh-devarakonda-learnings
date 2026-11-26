# 02 · Register Access

[Quiz](quiz.md) · [Answers](answers.md)

## Theory

### 1. Mask write = Read → Clear → Set → Write

A mask write changes only some bits of a register and leaves the rest untouched.

```
Goal: write 0b101 (5) into bits [6:4]

reg (read)    = 1 1 0 0 1 0 1 1      ← 1. READ
                  └─┬─┘
mask          = 0 1 1 1 0 0 0 0      ← field bits = 1
reg & ~mask   = 1 0 0 0 1 0 1 1      ← 2. CLEAR field
(val<<4)&mask = 0 1 0 1 0 0 0 0      ← 3. SET
OR result     = 1 1 0 1 1 0 1 1      ← 4. WRITE back, other bits unchanged
```

### 2. Single bit: register, position, value (0 or 1)

The mask is just `1U << pos`. Clear the bit, then OR in the value.

```
Goal: write val=1 at pos=3
reg            = 1 0 1 1 0 1 1 0
reg & ~(1U<<3) = 1 0 1 1 0 1 1 0   ← bit 3 cleared
val << 3       = 0 0 0 0 1 0 0 0
OR             = 1 0 1 1 1 1 1 0   ✅ only bit 3 changed
```

```c
uint32_t write_bit(uint32_t reg, uint32_t pos, uint32_t val)
{
    reg &= ~(1U << pos);          // clear
    reg |= ((val & 1U) << pos);   // set (forced to 0/1)
    return reg;
}

#define WRITE_BIT(reg, pos, val) \
    do { (reg) = ((reg) & ~(1U << (pos))) | ((uint32_t)!!(val) << (pos)); } while (0)

/* simplest logic, also fine */
if (val) reg |=  (1U << pos);
else     reg &= ~(1U << pos);
```

A first attempt like `#define Replacebit(reg, bitpos, val) reg = ((reg &~(1<<bitpos)) | (val<<bitpos))` is logically correct when `val` is strictly 0 or 1, but it needs hardening:
1. "Boolean" in C often means *any non-zero*. `val = 4` would corrupt another bit → force it with `!!(val)` or `& 1U`.
2. `1 << 31` is signed overflow (undefined) → use `1U`.
3. Put parentheses around every argument, or `pos = n+1` expands wrongly.
4. Wrap the body in `do { } while (0)` so it behaves as one statement inside `if/else`.

### 3. Multi-bit field: shift, width, mask, value

```
field [high:low]
shift = low bit
width = high − low + 1
mask  = ((1U << width) − 1) << shift
reg   = (reg & ~mask) | ((value << shift) & mask)
```

Worked example, bits [3:2] = `0b10`:

```
value 0b10 = decimal 2 = hex 0x2
bit:          7 6 5 4 3 2 1 0
0x2U        = 0 0 0 0 0 0 1 0    ← at bits [1:0], wrong place
0x2U << 2   = 0 0 0 0 1 0 0 0    ← now at bits [3:2]
0x3U << 2   = 0 0 0 0 1 1 0 0    ← mask: 2 bits wide = 0b11 = 3
```
```c
UART0->CTRL = (UART0->CTRL & ~(0x3U << 2)) | (0x2U << 2);
```

The mask comes from the **width**, the value from **what you want**. They are different numbers. Using the value as the mask (`~(5<<4)` for a 3-bit field) clears only some field bits: if the field was `111`, it ends as `111` instead of `101`. A plain `CTRL |= 0b10` is wrong twice: it isn't shifted, and it never clears old bits. Prefer hex over `0b`, which is a compiler extension.

Reusable helpers:
```c
static inline void field_write(volatile uint32_t *reg,
                               uint32_t shift, uint32_t width, uint32_t val)
{
    uint32_t mask = ((1U << width) - 1U) << shift;
    uint32_t tmp  = *reg;            // 1. read
    tmp &= ~mask;                    // 2. clear
    tmp |= (val << shift) & mask;    // 3. set
    *reg = tmp;                      // 4. single write
}

static inline uint32_t field_read(volatile uint32_t *reg, uint32_t shift, uint32_t width)
{
    return (*reg >> shift) & ((1U << width) - 1U);
}
```
Points to say out loud: `& mask` on the value protects neighbouring bits; `volatile` forces a real hardware access; one final write means the register never sees a half-updated value; `width = 32` makes `1U << 32` undefined, so handle it separately; the read-modify-write is **not atomic**, so protect it if an ISR touches the same register.

### 4. W1C (write-1-to-clear) status registers

In a W1C register, writing **1 clears** a bit and writing **0 does nothing**. Error/interrupt status registers are usually W1C: until the bit is cleared, the interrupt keeps firing.

Read-modify-write is **wrong** here: the read returns 1s for every other pending bit, writing them back clears them all, and other errors are silently lost.

```c
STATUS_REG = (1U << pos);   // clears ONLY bit pos, no read needed
```

### 5. `volatile const` together

```
volatile → HARDWARE can change it anytime   (always re-read)
const    → SOFTWARE must not write it       (read-only status register)
```
Both together are valid and common: `volatile const uint32_t *STATUS_REG;`

Pointer forms:
| Declaration | Pointer can move? | Data can change through it? |
|---|---|---|
| `uint32_t * const p` | ❌ | ✅ |
| `const uint32_t *q` | ✅ | ❌ |

### 6. Two styles of register access

**Style 1: macro for one register (cast + dereference)**
```c
#define UART_DATA  (*(volatile uint32_t *)0x40011004U)
                    ↑ └──────── cast ────────┘
                    │  "treat this number as a pointer"
                    └─ dereference: "go to that address"
UART_DATA = 'A';  char c = UART_DATA;
```
`UART_DATA` is the register itself (a value), not a pointer. There is one cast and one dereference, not two pointers.

**Style 2: struct overlay for a whole peripheral (vendor/CMSIS style)**
```c
typedef struct {
    volatile uint32_t DATA;     // 0x00  bytes 0–3
    volatile uint32_t STATUS;   // 0x04  bytes 4–7
    volatile uint32_t CTRL;     // 0x08  bytes 8–11
} UART_TypeDef;

#define UART0  ((UART_TypeDef *)0x40011000U)   // cast only, no *

UART0->CTRL |= (1U << 0);
while (!(UART0->STATUS & (1U << 5)));
UART0->DATA = 'A';
```

`#define` is only text replacement: the name `UART0` has no type. After pasting, the **expression** `((UART_TypeDef *)0x40011000U)` has type "pointer to UART_TypeDef". The cast creates the pointer, so no `*` on the name is needed, and access uses `->`:

```
UART0->CTRL  ≡  (*UART0).CTRL
```
`UART0.CTRL` would be a compile error ("request for member in something not a structure").

| Macro | `UART0` is | Access with |
|---|---|---|
| `((UART_TypeDef *)addr)` | a pointer | `UART0->CTRL` |
| `(*(UART_TypeDef *)addr)` | the struct | `UART0.CTRL` |

Both compile to the same code; vendor headers use the pointer style.

### 7. The struct must match the register map exactly

Inserting a `uint8_t` (1 byte) between `DATA` and `STATUS` adds 3 bytes of padding and shifts everything after it:

```
Correct map          After inserting uint8_t
0x00 DATA            0x00 DATA
0x04 STATUS          0x04 new byte + 3 padding
0x08 CTRL            0x08 STATUS  ❌ (really the CTRL register)
                     0x0C CTRL    ❌ (wrong register)
```
Reserved gaps must be declared explicitly, e.g. `uint32_t RESERVED;`.

## Practice Questions
1. Write `0x3` into bits [9:8] of `reg = 0xFFFFFFFF`. What is the result?
2. Write `0b101` into bits [6:4]: give shift, mask and value.
3. Read bits [15:12] from `0xABCD1234`.
4. Clear only bit 4 of a W1C status register. Why not use read-modify-write?
5. Why can a register pointer be both `volatile` and `const`?
6. `#define GPIO ((GPIO_TypeDef *)0x48000000U)` — `GPIO.ODR` or `GPIO->ODR`? Why?

## Interview One-liner
Mask write = read, clear the field with `~(mask << shift)`, OR in `(value << shift) & mask`, write once; W1C bits are cleared by writing only `1U << pos`; a cast makes an address a pointer, so struct overlays use `->`.
