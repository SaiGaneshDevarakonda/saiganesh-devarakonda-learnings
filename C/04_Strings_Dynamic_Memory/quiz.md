# Quiz: Strings and Dynamic Memory

[← Back to notes](README.md)

> 64-bit Linux: pointer = 8 bytes.

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz: Strings and dynamic memory (20 questions)


**Q1.**

```c
char s[] = "hello";
printf("%zu %zu", sizeof(s), strlen(s));
```

- a) `5 5`
- b) `6 5`
- c) `8 5`
- d) `6 6`


**Q2.**

```c
char *p = "hello";
printf("%zu %zu", sizeof(p), strlen(p));
```

- a) `6 5`
- b) `8 5`
- c) `8 6`
- d) `5 5`


**Q3.**

```c
char s[10] = "hi";
printf("%zu %zu", sizeof(s), strlen(s));
```

- a) `3 2`
- b) `10 10`
- c) `10 2`
- d) `2 2`


**Q4.**

```c
char s[] = {'a', 'b', 'c'};
printf("%zu", strlen(s));
```

- a) `3`
- b) `4`
- c) UB
- d) Compile error


**Q5.**

```c
char a[] = "abc", b[] = "abc";
if (a == b) printf("Same"); else printf("Diff");
```

- a) Same
- b) Diff
- c) Compile error
- d) UB


**Q6.**

strcmp("abc", "abd") returns…

- a) `0`
- b) A positive value
- c) A negative value
- d) 1 exactly


**Q7.**

```c
printf("%s", "hello" + 1);
```

- a) hello1
- b) ello
- c) e
- d) Compile error


**Q8.**

```c
if (strlen("ab") - strlen("abc") > 0) printf("Y"); else printf("N");
```

- a) N
- b) Y
- c) UB
- d) Compile error


**Q9.**

```c
char s[5];
strcpy(s, "hello");
```

- a) `Fine, s = hello`
- b) Compile error
- c) UB: buffer overflow
- d) `s = hell`


**Q10.**

```c
char s[3];
strncpy(s, "hello", 3);
printf("%s", s);
```

- a) hel
- b) he
- c) UB
- d) hello


**Q11.**

Main difference between malloc(10 \* sizeof(int)) and calloc(10, sizeof(int))?

- a) calloc is on the stack
- b) `calloc zero-fills; malloc leaves garbage`
- c) malloc is faster and zero-fills
- d) No difference


**Q12.**

```c
int *p = malloc(sizeof(int));
free(p);
free(p);
```

- a) Safe
- b) Compile error
- c) UB: double free
- d) Memory leak


**Q13.**

```c
int *p = malloc(sizeof(int));
free(p);
*p = 5;
```

- a) Stores 5 fine
- b) UB: use after free
- c) Compile error
- d) Crash is guaranteed


**Q14.**

```c
int *p = NULL;
free(p);
```

- a) Crash
- b) UB
- c) Safe: does nothing
- d) Compile error


**Q15.**

```c
p = realloc(p, 1000000000);
If realloc fails, what happens?
```

- a) p stays valid
- b) `Returns NULL → p = NULL → old block leaked`
- c) Program aborts
- d) Old block is freed


**Q16.**

```c
void f(void) { int *p = malloc(100); }
Called 1000 times. Problem?
```

- a) Stack overflow
- b) Memory leak: 100 KB never freed
- c) Double free
- d) None


**Q17.**

```c
int *p = malloc(100);
printf("%zu", sizeof(p));
```

- a) `100`
- b) `8`
- c) `25`
- d) `4`


**Q18.**

```c
char *s = malloc(strlen(src));
strcpy(s, src);
What is wrong?
```

- a) Nothing
- b) `Off by one: no room for '\0' → overflow`
- c) Should be calloc
- d) strcpy arguments reversed


**Q19.**

```c
void alloc(int *p) { p = malloc(sizeof(int)); }
int *q = NULL;
alloc(q);
After the call, q is…
```

- a) A valid heap address
- b) `Still NULL (and the block leaked)`
- c) UB
- d) Compile error


**Q20.**

```c
printf("%d %d", '\0', '0');
```

- a) `0 0`
- b) `0 48`
- c) `48 0`
- d) Compile error


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
