# Quiz: Functions, Recursion, Embedded C

[← Back to notes](README.md)

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz: Functions, recursion, embedded C, linked lists (20 questions)


**Q1.**

```c
int f(int n) { if (n == 0) return 0; return n + f(n - 1); }
printf("%d", f(4));
```

- a) `4`
- b) `10`
- c) `24`
- d) Stack overflow


**Q2.**

```c
int f(int n) { if (n <= 1) return 1; return n * f(n - 1); }
printf("%d", f(5));
```

- a) `15`
- b) `60`
- c) `120`
- d) `24`


**Q3.**

```c
void p(int n) { if (n == 0) return; p(n - 1); printf("%d ", n); }
p(3);
```

- a) `3 2 1`
- b) `1 2 3`
- c) `0 1 2 3`
- d) `3`


**Q4.**

```c
void p(int n) { if (n == 0) return; printf("%d ", n); p(n - 1); }
p(3);
```

- a) `3 2 1`
- b) `1 2 3`
- c) `3 2 1 0`
- d) `1`


**Q5.**

```c
int f(int n) { return f(n - 1); }
f(5);
```

- a) Returns 0
- b) Compile error
- c) `Stack overflow (crash)`
- d) Returns -1


**Q6.**

```c
int fib(int n) { return n < 2 ? n : fib(n - 1) + fib(n - 2); }
printf("%d", fib(6));
```

- a) `5`
- b) `8`
- c) `13`
- d) `6`


**Q7.**

Time complexity of that naive recursive fib(n)?

- a) `O(n)`
- b) `O(n²)`
- c) `O(2ⁿ)`
- d) `O(log n)`


**Q8.**

```c
int add(int a,int b){return a+b;} int sub(int a,int b){return a-b;}
typedef int (*op_t)(int,int);
op_t ops[] = { add, sub };
printf("%d", ops[1](7, 2));
```

- a) `9`
- b) `5`
- c) Compile error
- d) Address of sub


**Q9.**

```c
int twice(int x) { return 2 * x; }
int apply(int (*f)(int), int v) { return f(f(v)); }
printf("%d", apply(twice, 3));
```

- a) `6`
- b) `9`
- c) `12`
- d) Compile error


**Q10.**

```c
int sq(int x) { return x * x; }
int (*fp)(int) = sq;
Which call(s) are valid?
```

- a) `Only fp(3)`
- b) `Only (*fp)(3)`
- c) Both
- d) Neither


**Q11.**

Which is the BEST thing to do inside an ISR?

- a) printf the received byte
- b) malloc a buffer
- c) Clear the IRQ flag, save the data, set a volatile flag
- d) Wait 10 ms


**Q12.**

Why is printf() unsafe inside an ISR?

- a) It can't print numbers
- b) Slow, not reentrant, may use locks/buffers main was using
- c) Forbidden by the compiler
- d) It is safe


**Q13.**

Which function is NOT reentrant?

- a) `int add(int a, int b) { return a + b; }`
- b) `char *to_str(int n) { static char buf[12]; sprintf(buf, "%d", n); return buf; }`
- c) `void copy(char *d, const char *s) { while ((*d++ = *s++)); }`
- d) `int sq(int x) { return x * x; }`


**Q14.**

Typical signature of a Cortex-M interrupt handler?

- a) `int handler(int irq)`
- b) `void handler(void)`
- c) `void handler(int *regs)`
- d) `int handler(void)`


**Q15.**

The ISR forgets to clear the peripheral interrupt-pending flag. Result?

- a) Nothing
- b) `The ISR re-enters again and again (IRQ storm)`
- c) Compile error
- d) Interrupt disabled forever


**Q16.**

```c
List 1 → 2 → 3 → NULL is reversed with prev/cur/next.
At the end, head = prev points to…
```

- a) `1`
- b) `3`
- c) NULL
- d) `2`


**Q17.**

Floyd's cycle detection (slow 1 step, fast 2 steps): a loop exists if…

- a) fast becomes NULL
- b) `slow == fast at some point`
- c) slow reaches head again
- d) `fast->next == slow->next`


**Q18.**

List 1→2→3→4→5, slow/fast until fast or fast->next is NULL. slow is at…

- a) `2`
- b) `3`
- c) `4`
- d) `5`


**Q19.**

```c
void push(struct Node *head, int v) { ... head = n; }
push(list, 5);   // in main
```

- a) 5 is added at the front
- b) list is unchanged and the node leaks
- c) Compile error
- d) Crash always


**Q20.**

```c
while (cur) {
  free(cur);
  cur = cur->next;
}
```

- a) Nothing wrong
- b) `Use after free: cur->next is read after free`
- c) Memory leak
- d) Double free


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
