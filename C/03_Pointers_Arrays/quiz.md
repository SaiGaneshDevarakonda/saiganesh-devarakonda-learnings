# Quiz: Pointers and Arrays

[← Back to notes](README.md)

> 64-bit Linux: int = 4 bytes, pointer = 8 bytes.

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz: Pointers and arrays (20 questions)


**Q1.**

```c
int a[5] = {1,2,3,4,5};
int *p = a;
printf("%d", *(p + 2));
```

- a) `2`
- b) `3`
- c) Address
- d) `4`


**Q2.**

```c
int a[] = {10, 20, 30};
int *p = a;
printf("%d ", *p++);
printf("%d", *p);
```

- a) `11 11`
- b) `10 10`
- c) `10 20`
- d) `20 20`


**Q3.**

```c
int a[] = {10, 20, 30};
int *p = a;
printf("%d ", ++*p);
printf("%d", a[0]);
```

- a) `11 11`
- b) `20 10`
- c) `11 10`
- d) `20 20`


**Q4.**

```c
int a[] = {1, 2, 3, 4};
printf("%d", 2[a]);
```

- a) Compile error
- b) `2`
- c) `3`
- d) UB


**Q5.**

```c
int a[10];
printf("%zu %zu", sizeof(a), sizeof(a) / sizeof(a[0]));
```

- a) `8 2`
- b) `10 10`
- c) `40 10`
- d) `40 4`


**Q6.**

```c
void f(int a[10]) { printf("%zu", sizeof(a)); }
int arr[10]; f(arr);
```

- a) `40`
- b) `8`
- c) `10`
- d) `4`


**Q7.**

```c
int a[5];  // a starts at 0x1000
What is &a + 1 ?
```

- a) `0x1004`
- b) `0x1001`
- c) `0x1014`
- d) `0x1005`


**Q8.**

```c
int *p = (int *)0x1000;
p + 3 = ?
```

- a) `0x1003`
- b) `0x100C`
- c) `0x1018`
- d) `0x1012`


**Q9.**

```c
char *c = (char *)0x1000;
c + 3 = ?
```

- a) `0x1003`
- b) `0x100C`
- c) `0x1018`
- d) `0x1030`


**Q10.**

```c
int m[2][3] = {{1,2,3},{4,5,6}};
printf("%d", *(*(m + 1) + 2));
```

- a) `3`
- b) `5`
- c) `6`
- d) `4`


**Q11.**

```c
int m[2][3];  // m starts at 0x2000
m + 1 = ?
```

- a) `0x2004`
- b) `0x200C`
- c) `0x2018`
- d) `0x2003`


**Q12.**

```c
int *p[3];  is…
```

- a) Pointer to an array of 3 ints
- b) Array of 3 pointers to int
- c) Pointer to a function
- d) Compile error


**Q13.**

```c
int (*p)[3];  is…
```

- a) Array of 3 pointers to int
- b) Pointer to an array of 3 ints
- c) 3 ints
- d) `Function returning int*`


**Q14.**

```c
int (*f)(int, int);  is…
```

- a) `Function returning int*`
- b) `Pointer to a function taking (int,int) returning int`
- c) Array of functions
- d) Compile error


**Q15.**

```c
int a = 5;
int *p = &a;
int **pp = &p;
**pp = 10;
printf("%d", a);
```

- a) `5`
- b) `10`
- c) Address of p
- d) UB


**Q16.**

```c
int *p;
*p = 5;
```

- a) Stores 5 safely
- b) Compile error
- c) UB: wild pointer
- d) p becomes NULL


**Q17.**

```c
int a = 7;
void *v = &a;
printf("%d", *v);
```

- a) `7`
- b) UB
- c) Compile error
- d) Address


**Q18.**

```c
int a[5];
a++;
```

- a) `a points to a[1]`
- b) Compile error
- c) UB
- d) `a[0] becomes 1`


**Q19.**

```c
int a[10];
int *p = &a[2], *q = &a[7];
printf("%td", q - p);
```

- a) `20`
- b) `5`
- c) `-5`
- d) Compile error


**Q20.**

```c
void swap(int a, int b) { int t = a; a = b; b = t; }
int x = 1, y = 2;
swap(x, y);
printf("%d %d", x, y);
```

- a) `2 1`
- b) `1 2`
- c) UB
- d) `1 1`


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
