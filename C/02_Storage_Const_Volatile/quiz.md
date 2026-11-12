# Quiz: Storage Classes, const, volatile

[← Back to notes](README.md)

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz: Storage classes, const, volatile, memory layout (20 questions)


**Q1.**

```c
void f(void) { static int c = 0; printf("%d ", ++c); }
f(); f(); f();
```

- a) `1 1 1`
- b) `1 2 3`
- c) `0 1 2`
- d) Compile error


**Q2.**

```c
void f(void) { int x; printf("%d", x); }
```

- a) `0`
- b) `Garbage (indeterminate)`
- c) Compile error
- d) `-1`


**Q3.**

```c
int g;  // at file scope
int main(void) { printf("%d", g); }
```

- a) Garbage
- b) `0`
- c) Compile error
- d) UB


**Q4.**

```c
int g = 5;  // global
Which section is g in?
```

- a) .bss
- b) .text
- c) .data
- d) Stack


**Q5.**

```c
static int s;      // uninitialised
static int z = 0;  // zero
Where do they go?
```

- a) Both .bss
- b) s .bss, z .data
- c) Both .data
- d) Both stack


**Q6.**

```c
char *p = "hello";
p[0] = 'H';
```

- a) Prints Hello
- b) Compile error
- c) `UB (usually segfault)`
- d) p becomes NULL


**Q7.**

```c
char a[] = "hello";
a[0] = 'H';
printf("%s", a);
```

- a) Hello
- b) UB
- c) hello
- d) Compile error


**Q8.**

```c
int *f(void) { int x = 10; return &x; }
Using *f() in main is…
```

- a) Always 10
- b) Compile error
- c) UB: dangling pointer
- d) `0`


**Q9.**

```c
static int counter;  // file scope in a.c
Can b.c use it with extern int counter;?
```

- a) Yes
- b) `No: linker error (internal linkage)`
- c) Yes, but read-only
- d) Compile error in a.c


**Q10.**

```c
What does extern int x; do?
```

- a) Allocates x and sets it to 0
- b) `Declares x; memory is defined in another file`
- c) Makes x read-only
- d) Puts x in a register


**Q11.**

```c
register int r = 5;
int *p = &r;
```

- a) Works fine
- b) UB
- c) Compile error
- d) `p = NULL`


**Q12.**

```c
int f(void);
static int x = f();  // file scope, C
```

- a) `x = return value of f`
- b) Compile error
- c) `x = 0`
- d) UB


**Q13.**

```c
int a = 1, b = 2;
const int *p = &a;
Which line is a compile ERROR?
```

- a) `p = &b;`
- b) `*p = 5;`
- c) `a = 5;`
- d) `printf("%d", *p);`


**Q14.**

```c
int a = 1, b = 2;
int *const p = &a;
Which line is a compile ERROR?
```

- a) `*p = 5;`
- b) `p = &b;`
- c) `a = 5;`
- d) None


**Q15.**

```c
const int x = 10;
int *p = (int *)&x;
*p = 20;
```

- a) x becomes 20
- b) Compile error
- c) UB
- d) x stays 10, defined


**Q16.**

```c
int flag = 0;  // set to 1 by an ISR
while (flag == 0) { }
Compiled with -O2, what can happen?
```

- a) Works correctly always
- b) Infinite loop: the compiler keeps flag in a register
- c) Compile error
- d) Crash


**Q17.**

```c
volatile int counter;
counter++;  // in main AND in an ISR
Is this safe?
```

- a) Yes, volatile makes it atomic
- b) `No: ++ is read-modify-write; an ISR can interrupt in between`
- c) Yes, on ARM only
- d) Compile error


**Q18.**

```c
const volatile uint32_t *status = (const volatile uint32_t *)0x40000004;
What does it mean?
```

- a) The value never changes
- b) `Code can't write it; hardware can change it, so always re-read`
- c) Compile error
- d) Stored in .rodata


**Q19.**

```c
static void helper(void) { }  // in a.c
What does static do here?
```

- a) Function keeps local values
- b) Function is visible only inside a.c
- c) Function is inlined
- d) Function runs once


**Q20.**

```c
Why doesn't a large uninitialised global array (int buf[100000];) make the ELF file much bigger?
```

- a) The compiler compresses it
- b) `It is in .bss: only its size is stored; startup code zeroes it`
- c) It is on the heap
- d) It is in .text


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
