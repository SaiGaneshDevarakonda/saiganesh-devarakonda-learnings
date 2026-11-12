# Answers: Pointers and Arrays

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz: Pointers and arrays (20 questions)

1. **b)** `3`: *(p+2) = a[2] = 3.
2. **c)** `10 20`: *p++ = *(p++): use 10, then p moves to a[1].
3. **a)** `11 11`: ++*p increments the value a[0].
4. **c)** `3`: 2[a] = *(2 + a) = a[2] = 3.
5. **c)** `40 10`: Whole array = 40 bytes; divided by element size = 10 elements.
6. **b)** `8`: An array parameter decays to int* → 8. Pass the length separately.
7. **c)** `0x1014`: &a points to the whole 20-byte array → +0x14.
8. **b)** `0x100C`: 3 × 4 bytes = 12 = 0xC.
9. **a)** `0x1003`: char is 1 byte → +3.
10. **c)** `6`: m[1][2] = 6.
11. **b)** `0x200C`: m + 1 skips one row = 12 bytes.
12. **b)** Array of 3 pointers to int: [] binds tighter than * → array of 3 int*.
13. **b)** Pointer to an array of 3 ints: Brackets force * first → pointer to int[3].
14. **b)** `Pointer to a function taking (int,int) returning int`: Used for callbacks and vector/jump tables.
15. **b)** `10`: **pp → *p → a.
16. **c)** UB: wild pointer: An uninitialised pointer holds a garbage address.
17. **c)** Compile error: void* cannot be dereferenced. Use *(int *)v.
18. **b)** Compile error: An array name is not a modifiable lvalue.
19. **b)** `5`: Pointer subtraction gives elements, not bytes.
20. **b)** `1 2`: Pass by value → only copies are swapped. Use int * parameters.


<a id="practice-solutions"></a>
## Practice solutions

### 1. Swap
```c
#include <stdio.h>
void swap(int *a, int *b) { int t = *a; *a = *b; *b = t; }
int main(void) { int x = 3, y = 5; swap(&x, &y); printf("%d %d\n", x, y); return 0; }
```

### 2. Reverse with two pointers
```c
#include <stdio.h>
void reverse(int *a, int n) {
    int *l = a, *r = a + n - 1;
    while (l < r) { int t = *l; *l++ = *r; *r-- = t; }
}
int main(void) {
    int a[] = {1, 2, 3, 4, 5};
    reverse(a, 5);
    for (int *p = a; p < a + 5; p++) printf("%d ", *p);   /* 5 4 3 2 1 */
    printf("\n");
    return 0;
}
```

### 3. memcpy and memmove
```c
#include <stdio.h>
#include <stddef.h>
#include <stdint.h>
void *my_memcpy(void *dst, const void *src, size_t n) {
    uint8_t *d = dst; const uint8_t *s = src;
    while (n--) *d++ = *s++;
    return dst;
}
/* Overlap: if dst is inside src, a forward copy overwrites source bytes before they are read.
   memmove copies backwards when dst > src. */
void *my_memmove(void *dst, const void *src, size_t n) {
    uint8_t *d = dst; const uint8_t *s = src;
    if (d < s) while (n--) *d++ = *s++;
    else { d += n; s += n; while (n--) *--d = *--s; }
    return dst;
}
int main(void) {
    char a[] = "abcdef";
    my_memmove(a + 2, a, 4);          /* "ababcd" */
    printf("%s\n", a);
    return 0;
}
```

### 4. Max and second max
```c
#include <stdio.h>
#include <limits.h>
void top2(const int *a, int n, int *max, int *second) {
    *max = *second = INT_MIN;
    for (const int *p = a; p < a + n; p++) {
        if (*p > *max)                   { *second = *max; *max = *p; }
        else if (*p > *second && *p != *max) *second = *p;
    }
}
int main(void) {
    int a[] = {4, 9, 2, 9, 7}, m, s;
    top2(a, 5, &m, &s);
    printf("%d %d\n", m, s);    /* 9 7 */
    return 0;
}
```

### 5. Matrix with pointer syntax
```c
#include <stdio.h>
int main(void) {
    int m[2][3] = {{1, 2, 3}, {4, 5, 6}};
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 3; j++) printf("%d ", *(*(m + i) + j));
        printf("\n");
    }
    return 0;
}
```

### 6. Rotate right by k (reversal trick)
```c
#include <stdio.h>
static void rev(int *a, int l, int r) { while (l < r) { int t = a[l]; a[l++] = a[r]; a[r--] = t; } }
void rotate_right(int *a, int n, int k) {
    k %= n;
    rev(a, 0, n - 1);      /* 5 4 3 2 1 */
    rev(a, 0, k - 1);      /* 4 5 3 2 1 */
    rev(a, k, n - 1);      /* 4 5 1 2 3 */
}
int main(void) {
    int a[] = {1, 2, 3, 4, 5};
    rotate_right(a, 5, 2);
    for (int i = 0; i < 5; i++) printf("%d ", a[i]);
    printf("\n");
    return 0;
}
```

### 7. Remove duplicates (sorted)
```c
#include <stdio.h>
int dedup(int *a, int n) {
    if (n == 0) return 0;
    int w = 1;                               /* write index */
    for (int r = 1; r < n; r++)
        if (a[r] != a[w - 1]) a[w++] = a[r];
    return w;
}
int main(void) {
    int a[] = {1, 1, 2, 3, 3};
    int n = dedup(a, 5);
    printf("%d:", n); for (int i = 0; i < n; i++) printf(" %d", a[i]); printf("\n");   /* 3: 1 2 3 */
    return 0;
}
```

### 8. Move zeros to the end
```c
#include <stdio.h>
void move_zeros(int *a, int n) {
    int w = 0;
    for (int r = 0; r < n; r++) if (a[r] != 0) a[w++] = a[r];
    while (w < n) a[w++] = 0;
}
int main(void) {
    int a[] = {0, 1, 0, 3, 12};
    move_zeros(a, 5);
    for (int i = 0; i < 5; i++) printf("%d ", a[i]);   /* 1 3 12 0 0 */
    printf("\n");
    return 0;
}
```

### 9. Spiral order
```c
#include <stdio.h>
#define R 3
#define C 3
void spiral(int m[R][C]) {
    int top = 0, bot = R - 1, left = 0, right = C - 1;
    while (top <= bot && left <= right) {
        for (int j = left; j <= right; j++) printf("%d ", m[top][j]);
        top++;
        for (int i = top; i <= bot; i++) printf("%d ", m[i][right]);
        right--;
        if (top <= bot)   { for (int j = right; j >= left; j--) printf("%d ", m[bot][j]); bot--; }
        if (left <= right){ for (int i = bot; i >= top; i--)    printf("%d ", m[i][left]); left++; }
    }
    printf("\n");
}
int main(void) { int m[R][C] = {{1,2,3},{4,5,6},{7,8,9}}; spiral(m); return 0; }   /* 1 2 3 6 9 8 7 4 5 */
```

### 10. `a` vs `&a`
Both hold the same address, but the types differ:
- `a` decays to `int *`, so `a + 1` moves 4 bytes.
- `&a` is `int (*)[5]`, a pointer to the whole array, so `&a + 1` moves 20 bytes.

### 11. `int *p[3]` vs `int (*p)[3]`
- `int *p[3]` is an **array of 3 pointers** (24 bytes on 64-bit).
- `int (*p)[3]` is **one pointer to an array of 3 ints** (8 bytes), and `p + 1` moves 12 bytes.
