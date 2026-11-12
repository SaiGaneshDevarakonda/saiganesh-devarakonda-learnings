# Answers: Preprocessor, Macros, Compilation

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz: Preprocessor, macros, compilation (20 questions)

1. **b)** `11`: 2+3*2+3 = 11.
2. **b)** `25`: Brackets fix it.
3. **b)** `22`: 10*(2)+(2) = 22. Needs outer brackets.
4. **c)** UB: ((i++)*(i++)) → UB.
5. **b)** `6 7`: ?: is a sequence point. x++ runs twice → m = 6, x = 7.
6. **b)** `a int*, b int`: int* a, b; → * binds only to a.
7. **a)** `Both int*`: typedef is a real type alias.
8. **a)** hello: # stringizes the argument.
9. **b)** `10`: ## pastes tokens → xy.
10. **b)** So the macro acts as ONE statement: safe inside if/else without braces: Otherwise only the first statement is inside the if.
11. **b)** Compile error: Becomes int 10 = 5;
12. **c)** `-E`: -E preprocess, -S assembly, -c object.
13. **c)** Assembler: gcc -c.
14. **d)** Linker: Declared but never defined.
15. **b)** Prevents the header being included twice: Include guard.
16. **b)** B: #if 0 removes the block.
17. **b)** `7`: __LINE__ = current line.
18. **b)** `7`: 5+1*2 = 7. Write #define B (A+1).
19. **b)** Type checking, arguments evaluated once, debuggable
20. **a)** Prints dbg: -D defines the macro.


<a id="practice-solutions"></a>
## Practice solutions

### 1. MIN / MAX
```c
#include <stdio.h>
#define MIN(a, b) ((a) < (b) ? (a) : (b))
#define MAX(a, b) ((a) > (b) ? (a) : (b))
int main(void) {
    int i = 5, j = 3;
    printf("%d %d\n", MIN(2 + 1, 4), MAX(i, j));   /* 3 5 */
    int m = MAX(i++, j);                            /* ((i++) > (j) ? (i++) : (j)) */
    printf("%d %d\n", m, i);                        /* 6 7: i was incremented twice */
    return 0;
}
```
The brackets fix precedence but **not** double evaluation. Use a `static inline int max(int a, int b)` when the arguments may have side effects.

### 2. SWAP with do-while(0)
```c
#include <stdio.h>
#define SWAP(a, b) do { int t_ = (a); (a) = (b); (b) = t_; } while (0)
int main(void) {
    int x = 1, y = 2, flag = 1;
    if (flag) SWAP(x, y);      /* one statement, so the else below still pairs correctly */
    else      x = 100;
    printf("%d %d\n", x, y);   /* 2 1 */
    return 0;
}
```

### 3. ARRAY_SIZE
```c
#include <stdio.h>
#define ARRAY_SIZE(a) (sizeof(a) / sizeof((a)[0]))
void f(int a[]) { printf("in f: %zu\n", ARRAY_SIZE(a)); }   /* 8/4 = 2 on 64-bit: WRONG */
int main(void) {
    int a[10];
    printf("in main: %zu\n", ARRAY_SIZE(a));                /* 10 */
    f(a);
    return 0;
}
```
Inside `f`, `a` is an `int *`, so `sizeof(a)` is the pointer size. GCC even warns: `sizeof on array function parameter`.

### 4. Debug print macro
```c
#include <stdio.h>
#ifdef DEBUG
#define DBG(fmt, ...) printf("%s:%d %s: " fmt "\n", __FILE__, __LINE__, __func__, ##__VA_ARGS__)
#else
#define DBG(fmt, ...) do { } while (0)
#endif
int main(void) {
    int v = 42;
    DBG("value = %d", v);      /* prints only with: gcc -DDEBUG */
    (void)v;                   /* silence "unused" when DEBUG is off */
    DBG("no args");
    return 0;
}
```
`##__VA_ARGS__` is a GCC extension that drops the comma when no extra arguments are given.

### 5. A header with an include guard
```c
/* uart.h */
#ifndef UART_H
#define UART_H
#include <stdint.h>
#define REG32(addr)   (*(volatile uint32_t *)(addr))
#define UART_BASE     0x40001000u
#define UART_DATA     REG32(UART_BASE + 0x00u)
#define UART_STATUS   REG32(UART_BASE + 0x04u)
#define UART_TX_READY (1u << 0)
extern volatile uint32_t uart_rx_count;     /* defined once in uart.c */
void uart_init(uint32_t baud);
void uart_putc(char c);
#endif /* UART_H */
```

### 6. The stages on the command line
```
gcc -E main.c -o main.i    # preprocessed: macros expanded, headers pasted in
gcc -S main.c -o main.s    # assembly
gcc -c main.c -o main.o    # object file (machine code, unresolved symbols)
gcc main.o -o app          # link
nm main.o                  # 'U' = undefined symbols the linker must resolve
```

### 7. Macro vs inline vs const
- **`static inline`**: small functions. They're type-checked, evaluate each argument once, and you can step into them in a debugger.
- **Macros**: when you need `#`/`##`, `__FILE__`/`__LINE__`, conditional compilation, or code that works on any type.
- **`const` / `enum`**: named constants. They have a type and a scope, and show up in the debugger.

### 8. The stages
1. The **preprocessor** pastes headers and expands macros.
2. The **compiler** checks syntax and types and produces assembly.
3. The **assembler** produces a `.o` file.
4. The **linker** combines the `.o` files and libraries and resolves every symbol.

"Undefined reference" comes from the **linker**: the symbol was declared, but nothing defines it.
