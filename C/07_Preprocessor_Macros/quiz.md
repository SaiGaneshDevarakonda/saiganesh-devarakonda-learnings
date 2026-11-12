# Quiz: Preprocessor, Macros, Compilation

[← Back to notes](README.md)

> Expand the macro as TEXT first, then evaluate.

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz: Preprocessor, macros, compilation (20 questions)


**Q1.**

```c
#define SQ(x) x*x
printf("%d", SQ(2+3));
```

- a) `25`
- b) `11`
- c) `10`
- d) `13`


**Q2.**

```c
#define SQ(x) ((x)*(x))
printf("%d", SQ(2+3));
```

- a) `11`
- b) `25`
- c) `10`
- d) UB


**Q3.**

```c
#define DBL(x) (x)+(x)
printf("%d", 10 * DBL(2));
```

- a) `40`
- b) `22`
- c) `24`
- d) `20`


**Q4.**

```c
#define SQ(x) ((x)*(x))
int i = 3;
int r = SQ(i++);
```

- a) `r = 9`
- b) `r = 12`
- c) UB
- d) Compile error


**Q5.**

```c
#define MAX(a,b) ((a) > (b) ? (a) : (b))
int x = 5, y = 3;
int m = MAX(x++, y);
printf("%d %d", m, x);
```

- a) `5 6`
- b) `6 7`
- c) UB
- d) `6 6`


**Q6.**

```c
#define PTR int*
PTR a, b;
Types of a and b?
```

- a) `Both int*`
- b) `a int*, b int`
- c) Both int
- d) Compile error


**Q7.**

```c
typedef int* PTR;
PTR a, b;
Types of a and b?
```

- a) `Both int*`
- b) `a int*, b int`
- c) Both int
- d) Compile error


**Q8.**

```c
#define STR(x) #x
printf("%s", STR(hello));
```

- a) hello
- b) x
- c) Compile error
- d) `#hello`


**Q9.**

```c
#define CAT(a,b) a##b
int xy = 10;
printf("%d", CAT(x, y));
```

- a) Compile error
- b) `10`
- c) xy
- d) `0`


**Q10.**

```c
Why wrap multi-statement macros in do { ... } while (0)?
```

- a) Speed
- b) So the macro acts as ONE statement: safe inside if/else without braces
- c) Required by the compiler
- d) To return values


**Q11.**

```c
#define N 10
int main(void) { int N = 5; }
```

- a) Local N hides the macro
- b) Compile error
- c) UB
- d) Warning only


**Q12.**

Which gcc flag stops after preprocessing?

- a) `-c`
- b) `-S`
- c) `-E`
- d) `-o`


**Q13.**

Which stage produces the .o file?

- a) Preprocessor
- b) Compiler proper
- c) Assembler
- d) Linker


**Q14.**

"undefined reference to `foo'" comes from…

- a) Preprocessor
- b) Compiler
- c) Assembler
- d) Linker


**Q15.**

```c
#ifndef UART_H
#define UART_H
...
#endif
Purpose?
```

- a) Speed
- b) Prevents the header being included twice
- c) Makes it private
- d) Required for linking


**Q16.**

```c
#if 0
  printf("A"); /* comment */
#endif
printf("B");
```

- a) AB
- b) B
- c) Compile error
- d) A


**Q17.**

```c
// this is line 7 of the file:
printf("%d", __LINE__);
```

- a) `0`
- b) `7`
- c) `8`
- d) Compile error


**Q18.**

```c
#define A 5
#define B A+1
printf("%d", B * 2);
```

- a) `12`
- b) `7`
- c) `11`
- d) `10`


**Q19.**

Main advantage of static inline over a function-like macro?

- a) Always faster
- b) Type checking, arguments evaluated once, debuggable
- c) Works without a header
- d) Smaller code always


**Q20.**

```c
#ifdef DEBUG
  printf("dbg");
#endif
Compiled with gcc -DDEBUG main.c
```

- a) Prints dbg
- b) Prints nothing
- c) Compile error
- d) Linker error


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
