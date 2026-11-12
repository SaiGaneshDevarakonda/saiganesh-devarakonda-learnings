# Answers: Storage Classes, const, volatile

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz: Storage classes, const, volatile, memory layout (20 questions)

1. **b)** `1 2 3`: A static local is initialised once and keeps its value between calls.
2. **b)** `Garbage (indeterminate)`: Local auto variables live on the stack and are not initialised. Only globals/statics default to 0.
3. **b)** `0`: Globals go in .bss, which startup code zeroes.
4. **c)** .data: Initialised to non-zero → .data.
5. **a)** Both .bss: Uninitialised OR zero-initialised → .bss.
6. **c)** `UB (usually segfault)`: The literal is in read-only .rodata. Modifying it is UB.
7. **a)** Hello: The array gets its own writable copy of the string.
8. **c)** UB: dangling pointer: x's stack frame is gone after return. Fix: static int x, or malloc.
9. **b)** `No: linker error (internal linkage)`: A static global is visible only in its own file.
10. **b)** `Declares x; memory is defined in another file`: extern is a declaration only; no storage is allocated.
11. **c)** Compile error: In C you cannot take the address of a register variable.
12. **b)** Compile error: Static/global initialisers in C must be compile-time constants.
13. **b)** `*p = 5;`: const int *p → the data is locked through p; the pointer can move.
14. **b)** `p = &b;`: int *const p → the pointer is locked, the data can change.
15. **c)** UB: The cast silences the compiler, but modifying a const object is UB.
16. **b)** Infinite loop: the compiler keeps flag in a register: Fix: volatile int flag.
17. **b)** `No: ++ is read-modify-write; an ISR can interrupt in between`: volatile only stops caching. Disable interrupts or use atomics.
18. **b)** `Code can't write it; hardware can change it, so always re-read`: Classic read-only hardware status register.
19. **b)** Function is visible only inside a.c: A static function has internal linkage (file-private).
20. **b)** `It is in .bss: only its size is stored; startup code zeroes it`: Startup code (crt0) zeroes .bss in RAM before main().


<a id="practice-solutions"></a>
## Practice solutions

### 1. Call counter with a static local
```c
#include <stdio.h>
int call_count(void) {
    static int count = 0;      /* initialised once, keeps its value between calls */
    return ++count;
}
int main(void) {
    call_count(); call_count();
    printf("%d\n", call_count());   /* 3 */
    return 0;
}
```
A global would let **any** file change the counter. The `static` local keeps it private to the function and still alive for the whole program.

### 2. `extern` and `static` across two files
```c
/* a.c */
int shared = 42;                    /* definition: memory lives here       */
static void helper(void) { }       /* internal linkage: only a.c sees it  */
void use_helper(void) { helper(); }
```
```c
/* b.c */
#include <stdio.h>
extern int shared;                  /* declaration only */
void use_helper(void);
int main(void) { printf("%d\n", shared); use_helper(); return 0; }
```
If `b.c` calls `helper()` directly, both files compile, but the **linker** fails with `undefined reference to 'helper'`.

### 3. Volatile register access
```c
#include <stdint.h>
#define UART_BASE   0x40001000u
#define UART_CTRL   (*(volatile uint32_t *)(UART_BASE + 0x0))
#define UART_STATUS (*(volatile uint32_t *)(UART_BASE + 0x4))

void set_bit(unsigned n)     { UART_CTRL |= (1u << n); }
void wait_until_ready(void)  { while ((UART_STATUS & 1u) == 0u) { } }   /* re-read every loop */
```
This is bare-metal only: on a PC, the address 0x40001000 isn't mapped.

### 4. Where things live
```c
#include <stdio.h>
#include <stdlib.h>
int global = 1;
int main(void) {
    int local = 0;
    static int s_local = 0;
    int *heap = malloc(4);
    const char *lit = "lit";
    printf("text    %p\n", (void *)main);
    printf("rodata  %p\n", (void *)lit);
    printf("data    %p\n", (void *)&global);
    printf("bss     %p\n", (void *)&s_local);
    printf("heap    %p\n", (void *)heap);
    printf("stack   %p\n", (void *)&local);
    free(heap);
    return 0;
}
```
The typical order on Linux is text < rodata < data < bss < heap ≪ stack. Exact addresses vary because of ASLR.

### 5. Returning a local array
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
/* Fix 1: static storage (lives forever, but shared and not reentrant) */
char *get_name1(void) { static char n[] = "sai"; return n; }
/* Fix 2: heap (the caller must free) */
char *get_name2(void) { char *n = malloc(4); if (n) strcpy(n, "sai"); return n; }
/* Fix 3: caller provides the buffer (best for firmware) */
void get_name3(char *buf, size_t len) { snprintf(buf, len, "%s", "sai"); }
int main(void) {
    char b[8];
    char *h = get_name2();
    get_name3(b, sizeof b);
    printf("%s %s %s\n", get_name1(), h, b);
    free(h);
    return 0;
}
```

### 6. `const volatile`
Yes. A read-only hardware status register: `const` stops **our code** writing to it, and `volatile` makes the compiler re-read it because **hardware** changes it.

### 7. `.data` vs `.bss`
- **`.data`** holds globals and statics with non-zero initial values. Those values are stored in the binary (flash) and copied to RAM by the startup code.
- **`.bss`** holds globals and statics that are zero or uninitialised. Only the size is stored, and the startup code zeroes the region.

Both are done in the reset/startup code (crt0) before `main()` runs.
