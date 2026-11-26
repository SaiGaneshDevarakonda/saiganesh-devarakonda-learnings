# Answers: Memory Layout, Boot and Startup

[← Notes](README.md) · [Quiz](quiz.md)

## Quiz answer key
1. **b)** Zero-initialised → .bss.
2. **b)** The pointer is a local; only `*p` is on the heap.
3. **b)** Hardware loads SP before running any code.
4. **b)** RAM powers up with random content.
5. **b)** XIP: read-only code runs straight from flash.
6. **b)** `0x80` = 128 bytes / 4 = 32.
7. **c)** NMI is non-maskable.
8. **c)** 1 + 3 pad + 4 + 1 + 3 pad = 12.
9. **b)** ROM is immutable and generic; platform firmware is per design.
10. **b)** SMC → EL3 → PSCI CPU_ON.
11. **b)** Typed, scoped, usually in `.rodata`.
12. **b)** The linker picks the strong (user) definition.

## Practice question solutions
1. `int x;` → .bss. `static int y = 3;` in a function → .data. String literal → .rodata. The pointer → stack; the allocated block → heap.
2. Every C function, including `Reset_Handler`, needs a valid stack, so hardware loads SP before the first instruction.
3. `.data` variables must be writable (RAM) but their initial values must survive power-off (flash). `.text` is read-only and can execute in place from flash.
4. `0x20` = **32 bytes**, 32 / 4 = **8 iterations**.
5. A linker symbol has no storage; its address is the boundary. `_sdata` would read the memory at that address.
6. The ROM is fixed at the factory and shared by every customer; DDR type, timings and board layout differ per design, so that code must live in updatable platform firmware.
7. It's a fallback: if the application defines a function with the same name, the linker uses that one instead.
8. `#define`: preprocessor text, no type, no memory, no scope, invisible to the debugger. `const`: compiler-checked type, usually occupies `.rodata`, follows C scope, visible in the debugger.
