# 08 · Functions, Recursion, Function Pointers, Embedded C

**Quiz:** [quiz.md](quiz.md) · **Answers and solutions:** [answers.md](answers.md)

> Linked lists are covered in [Data_Structures/01_Linked_List](../../Data_Structures/01_Linked_List/README.md).

---

## Theory

### 1. The call stack and recursion
```
 f(3) calls f(2) calls f(1) ...             STACK (grows ↓)
                                    ┌────────────────────┐
 Each call pushes a FRAME:          │ f(3): n=3, ret addr│
  arguments, return address,        ├────────────────────┤
  saved registers, locals           │ f(2): n=2, ret addr│
                                    ├────────────────────┤
 No base case → frames forever      │ f(1): n=1, ret addr│ ← base case returns
  → STACK OVERFLOW                  └────────────────────┘   frames pop in reverse
```
| Where the print is | Output of `p(3)` |
|---|---|
| **before** the recursive call | 3 2 1 (in calling order) |
| **after** the recursive call | 1 2 3 (runs while the frames pop) |

Naive recursive Fibonacci is **O(2ⁿ)**, because each call branches in two. The iterative version is O(n) with O(1) stack. On a microcontroller with a small stack, prefer iteration.

### 2. Function pointers

**The one rule for writing ANY function pointer:**
```
 Step 1: copy the function's prototype       void Setbit(int *ptr, int bit);
 Step 2: replace the NAME with (*fp)         void (*fp)(int *ptr, int bit);
 Step 3: (optional) drop parameter names     void (*fp)(int *, int);
 Array of them:                              void (*ops[3])(int *, int);
```

**typedef rule: write a normal variable declaration, then put `typedef` in front. The variable's name becomes the type name.**
| Variable declaration | With `typedef` | Alias name |
|---|---|---|
| `int myint;` | `typedef int myint;` | `myint` |
| `int arr5[5];` | `typedef int arr5[5];` | `arr5` |
| `void (*bitop_t)(int *, int);` | `typedef void (*bitop_t)(int *, int);` | `bitop_t` |
```
 typedef  void  ( * bitop_t )  ( int *, int );
          ────    ───────────    ─────────────
          return    ALIAS NAME    parameters
          type      (in the middle!)
```
After that, `bitop_t fp;` reads just like `int x;`.

**Storing vs calling:**
| Want to… | Write |
|---|---|
| store a function in a pointer | `fp = Setbit;` or `ops[0] = Setbit;` (no brackets after the name) |
| call it | `fp(&value, 1);` or `ops[i](&value, 1);` (brackets with the arguments). `(*fp)(…)` also works |
| pass it to another function (callback) | `apply(Setbit, &value, 1);` |

```c
typedef void (*bitop_t)(int *, int);
bitop_t ops[3] = { Setbit, Clearbit, Togglebit };   /* jump table */
for (int i = 0; i < 3; i++)
    ops[i](&value, 1);                              /* CALL function i */
```
Common mistakes:
- `ops[i] = value;` **assigns** an int to the pointer. It doesn't call anything, and it's a compile error.
- `(void *)Setbit` isn't portable C: a function pointer must have the matching function type.
- Calling a function before its prototype or definition.

Jump tables like this are how **interrupt vector tables** and **command dispatchers** work.

### 3. ISR rules
```
 volatile int flag = 0;
 void UART_IRQHandler(void) {     ← no args, no return value (called by hardware)
     clear_irq_flag();            ← forget this → ISR fires again and again (IRQ storm)
     flag = 1;                    ← set a flag / copy data; keep it SHORT
 }
 main: while (1) { if (flag) { flag = 0; process(); } }
```
| ❌ Never in an ISR | Why |
|---|---|
| `printf`, `malloc`, `free` | slow, not reentrant, may take locks main is holding |
| delays or blocking waits | blocks other interrupts |
| heavy float maths | latency, plus FPU context saving |

**Reentrant** means safe to interrupt and call again mid-way. That means no `static`/global state and no non-reentrant library calls. `volatile` makes shared data visible, but **not** atomic.

---

## Practice Questions
Solutions are in [answers.md](answers.md#practice-solutions).

**Function pointers**
| # | Problem | Input → Output |
|---|---|---|
| 1 | Pointer to `int add(int, int)`, then call it | `add(2,3)` via pointer → `5` |
| 2 | Array of 4 pointers (`add`, `sub`, `mul`, `div`) + a loop calling all on `(10, 2)` | `12 8 20 5` |
| 3 | `typedef` the type from 2 and rewrite the array with it | same |
| 4 | Callback: `int calc(int (*op)(int,int), int a, int b)` | `calc(mul, 6, 7)` → `42` |
| 5 | Command table `struct { const char *name; void (*fn)(void); }` + lookup by name | `"led"` → calls `led_cmd()` |
| 6 | Bit-ops menu with a function pointer array (set/clear/toggle) | `op=2, bit=4, x=0x10` → `0x00` |
| 7 | Count set bits via a callback: `int count(uint32_t x, int (*algo)(uint32_t))` | `0xF0` → `4` |
| 8 | Calculator table `struct { char op; int (*fn)(int,int); }`, dispatch on the char | `'*', 6, 7` → `42` |

**Recursion**
| # | Problem | Input → Output |
|---|---|---|
| 9 | Factorial and Fibonacci: recursive and iterative | `5` → `120`, `fib(7)` → `13` |
| 10 | Sum of digits | `1234` → `10` |
| 11 | Power `x^n` in O(log n) | `2, 10` → `1024` |
| 12 | Reverse a string recursively | `"abc"` → `"cba"` |
| 13 | All permutations of a string | `"abc"` → `abc acb bac bca cba cab` |

**Embedded**
| # | Problem |
|---|---|
| 14 | UART RX: the ISR stores the byte in a `volatile` ring buffer, and the main loop processes it |
| 15 | Rewrite a non-reentrant function (static buffer) as reentrant |

**Explain out loud:**
16. Why must ISRs be short, and what do you defer to the main loop?
17. What does reentrant mean? Is `volatile` enough to make shared data safe?

---

## Interview One-liner
*"Write the prototype and replace the name with (*fp). Call it like a normal function. An array of them is a jump table, like a vector table."*
