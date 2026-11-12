# Quiz: Structs, Unions, Endianness

[← Back to notes](README.md)

> 64-bit Linux GCC, little endian unless stated.

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz: Struct, union, padding, endianness (20 questions)


**Q1.**

```c
struct A { char c; int i; char d; };
sizeof(struct A) = ?
```

- a) `6`
- b) `8`
- c) `12`
- d) `16`


**Q2.**

```c
struct B { int i; char c; char d; };
sizeof(struct B) = ?
```

- a) `6`
- b) `8`
- c) `12`
- d) `10`


**Q3.**

```c
struct C { char c; double d; };
sizeof(struct C) = ?
```

- a) `9`
- b) `12`
- c) `16`
- d) `8`


**Q4.**

```c
struct D { char a; short b; int c; };
sizeof(struct D) = ?
```

- a) `7`
- b) `8`
- c) `12`
- d) `10`


**Q5.**

```c
struct E { double d; char c; int i; };
sizeof(struct E) = ?
```

- a) `13`
- b) `16`
- c) `24`
- d) `20`


**Q6.**

```c
struct __attribute__((packed)) F { char c; int i; };
sizeof(struct F) = ?
```

- a) `8`
- b) `5`
- c) `4`
- d) `6`


**Q7.**

```c
union U { int i; char c[5]; };
sizeof(union U) = ?
```

- a) `5`
- b) `9`
- c) `8`
- d) `4`


**Q8.**

```c
union { int i; char c; } u;
u.i = 0x12345678;
printf("%x", u.c);  // little endian
```

- a) `12`
- b) `78`
- c) `56`
- d) `12345678`


**Q9.**

```c
union { int i; float f; } u;
u.i = 10;
u.f = 2.5f;
printf("%d", u.i);
```

- a) `10`
- b) `2`
- c) `The bits of 2.5f read as an int (1075838976)`
- d) Compile error


**Q10.**

```c
struct { unsigned a:3; } s;
s.a = 9;
printf("%u", s.a);
```

- a) `9`
- b) `1`
- c) `7`
- d) UB


**Q11.**

```c
struct { unsigned a:3; unsigned b:5; } s;
sizeof(s) = ?
```

- a) `1`
- b) `8`
- c) `4`
- d) `2`


**Q12.**

```c
struct { unsigned a:3; } s;
unsigned *p = &s.a;
```

- a) Works
- b) UB
- c) Compile error
- d) `p = NULL`


**Q13.**

```c
enum { A, B = 5, C, D = 2, E };
printf("%d %d", C, E);
```

- a) `2 4`
- b) `6 3`
- c) `6 5`
- d) Compile error


**Q14.**

```c
struct P { int x; } a = {1}, b = {1};
if (a == b) printf("Eq");
```

- a) Eq
- b) Nothing
- c) Compile error
- d) UB


**Q15.**

```c
struct P { int x; } a = {1}, b;
b = a;
b.x = 5;
printf("%d", a.x);
```

- a) `5`
- b) `1`
- c) Compile error
- d) UB


**Q16.**

```c
struct Node { int v; struct Node next; };
```

- a) Valid linked list node
- b) Compile error: incomplete type
- c) UB
- d) Size 8


**Q17.**

```c
struct S { int *p; } a, b;
a.p = malloc(sizeof(int)); *a.p = 1;
b = a;
*b.p = 9;
printf("%d", *a.p);
```

- a) `1`
- b) `9`
- c) UB
- d) Compile error


**Q18.**

```c
A BIG endian CPU stores 0x12345678 at 0x100.
Which byte is at 0x100?
```

- a) `0x78`
- b) `0x12`
- c) `0x34`
- d) `0x56`


**Q19.**

Why does the compiler add padding?

- a) Room for future members
- b) So each member is naturally aligned: faster access, no alignment faults
- c) Security
- d) The standard demands 4-byte structs


**Q20.**

```c
struct T { char c; int i; };
offsetof(struct T, i) = ?
```

- a) `1`
- b) `4`
- c) `8`
- d) `0`


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
