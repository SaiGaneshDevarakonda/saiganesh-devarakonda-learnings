# 07 · Preprocessor, Macros, Compilation Stages

**Quiz:** [quiz.md](quiz.md) · **Answers and solutions:** [answers.md](answers.md)

---

## Theory

### 1. The 4 compilation stages
```mermaid
flowchart LR
  A[main.c] -->|"Preprocessor<br/>gcc -E"| B[main.i]
  B -->|"Compiler<br/>gcc -S"| C[main.s]
  C -->|"Assembler<br/>gcc -c"| D[main.o]
  D -->|"Linker"| E[a.out / ELF]
```
| Stage | Does | Typical error |
|---|---|---|
| Preprocessor | `#include`, `#define`, `#if`, removes comments | `No such file or directory` (missing header) |
| Compiler | C → assembly, type checks | `expected ';'`, `assignment of read-only variable` |
| Assembler | assembly → machine code (`.o`) | rare |
| Linker | joins `.o` files and libraries, resolves symbols | `undefined reference to 'foo'`, `multiple definition` |

### 2. A macro is TEXT replacement
There's no type, no evaluation and no scope, and nothing gets brackets unless you write them.
```
 #define SQ(x)  x*x
 SQ(2+3)  →  2+3*2+3  =  11       ✗
 #define SQ(x)  ((x)*(x))         ✓ brackets around EACH argument AND the WHOLE body
```

### 3. The 5 macro traps
| Trap | Bad | Result |
|---|---|---|
| No argument brackets | `#define SQ(x) x*x` | `SQ(2+3)` = 11 |
| No outer brackets | `#define DBL(x) (x)+(x)` | `10*DBL(2)` = 22 |
| Constant without brackets | `#define B A+1` | `B*2` = A+1*2 |
| Side effects | `SQ(i++)` | `i++` runs twice → UB |
| Double evaluation with `?:` | `MAX(x++, y)` | x incremented twice (defined, but surprising) |
| Multi-statement | `#define SWAP(a,b) t=a; a=b; b=t;` | breaks inside an `if`. Wrap it in `do { … } while (0)` |
| Type alias | `#define PTR int*` then `PTR a, b;` | b is a plain `int`. Use `typedef` |
| Macros ignore scope | `#define N 10` … `int N = 5;` | becomes `int 10 = 5;` → error |

### 4. Special operators and macros
```
 #x        stringize      STR(hello) → "hello"     STR(1+2) → "1+2"  (not evaluated)
 a##b      token paste    CAT(x,y)   → xy
 __FILE__  __LINE__  __func__  __DATE__  → debug prints
 #ifndef X_H / #define X_H / ... / #endif → include guard (or #pragma once)
 #if 0 ... #endif                         → removes code, even code with comments inside
 gcc -DDEBUG                              → same as #define DEBUG
```

### 5. Macro vs `inline` vs `const`
| Use | When |
|---|---|
| `static inline` function | default for "small fast function": type-checked, args evaluated once, debuggable |
| macro | needs `#`/`##`, `__FILE__`/`__LINE__`, works on any type, or conditional compilation |
| `const` / `enum` | named constants with a type (prefer them to `#define` for values) |

### 6. Embedded must-know
```c
#define REG32(addr)  (*(volatile uint32_t *)(addr))
REG32(0x40001000u) = 0x1u;          /* write an MMIO register */
```

---

## Practice Questions
Solutions are in [answers.md](answers.md#practice-solutions).

1. Safe `MIN`/`MAX` macros. What still goes wrong with `MAX(i++, j)`?
2. A `SWAP(a, b)` macro using `do { … } while (0)`. Test it in an `if`/`else` without braces.
3. `ARRAY_SIZE(a)` = `sizeof(a)/sizeof((a)[0])`. Why is it wrong for an array passed to a function?
4. `DBG(fmt, ...)`: a variadic macro that prints `file:line func:` and compiles to nothing unless `-DDEBUG` is set.
5. `uart.h`: an include guard, `extern` declarations and register macros built on `REG32`.
6. Run `gcc -E`, `-S` and `-c` on a small file and see what each stage produces.

**Explain out loud:**
7. Macro vs `inline` vs `const`: when would you use each?
8. What happens at each of the 4 stages, and which one gives "undefined reference"?

---

## Interview One-liner
*"Macros are text replacement, so bracket every argument and the whole body. Never pass ++ to them, and prefer inline functions and typedef."*
