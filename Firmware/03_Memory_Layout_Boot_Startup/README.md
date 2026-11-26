# 03 · Memory Layout, Boot and Startup

[Quiz](quiz.md) · [Answers](answers.md)

## Theory

### 1. Where variables live

```
 High address
 ┌──────────────┐
 │    STACK  ↓  │  local variables, function calls (grows down)
 │    HEAP   ↑  │  malloc() (grows up; often avoided in firmware)
 ├──────────────┤
 │    .bss      │  globals/statics = 0 or uninitialised  → zeroed at boot
 ├──────────────┤
 │    .data     │  globals/statics with a value          → copied Flash→RAM at boot
 ├──────────────┤
 │    .rodata   │  const data, string literals           → stays in Flash
 │    .text     │  code                                  → Flash
 └──────────────┘
 Low address
```

```c
int a = 10;              // .data
int b;                   // .bss
static int c = 0;        // .bss  (zero-initialised goes to .bss, not .data)
const int d = 5;         // .rodata (in Flash next to .text, but its own section)

void f(void) {
    int e = 3;           // stack
    static int g = 7;    // .data (static locals live with the globals)
    int *p = malloc(4);  // p on the stack, *p (the 4 bytes) on the heap
}
```
```
STACK:  [ e ][ p ]──────┐
HEAP:                   └──► [ 4 bytes ]  ← *p
```
The pointer lives on the stack; the memory it points to is on the heap.

### 2. `const` vs `#define`, `static`

| | `#define MAX 10` | `const int MAX = 10;` |
|---|---|---|
| Handled by | Preprocessor (text paste) | Compiler |
| Type checking | ❌ | ✅ |
| Memory | None | Yes, usually `.rodata` (unless optimised away) |
| Scope | From the definition onward, ignores blocks | Normal C scope |
| Visible in debugger | ❌ | ✅ |

`static` on a function or global = visible only inside this **translation unit** (this `.c` file). `static` on a local = keeps its value between calls (stored in `.data`/`.bss`).

### 3. Struct padding and endianness

`struct { char a; int b; char c; }` on a 32-bit system = **12 bytes**: `a` at 0, 3 padding, `b` at 4–7 (aligned to its size), `c` at 8, then 3 padding so the total is a multiple of the largest member (4). Grouping the chars, `struct { int b; char a; char c; }` = 4 + 1 + 1 + 2 padding = **8 bytes**. Rule: order members largest to smallest.

Endianness belongs to the CPU/system:
```c
int x = 1;
if (*(char *)&x == 1)  /* little-endian: lowest byte stored first */
```

### 4. ROM vs Flash vs RAM

| Memory | Keeps data at power-off? | Writable? | Holds |
|---|---|---|---|
| **ROM** (mask ROM) | ✅ | ❌ fixed at the factory | Boot ROM |
| **Flash** | ✅ | ✅ slowly, erased in blocks | Program: `.text`, `.rodata`, `.data` initial values |
| **RAM** (SRAM/DDR) | ❌ | ✅ fast | `.data`, `.bss`, heap, stack at runtime |

Flash is like a ROM you can reprogram. Flash is **storage**, not RAM.

### 5. Power-on to `main()` (Cortex-M)

```
 POWER ON / RESET
        │
        ▼
 CPU reads VECTOR TABLE at address 0x0000_0000
 ┌──────────────────────────┐
 │ [0] Initial Stack Pointer│ → loaded into SP by hardware
 │ [1] Reset vector         │ → CPU jumps to Reset_Handler
 │ [2] NMI  [3] HardFault ..│
 │ [16+] peripheral IRQs    │
 └──────────────────────────┘
        │
        ▼
 Reset_Handler (startup code)
   1. Copy .data  Flash → RAM
   2. Zero  .bss  in RAM
   3. SystemInit() → clocks, PLL
   4. call main()
   5. while(1) if main ever returns
```

The stack pointer is entry [0] because **every C function, even `Reset_Handler`, needs a stack** for locals and return addresses, so the hardware loads SP before the first instruction runs.

`.data` must be copied because its initial values must survive power-off (so they're in Flash), but the variables must be writable (so they live in RAM). `.text` is not copied on a microcontroller: it is read-only and the CPU executes directly from Flash (**XIP**, execute-in-place). `.bss` must be zeroed because RAM powers up with random content: a global counter or a `NULL` pointer check would break.

```
FLASH:  [ .text ][ .rodata ][ .data init values ]──copy──┐
RAM:                       [ .data ][ .bss=0 ][ heap → ... ← stack ]
                              ▲─────────────────────────────┘
```

### 6. Startup code and linker symbols

The **linker script** creates labels marking section boundaries (s = start, e = end):

```
FLASH                                   RAM
┌────────────┐                          ┌────────────┐ ◄─ _sdata
│  .text     │                          │  .data     │
│  .rodata   │                          │            │
├────────────┤ ◄─ _sidata ── copy ────► ├────────────┤ ◄─ _edata
│ .data init │                          │  .bss      │ ◄─ _sbss
│  values    │                          │  (zeroed)  │
└────────────┘                          ├────────────┤ ◄─ _ebss
                                        │  heap ↑    │
                                        │  stack ↓   │
                                        └────────────┘ ◄─ _estack (top of RAM)
```
| Symbol | Meaning |
|---|---|
| `_sidata` | start of the **i**nit values of `.data` (in Flash) |
| `_sdata` / `_edata` | start / end of `.data` (in RAM) |
| `_sbss` / `_ebss` | start / end of `.bss` (in RAM) |
| `_estack` | end of the stack region = top of RAM |

```c
extern uint32_t _sidata, _sdata, _edata, _sbss, _ebss, _estack;
int main(void);

void Reset_Handler(void)
{
    uint32_t *src = &_sidata;
    uint32_t *dst = &_sdata;
    while (dst < &_edata) *dst++ = *src++;    // 1. copy .data

    for (dst = &_sbss; dst < &_ebss; )        // 2. zero .bss
        *dst++ = 0;

    SystemInit();                             // 3. clocks
    main();                                   // 4. go
    while (1);                                // 5. trap if main returns
}

__attribute__((section(".isr_vector")))       // placed at address 0
const uint32_t vector_table[] = {
    (uint32_t)&_estack,                       // [0] initial SP
    (uint32_t)Reset_Handler,                  // [1] reset
    /* NMI, HardFault, ... */
};
```
A linker symbol is **only an address label with no variable stored there**, so the code uses `&_sdata` (the boundary address). `_sdata` would read whatever happens to be stored at that address.

Example: `_sbss = 0x2000_0100`, `_ebss = 0x2000_0140` → `0x40` = 64 bytes, 64 / 4 = 16 iterations with `uint32_t *`.

### 7. The vector table entries

The **reset vector** is the entry (an address in the table); the **reset handler** is the function it points to. Every entry works the same way.

```
Index  Entry               When it runs                         Typical use
[0]    Initial SP          (not code, just the stack top value)
[1]    Reset               power-on / reset                     startup → main()
[2]    NMI                 non-maskable, can't be disabled      clock failure, watchdog
[3]    HardFault           any crash / fault                    debug: dump registers
[4-6]  MemManage/Bus/Usage specific faults                      bad address, bad instruction
[11]   SVCall              software `svc`                       RTOS system call
[14]   PendSV              pended by software                   RTOS context switch
[15]   SysTick             system timer tick                    RTOS tick / delays
[16+]  IRQ0, IRQ1, ...     peripheral interrupts                UART, timer, DMA, GPIO
```
Indices 0–15 are ARM system exceptions; 16+ are vendor peripheral IRQs. Every entry needs a valid address, but unused ones point to a default trap:

```c
void Default_Handler(void) { while (1); }
void NMI_Handler(void)      __attribute__((weak, alias("Default_Handler")));
void UART0_IRQHandler(void) __attribute__((weak, alias("Default_Handler")));
```
`weak` lets your own `UART0_IRQHandler()` replace the default automatically. A crash is debugged by breaking in HardFault and reading the stacked PC/LR. An RTOS uses SysTick for the tick and PendSV for the context switch. (ARMv8-A application cores instead use a table pointed to by `VBAR_ELx`, with Sync/IRQ/FIQ/SError entries per exception level.)

### 8. Microcontroller vs application SoC

```
MICROCONTROLLER                            APPLICATION SoC
┌───────── one chip ─────────┐            ┌──────── SoC ─────────────┐
│ CPU                        │            │ application cores        │
│ FLASH  (program, on-chip)  │            │ boot ROM (on-chip)       │
│ SRAM   (variables, on-chip)│            │ on-chip SRAM             │
└────────────────────────────┘            │ boot/management MCU      │
                                          └──────────┬───────────────┘
                                                     │ board
                                          ┌──────────┴──────────┐
                                          │ QSPI/OSPI NOR flash │ ← boot image
                                          │ eMMC / SD card      │   (selected by boot-mode pins)
                                          │ DDR (external RAM)  │
                                          └─────────────────────┘
```
| | Microcontroller | SoC running Linux |
|---|---|---|
| Where code runs | Directly from on-chip flash (XIP) | Images copied from flash/SD into on-chip SRAM or DDR, then run |
| What gets copied | Only `.data` + zeroing `.bss` | Whole images, stage by stage |

### 9. The SoC boot chain

```
POWER ON → power rails stable → power-on reset released
  │  (the oscillator is just an always-running clock source)
  ▼
① Boot/management MCU runs the BOOT ROM, in place from ROM
  │  (ROM code is not copied to SRAM; it only uses its local RAM for stack/data)
  │  reads the boot-mode pins (latched at reset)
  │  sets up clocks/pins for that interface: QSPI / OSPI / SD / eMMC / JTAG
  ▼
② Boot ROM loads + authenticates the PLATFORM FIRMWARE from the boot image
  │  boot image lives in FLASH on the board (or is sent over JTAG in the lab)
  ▼
③ Platform firmware brings up the rest: clocks, interconnect, DDR init,
  │  programmable logic; loads TF-A and U-Boot into on-chip SRAM / DDR
  ▼
④ Releases the application cores from reset; they START at EL3 running TF-A
  ▼
⑤ TF-A (EL3) → U-Boot (EL2) → Linux (EL1)
```

**Why the boot ROM doesn't do everything:**

| Boot ROM | Platform firmware |
|---|---|
| Burned into silicon, can never change | Stored in the boot image, **updatable** |
| Must be tiny and bug-free (a bug = chip respin) | Can be large and fixed in a new release |
| Same for every customer and board | Built for one design: its DDR, clocks, images |
| Only inits the boot device, loads and authenticates the next stage | Everything else |

The management MCU is the hardware; the platform firmware is the software it runs after the ROM. It stays resident after boot for **power management** (Linux asks via TF-A to power off a core), **error management** (functional-safety errors are reported to it), secure services and watchdogs. TF-A also stays resident: Linux uses an `SMC` instruction to trap into EL3, and TF-A handles **PSCI** calls (CPU_ON, CPU_OFF, suspend, reset).

On-chip SRAM is the technology; a named on-chip memory block is one SRAM region. Not all SRAM in the chip is the same block (the management MCU has its own).

Each stage (platform firmware, TF-A, U-Boot) has its **own small startup code** that sets the stack, copies `.data`, zeroes `.bss` and jumps to its own `main`, exactly as in section 6.

## Practice Questions
1. Where does each live: a global `int x;`, a `static int y = 3;` inside a function, a string literal, the pointer returned by `malloc` vs the memory it points to?
2. Why is SP the first vector-table entry?
3. Why is `.data` copied but `.text` not, on a microcontroller?
4. `_sdata = 0x2000_0000`, `_edata = 0x2000_0020`: how many bytes and `uint32_t` iterations does the copy loop do?
5. Why `&_sdata` and not `_sdata`?
6. Why can't the boot ROM contain the DDR initialisation for every board?
7. What does `weak` do on a default interrupt handler?
8. Give three differences between `#define` and `const`.

## Interview One-liner
Reset loads SP from vector [0] and jumps to the reset handler, which copies `.data` from flash, zeroes `.bss` and calls `main`; on an SoC, boot is a chain of loaders (immutable boot ROM → updatable platform firmware → TF-A → U-Boot → Linux), each copying the next image from flash into RAM and running its own startup code.
