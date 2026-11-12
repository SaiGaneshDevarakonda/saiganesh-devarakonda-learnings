# 03 · Pointers and Arrays

**Quiz:** [quiz.md](quiz.md) · **Answers and solutions:** [answers.md](answers.md)

> All sizes assume 64-bit Linux: `int` = 4 bytes, pointer = 8 bytes.

---

## Theory

### 1. A pointer is a box that holds an address
```
 int a = 10;   int *p = &a;   int **pp = &p;

   pp               p                a
 ┌────────┐      ┌────────┐      ┌────────┐
 │ 0x2000 │ ───► │ 0x1000 │ ───► │   10   │
 └────────┘      └────────┘      └────────┘
  @0x3000         @0x2000         @0x1000
 *pp = p (0x1000)       **pp = *p = a = 10
```

### 2. Pointer arithmetic moves by the size of the pointed-to type
```
 int a[4];  base = 0x1000
 ┌──────┬──────┬──────┬──────┐
 │ a[0] │ a[1] │ a[2] │ a[3] │
 └──────┴──────┴──────┴──────┘
 0x1000 0x1004 0x1008 0x100C
   p      p+1    p+2    p+3      int*   → +4 bytes per step
                                 char*  → +1     short* → +2
 q - p = number of ELEMENTS between them (not bytes)
```

### 3. Arrays and pointers
```
 a[i]  ==  *(a + i)  ==  *(i + a)  ==  i[a]       (so 2[a] is legal!)

 a     → address of a[0]      type int*          a + 1  → +4 bytes
 &a    → address of WHOLE     type int(*)[4]     &a + 1 → +16 bytes
         (same address, different TYPE)

 sizeof(a) in its own scope      → 16 (the whole array)
 passed to f(int a[]) or f(int *a) → decays to int* → sizeof = 8
 a++                              → compile error (an array name is not a variable)
```
Count elements with `sizeof(a) / sizeof(a[0])`. This **only** works where the array is declared, not inside a function that receives it.

### 4. `*p++` vs `++*p` vs `(*p)++`
| Expression | Meaning | `a = {10,20,30}`, `p = a` |
|---|---|---|
| `*p++` | use `*p`, then move `p` (++ binds to p) | gives 10, p → a[1] |
| `*++p` | move `p` first, then read | gives 20 |
| `++*p` | increment the **value** | a[0] = 11 |
| `(*p)++` | use the value, then increment it | gives 10, a[0] = 11 |

### 5. 2D arrays
```
 int m[2][3];                  row 0          row 1
                         ┌────┬────┬────┬────┬────┬────┐
                         │ 00 │ 01 │ 02 │ 10 │ 11 │ 12 │   (stored row by row)
                         └────┴────┴────┴────┴────┴────┘
 m + 1     → jumps one ROW = 3 * 4 = 12 bytes
 m[i][j]   == *(*(m + i) + j)
 sizeof(m[0]) = 12   (one row)
```

### 6. Reading declarations: start at the name, go right, then left
```
 int *p[3];          p is an ARRAY of 3 → pointers to int           sizeof = 24
 int (*p)[3];        p is a POINTER     → to an array of 3 ints     p+1 jumps 12
 int (*f)(int);      f is a POINTER     → to a function(int) returning int
 int *f(int);        f is a FUNCTION    → returning int*
```

### 7. Pass by value vs by pointer
C always passes **copies**. To change the caller's variable, pass its address:
```c
void swap(int *a, int *b) { int t = *a; *a = *b; *b = t; }   /* call: swap(&x, &y); */
```

### 8. Bad pointers (all UB)
| Name | Example |
|---|---|
| Wild | `int *p; *p = 5;` (never initialised) |
| NULL dereference | `int *p = NULL; *p;` |
| Dangling | pointer to a freed block, or a returned local |
| `void *` dereference | `*v` is a **compile error**. Cast it first: `*(int *)v` |

---

## Practice Questions
Use pointers only, with no `[]` where the problem says so. Solutions are in [answers.md](answers.md#practice-solutions).

| # | Problem | Input → Output |
|---|---|---|
| 1 | `swap(int *a, int *b)` | `3, 5` → `5, 3` |
| 2 | Reverse an array in place with two pointers (no `[]`) | `{1,2,3,4,5}` → `{5,4,3,2,1}` |
| 3 | `my_memcpy(void *dst, const void *src, size_t n)` byte by byte. What breaks if the regions overlap? Write `my_memmove`. | — |
| 4 | Max and second max in one pass, returned through pointers | `{4,9,2,9,7}` → `9, 7` |
| 5 | Print a 2D matrix using only `*(*(m + i) + j)` | 2×3 matrix |
| 6 | Rotate an array right by k | `{1,2,3,4,5}, k=2` → `{4,5,1,2,3}` |
| 7 | Remove duplicates from a sorted array in place, return the new length | `{1,1,2,3,3}` → `3, {1,2,3}` |
| 8 | Move all zeros to the end, keeping the order | `{0,1,0,3,12}` → `{1,3,12,0,0}` |
| 9 | Print a matrix in spiral order | `{{1,2,3},{4,5,6},{7,8,9}}` → `1 2 3 6 9 8 7 4 5` |

**Explain out loud:**
10. What is the difference between `a` and `&a` for `int a[5]`?
11. What is the difference between `int *p[3]` and `int (*p)[3]`?

---

## Interview One-liner
*"Pointer + 1 moves by sizeof(type). An array name decays to a pointer to its first element, except with sizeof and &."*
