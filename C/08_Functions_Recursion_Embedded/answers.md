# Answers: Functions, Recursion, Embedded C

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz: Functions, recursion, embedded C, linked lists (20 questions)

1. **b)** `10`: 4+3+2+1+0.
2. **c)** `120`: 5!
3. **b)** `1 2 3`: Print after the call → runs while frames pop.
4. **a)** `3 2 1`: Print before recursing.
5. **c)** `Stack overflow (crash)`: No base case.
6. **b)** `8`: 0 1 1 2 3 5 8.
7. **c)** `O(2ⁿ)`: Each call branches in two.
8. **b)** `5`: Jump table.
9. **c)** `12`: Callback: twice(twice(3)).
10. **c)** Both
11. **c)** Clear the IRQ flag, save the data, set a volatile flag: Keep ISRs short; defer work.
12. **b)** Slow, not reentrant, may use locks/buffers main was using
13. **b)** `char *to_str(int n) { static char buf[12]; sprintf(buf, "%d", n); return buf; }`: Shared static buffer.
14. **b)** `void handler(void)`: Called by hardware via the vector table.
15. **b)** `The ISR re-enters again and again (IRQ storm)`: Same idea as signalling end-of-interrupt to an interrupt controller.
16. **b)** `3`
17. **b)** `slow == fast at some point`
18. **b)** `3`: Middle in one pass.
19. **b)** list is unchanged and the node leaks: Use struct Node **head.
20. **b)** `Use after free: cur->next is read after free`: Save next first.


<a id="practice-solutions"></a>
## Practice solutions

### 1–3. Function pointer, array, typedef
```c
#include <stdio.h>
int add(int a, int b) { return a + b; }
int sub(int a, int b) { return a - b; }
int mul(int a, int b) { return a * b; }
int dvd(int a, int b) { return b ? a / b : 0; }

typedef int (*op_t)(int, int);                  /* 3: alias name is op_t */

int main(void) {
    int (*fp)(int, int) = add;                  /* 1: prototype with the name replaced by (*fp) */
    printf("%d\n", fp(2, 3));                   /* 5 */

    int (*ops_raw[4])(int, int) = { add, sub, mul, dvd };   /* 2: without typedef */
    op_t ops[4] = { add, sub, mul, dvd };                    /* 3: with typedef */
    for (int i = 0; i < 4; i++) printf("%d ", ops[i](10, 2)); /* 12 8 20 5 */
    printf("\n%d\n", ops_raw[2](3, 3));                       /* 9 */
    return 0;
}
```

### 4. Callback
```c
#include <stdio.h>
int mul(int a, int b) { return a * b; }
int calc(int (*op)(int, int), int a, int b) { return op(a, b); }
int main(void) { printf("%d\n", calc(mul, 6, 7)); return 0; }   /* 42 */
```

### 5. Command table
```c
#include <stdio.h>
#include <string.h>
static void led_cmd(void)   { printf("LED toggled\n"); }
static void reset_cmd(void) { printf("Resetting\n"); }
static void help_cmd(void)  { printf("cmds: led reset help\n"); }
typedef struct { const char *name; void (*fn)(void); } cmd_t;
static const cmd_t cmds[] = { {"led", led_cmd}, {"reset", reset_cmd}, {"help", help_cmd} };
int run(const char *name) {
    for (size_t i = 0; i < sizeof cmds / sizeof cmds[0]; i++)
        if (strcmp(cmds[i].name, name) == 0) { cmds[i].fn(); return 0; }
    printf("unknown: %s\n", name);
    return -1;
}
int main(void) { run("led"); run("foo"); return 0; }
```

### 6. Bit-ops menu
```c
#include <stdio.h>
typedef void (*bitop_t)(unsigned *, int);
void set_bit(unsigned *p, int b)    { *p |=  (1u << b); }
void clear_bit(unsigned *p, int b)  { *p &= ~(1u << b); }
void toggle_bit(unsigned *p, int b) { *p ^=  (1u << b); }
int main(void) {
    bitop_t ops[] = { set_bit, clear_bit, toggle_bit };
    const char *name[] = { "set", "clear", "toggle" };
    unsigned x = 0x10;
    int op = 2, bit = 4;                         /* e.g. read with scanf("%d %d", &op, &bit) */
    if (op >= 0 && op < 3) ops[op](&x, bit);     /* always bounds-check the index */
    printf("%s -> 0x%X\n", name[op], x);         /* toggle -> 0x0 */
    return 0;
}
```

### 7. Count bits via a callback
```c
#include <stdio.h>
#include <stdint.h>
int loop_cnt(uint32_t x) { int c = 0; while (x) { c += x & 1u; x >>= 1; } return c; }
int kern_cnt(uint32_t x) { int c = 0; while (x) { x &= x - 1; c++; } return c; }
int count(uint32_t x, int (*algo)(uint32_t)) { return algo(x); }
int main(void) { printf("%d %d\n", count(0xF0, loop_cnt), count(0xF0, kern_cnt)); return 0; }
```

### 8. Calculator table
```c
#include <stdio.h>
int add(int a, int b) { return a + b; } int sub(int a, int b) { return a - b; }
int mul(int a, int b) { return a * b; } int dvd(int a, int b) { return b ? a / b : 0; }
struct op { char sym; int (*fn)(int, int); };
static const struct op table[] = { {'+', add}, {'-', sub}, {'*', mul}, {'/', dvd} };
int eval(char sym, int a, int b, int *res) {
    for (size_t i = 0; i < sizeof table / sizeof table[0]; i++)
        if (table[i].sym == sym) { *res = table[i].fn(a, b); return 0; }
    return -1;
}
int main(void) { int r; if (eval('*', 6, 7, &r) == 0) printf("%d\n", r); return 0; }   /* 42 */
```

### 9. Factorial and Fibonacci
```c
#include <stdio.h>
unsigned long fact_r(unsigned n) { return n <= 1 ? 1 : n * fact_r(n - 1); }
unsigned long fact_i(unsigned n) { unsigned long r = 1; while (n > 1) r *= n--; return r; }
unsigned long fib_r(unsigned n) { return n < 2 ? n : fib_r(n - 1) + fib_r(n - 2); }      /* O(2^n) */
unsigned long fib_i(unsigned n) { unsigned long a = 0, b = 1; while (n--) { unsigned long t = a + b; a = b; b = t; } return a; } /* O(n) */
int main(void) { printf("%lu %lu %lu %lu\n", fact_r(5), fact_i(5), fib_r(7), fib_i(7)); return 0; }  /* 120 120 13 13 */
```
Stack cost: the recursive versions use one frame per call (depth n). The iterative ones use O(1) stack.

### 10. Sum of digits
```c
#include <stdio.h>
int digit_sum(int n) { return n ? n % 10 + digit_sum(n / 10) : 0; }
int main(void) { printf("%d\n", digit_sum(1234)); return 0; }   /* 10 */
```

### 11. Fast power
```c
#include <stdio.h>
long power(long x, unsigned n) {
    if (n == 0) return 1;
    long half = power(x, n / 2);          /* each step halves n → O(log n) */
    return (n % 2) ? half * half * x : half * half;
}
int main(void) { printf("%ld\n", power(2, 10)); return 0; }    /* 1024 */
```

### 12. Reverse a string recursively
```c
#include <stdio.h>
#include <string.h>
void rev(char *s, int l, int r) {
    if (l >= r) return;
    char t = s[l]; s[l] = s[r]; s[r] = t;
    rev(s, l + 1, r - 1);
}
int main(void) { char s[] = "abc"; rev(s, 0, (int)strlen(s) - 1); printf("%s\n", s); return 0; }   /* cba */
```

### 13. Permutations
```c
#include <stdio.h>
#include <string.h>
static void sw(char *a, char *b) { char t = *a; *a = *b; *b = t; }
void permute(char *s, int l, int r) {
    if (l == r) { printf("%s ", s); return; }
    for (int i = l; i <= r; i++) {
        sw(&s[l], &s[i]);          /* choose */
        permute(s, l + 1, r);      /* explore */
        sw(&s[l], &s[i]);          /* undo (backtrack) */
    }
}
int main(void) { char s[] = "abc"; permute(s, 0, (int)strlen(s) - 1); printf("\n"); return 0; }
/* abc acb bac bca cba cab */
```

### 14. UART RX ring buffer (ISR + main loop)
```c
#include <stdint.h>
#include <stdio.h>
#define RB_SIZE 64u                                   /* power of 2 → cheap wrap with & */
static volatile uint8_t  rb[RB_SIZE];
static volatile uint32_t head = 0;                    /* written only by the ISR  */
static volatile uint32_t tail = 0;                    /* written only by main     */

/* Called from the UART RX interrupt with the received byte (read from the DATA register on hardware). */
void uart_rx_isr(uint8_t byte) {
    uint32_t next = (head + 1u) & (RB_SIZE - 1u);
    if (next != tail) { rb[head] = byte; head = next; }   /* drop the byte if full */
    /* on hardware: clear the RX interrupt flag here */
}
int rb_get(uint8_t *out) {
    if (tail == head) return 0;                       /* empty */
    *out = rb[tail];
    tail = (tail + 1u) & (RB_SIZE - 1u);
    return 1;
}
int main(void) {
    const char *msg = "hi";                           /* simulate interrupts */
    for (const char *p = msg; *p; p++) uart_rx_isr((uint8_t)*p);
    uint8_t c;
    while (rb_get(&c)) putchar(c);                    /* main loop processes data */
    putchar('\n');
    return 0;
}
```
This is safe without locks because only the ISR writes `head` and only main writes `tail`, and on a 32-bit CPU each index update is a single aligned store.

### 15. Non-reentrant → reentrant
```c
#include <stdio.h>
/* NOT reentrant: one shared static buffer */
char *to_str_bad(int n) { static char buf[12]; snprintf(buf, sizeof buf, "%d", n); return buf; }
/* Reentrant: the caller owns the buffer, no shared state */
char *to_str(int n, char *buf, size_t len) { snprintf(buf, len, "%d", n); return buf; }
int main(void) {
    char a[12], b[12];
    printf("%s %s\n", to_str(1, a, sizeof a), to_str(2, b, sizeof b));   /* 1 2 */
    return 0;
}
```
(`snprintf` itself isn't guaranteed to be async-signal-safe. The point here is that the function doesn't share state.)

### 16. Why ISRs must be short
While an ISR runs, equal and lower priority interrupts wait, so long ISRs add latency and can make you miss events. Do the minimum in the ISR: clear the flag, grab the data, set a flag or push to a buffer. Do the processing (parsing, printing, maths) in the main loop or a task.

### 17. Reentrancy and `volatile`
A reentrant function works correctly even if it's interrupted and called again before the first call finishes: it uses only arguments and locals, and no shared static or global data. `volatile` only forces real memory reads and writes. It does **not** make `x++` atomic, so shared read-modify-write data still needs a critical section (disabling IRQs) or atomic instructions.
