# Quiz: Memory Layout, Boot and Startup

[← Back to notes](README.md)

Answer every question before you check the [answer sheet](answers.md). Pass mark: **80%**.

**Q1.** `static int c = 0;` at file scope lives in:
- a) .data
- b) .bss
- c) .rodata
- d) stack

**Q2.** `int *p = malloc(8);` inside a function. `p` lives in:
- a) heap
- b) stack
- c) .bss
- d) .data

**Q3.** The first entry of a Cortex-M vector table is:
- a) Reset_Handler address
- b) Initial stack pointer value
- c) NMI handler
- d) main()

**Q4.** If startup code forgets to zero `.bss`:
- a) Nothing happens
- b) Uninitialised globals start with random values
- c) Code won't link
- d) .text gets corrupted

**Q5.** On a microcontroller, `.text` usually:
- a) Is copied to RAM
- b) Executes in place from flash
- c) Is zeroed
- d) Lives on the stack

**Q6.** `_sbss = 0x2000_0100`, `_ebss = 0x2000_0180`. Iterations with `uint32_t *`:
- a) 16
- b) 32
- c) 128
- d) 64

**Q7.** Which exception cannot be masked?
- a) SysTick
- b) PendSV
- c) NMI
- d) IRQ0

**Q8.** Size of `struct { char a; int b; char c; }` on 32-bit:
- a) 6
- b) 8
- c) 12
- d) 16

**Q9.** Why is platform firmware stored in flash instead of the boot ROM?
- a) Flash is faster
- b) It's design-specific and must be updatable; ROM is fixed at the factory
- c) ROM is too big
- d) Linux requires it

**Q10.** Linux brings a secondary core online on an ARMv8-A SoC by:
- a) Writing the core's reset register directly
- b) An SMC call into EL3, handled by TF-A's PSCI service
- c) A HardFault
- d) A DMA transfer

**Q11.** `const int MAX = 10;` compared with `#define MAX 10`:
- a) Both have no type
- b) `const` is type-checked and may occupy `.rodata`
- c) `#define` uses memory
- d) Identical in all ways

**Q12.** A `weak` default handler means:
- a) It runs at low priority
- b) A user-defined function with the same name overrides it
- c) It can't be called
- d) It is placed in RAM
