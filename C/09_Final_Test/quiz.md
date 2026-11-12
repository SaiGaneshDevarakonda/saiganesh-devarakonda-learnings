# Quiz: C Final Test

[← Back to notes](README.md)

> 64-bit Linux GCC, little endian, int 4 bytes, pointer 8 bytes. Pass mark: 40/50.

Answer every question before you check the answer sheet. Pass mark: **80%**.


## C final test: 50 mixed questions


**Q1.**

```c
int i = -3; unsigned u = 2;
printf("%s", i < u ? "lt" : "ge");
```

- a) lt
- b) ge
- c) UB
- d) Compile error


**Q2.**

```c
if (sizeof(int) > -2) printf("Y"); else printf("N");
```

- a) Y
- b) UB
- c) N
- d) Compile error


**Q3.**

```c
signed char c = 250;
printf("%d", c);
```

- a) `250`
- b) `-6`
- c) `-5`
- d) `6`


**Q4.**

```c
unsigned short s = 65537;
printf("%u", s);
```

- a) `65537`
- b) `65535`
- c) `1`
- d) `0`


**Q5.**

```c
int x = 1;
int y = x++ + x;
```

- a) `y = 2`
- b) `y = 3`
- c) UB
- d) Compile error


**Q6.**

```c
int x = 1;
int y = x++ && x;
printf("%d %d", x, y);
```

- a) `2 1`
- b) UB
- c) `1 1`
- d) `2 0`


**Q7.**

```c
printf("%d", 2.0);
```

- a) `2`
- b) `0`
- c) Compile error
- d) UB


**Q8.**

```c
printf("%d", 015 + 0x5);
```

- a) `20`
- b) `18`
- c) `15`
- d) `26`


**Q9.**

```c
int x = 6;
x %= 4 + 1;
printf("%d", x);
```

- a) `3`
- b) `2`
- c) `1`
- d) `0`


**Q10.**

```c
if (0.5f + 0.25f == 0.75) printf("Yes"); else printf("No");
```

- a) Yes
- b) No
- c) UB
- d) Compile error


**Q11.**

```c
void f(void) { static int c = 0; printf("%d ", c++); }
f(); f(); f(); f();
```

- a) `1 2 3 4`
- b) `0 0 0 0`
- c) `0 1 2 3`
- d) `1 1 1 1`


**Q12.**

```c
int buf[1000];  // global, not initialised
Which section?
```

- a) .data
- b) .bss
- c) Heap
- d) .rodata


**Q13.**

```c
char *s = "abc";
s[1] = 'X';
```

- a) `s = aXc`
- b) UB
- c) Compile error
- d) Defined, unchanged


**Q14.**

```c
int a = 1, b = 2;
const int *p = &a;
p = &b;
```

- a) Compile error
- b) UB
- c) Allowed
- d) Warning


**Q15.**

```c
int a[3] = {1,2,3};
int *const p = a;
Which line is a compile error?
```

- a) `*p = 9;`
- b) `p[1] = 9;`
- c) `p++;`
- d) `*(p + 2) = 9;`


**Q16.**

```c
while (!(STATUS & 1)) { }
Why must STATUS be read through a volatile pointer?
```

- a) Atomicity
- b) So the compiler re-reads it every loop
- c) To put it in .rodata
- d) It doesn't need to be


**Q17.**

```c
int a[] = {5, 6, 7};
int *p = a;
printf("%d ", *++p);
printf("%d", *p);
```

- a) `6 6`
- b) `5 6`
- c) `6 7`
- d) `5 5`


**Q18.**

```c
int a[] = {5, 6, 7};
int *p = a;
printf("%d ", (*p)++);
printf("%d", a[0]);
```

- a) `6 6`
- b) `5 5`
- c) `5 6`
- d) `6 7`


**Q19.**

```c
int a[4];  // a at 0x1000
a + 1 and &a + 1 are…
```

- a) `0x1004, 0x1004`
- b) `0x1004, 0x1010`
- c) `0x1001, 0x1004`
- d) `0x1010, 0x1004`


**Q20.**

```c
short *s = (short *)0x100;
s + 5 = ?
```

- a) `0x105`
- b) `0x10A`
- c) `0x114`
- d) `0x110`


**Q21.**

```c
int m[3][4];
printf("%zu", sizeof(m[0]));
```

- a) `4`
- b) `8`
- c) `16`
- d) `48`


**Q22.**

```c
char *arr[] = {"ab", "cde"};
printf("%c", arr[1][2]);
```

- a) d
- b) e
- c) b
- d) UB


**Q23.**

```c
int (*p)[4];
How many bytes does p + 1 move?
```

- a) `4`
- b) `8`
- c) `16`
- d) `1`


**Q24.**

```c
char s[] = "hi\0there";
printf("%zu %zu", sizeof(s), strlen(s));
```

- a) `9 2`
- b) `8 8`
- c) `3 2`
- d) `9 8`


**Q25.**

```c
char s[6] = "hello";
printf("%d", s[5]);
```

- a) `111`
- b) `0`
- c) UB
- d) `48`


**Q26.**

```c
if (strlen("abc") - 5 > 0) printf("Y"); else printf("N");
```

- a) N
- b) Y
- c) UB
- d) Compile error


**Q27.**

```c
int *p = calloc(3, sizeof(int));
printf("%d", p[2]);
```

- a) Garbage
- b) `0`
- c) `3`
- d) UB


**Q28.**

Correct allocation to copy string s?

- a) `malloc(strlen(s))`
- b) `malloc(sizeof(s))  // s is char*`
- c) `malloc(strlen(s) + 1)`
- d) `malloc(strlen(s) - 1)`


**Q29.**

```c
free(p);
p = NULL;
free(p);
```

- a) Double free → UB
- b) Safe
- c) Crash
- d) Leak


**Q30.**

```c
struct { char a; char b; int c; char d; };
sizeof = ?
```

- a) `7`
- b) `8`
- c) `12`
- d) `16`


**Q31.**

```c
struct { short s; char c; };
sizeof = ?
```

- a) `3`
- b) `4`
- c) `6`
- d) `8`


**Q32.**

```c
union { char c[3]; short s; };
sizeof = ?
```

- a) `3`
- b) `5`
- c) `4`
- d) `2`


**Q33.**

```c
struct { double d; int i; };
sizeof = ?
```

- a) `12`
- b) `16`
- c) `24`
- d) `8`


**Q34.**

Little endian stores 0x11223344 at address A. Byte at A?

- a) `0x11`
- b) `0x22`
- c) `0x33`
- d) `0x44`


**Q35.**

```c
struct { unsigned a:2; } s;
s.a = 5;
printf("%u", s.a);
```

- a) `5`
- b) `1`
- c) `3`
- d) `2`


**Q36.**

```c
unsigned x = 0x55;
x ^= 0xFF;
printf("0x%X", x);
```

- a) `0xFF`
- b) `0xAA`
- c) `0x55`
- d) `0x00`


**Q37.**

```c
unsigned x = 0x80;
printf("%u", (x >> 7) & 1);
```

- a) `0`
- b) `1`
- c) `128`
- d) `7`


**Q38.**

```c
unsigned reg = 0x00;
Write 0x5 into bits [6:4]. Result?
```

- a) `0x05`
- b) `0x50`
- c) `0x500`
- d) `0x14`


**Q39.**

Number of set bits in 0x77?

- a) `4`
- b) `8`
- c) `6`
- d) `7`


**Q40.**

```c
unsigned x = 16;
printf("%u", x & (x - 1));
```

- a) `15`
- b) `16`
- c) `0`
- d) `1`


**Q41.**

```c
printf("0x%X", (unsigned char)(0xF0 << 1));
```

- a) `0x1E0`
- b) `0xE0`
- c) `0xF0`
- d) `0x78`


**Q42.**

```c
#define MUL(a,b) a*b
printf("%d", MUL(1+2, 3+4));
```

- a) `21`
- b) `11`
- c) `10`
- d) `15`


**Q43.**

```c
#define INC(x) ((x)+1)
printf("%d", INC(INC(1)));
```

- a) `2`
- b) `3`
- c) Compile error
- d) UB


**Q44.**

```c
#define P char*
P a, b;
printf("%zu", sizeof(b));
```

- a) `8`
- b) `1`
- c) `2`
- d) Compile error


**Q45.**

```c
"error: expected ';' before '}'" comes from which stage?
```

- a) Preprocessor
- b) Compiler
- c) Assembler
- d) Linker


**Q46.**

```c
#define STR(x) #x
printf("%s", STR(1+2));
```

- a) `3`
- b) 1+2
- c) x
- d) Compile error


**Q47.**

```c
int f(int n) { return n ? n % 10 + f(n / 10) : 0; }
printf("%d", f(1234));
```

- a) `1234`
- b) `4321`
- c) `10`
- d) `4`


**Q48.**

```c
void p(int n) { if (n > 0) { p(n - 2); printf("%d ", n); } }
p(5);
```

- a) `5 3 1`
- b) `1 3 5`
- c) `5 4 3 2 1`
- d) `1 2 3 4 5`


**Q49.**

```c
volatile int count;
main: count++;    ISR: count++;
Risk without protection?
```

- a) None
- b) Lost update: ++ is load/add/store
- c) Compile error
- d) Stack overflow


**Q50.**

To write delete_head() that changes the caller's list pointer, the parameter should be…

- a) `struct Node *head`
- b) struct Node head
- c) `struct Node **head`
- d) `const struct Node *head`


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
