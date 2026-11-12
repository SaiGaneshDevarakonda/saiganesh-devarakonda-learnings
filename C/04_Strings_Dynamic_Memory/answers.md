# Answers: Strings and Dynamic Memory

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz: Strings and dynamic memory (20 questions)

1. **b)** `6 5`: sizeof counts the '\0'; strlen stops before it.
2. **b)** `8 5`: sizeof(pointer) = 8.
3. **c)** `10 2`: sizeof is the declared size; the rest is filled with '\0'.
4. **c)** UB: No '\0' → strlen reads past the array.
5. **b)** Diff: == compares addresses. Use strcmp.
6. **c)** A negative value: 'c' < 'd' → negative. Only the sign is guaranteed.
7. **b)** ello: +1 points to 'e'.
8. **b)** Y: strlen returns size_t (unsigned): 2 - 3 wraps to a huge value.
9. **c)** UB: buffer overflow: "hello" needs 6 bytes.
10. **c)** UB: strncpy does not add '\0' here → printf reads past the array.
11. **b)** `calloc zero-fills; malloc leaves garbage`: Both use the heap; calloc zeroes.
12. **c)** UB: double free: Set p = NULL after free.
13. **b)** UB: use after free: free does not change p; it still holds the old address.
14. **c)** Safe: does nothing: free(NULL) is a no-op.
15. **b)** `Returns NULL → p = NULL → old block leaked`: Use tmp = realloc(p, n); if (tmp) p = tmp;
16. **b)** Memory leak: 100 KB never freed: Heap memory is never freed automatically.
17. **b)** `8`: sizeof gives the pointer size, not the block size.
18. **b)** `Off by one: no room for '\0' → overflow`: Need strlen(src) + 1 bytes.
19. **b)** `Still NULL (and the block leaked)`: The pointer is passed by value. Use int **p and alloc(&q).
20. **b)** `0 48`: '\0' = 0, '0' = 48. Digit to int: c - '0'.


<a id="practice-solutions"></a>
## Practice solutions

### 1. strlen, strcpy, strcmp
```c
#include <stdio.h>
#include <stddef.h>
size_t my_strlen(const char *s) { const char *p = s; while (*p) p++; return (size_t)(p - s); }
char *my_strcpy(char *d, const char *s) { char *r = d; while ((*d++ = *s++)) { } return r; }
int my_strcmp(const char *a, const char *b) {
    while (*a && *a == *b) { a++; b++; }
    return (unsigned char)*a - (unsigned char)*b;
}
int main(void) {
    char buf[8];
    my_strcpy(buf, "abc");
    printf("%zu %s %d\n", my_strlen(buf), buf, my_strcmp("abc", "abd") < 0);   /* 3 abc 1 */
    return 0;
}
```

### 2. Reverse in place
```c
#include <stdio.h>
#include <string.h>
void reverse(char *s) {
    char *l = s, *r = s + strlen(s) - 1;
    while (l < r) { char t = *l; *l++ = *r; *r-- = t; }
}
int main(void) { char s[] = "hello"; reverse(s); printf("%s\n", s); return 0; }   /* olleh */
```

### 3. Palindrome, ignoring case
```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>
int is_pal(const char *s) {
    int l = 0, r = (int)strlen(s) - 1;
    while (l < r)
        if (tolower((unsigned char)s[l++]) != tolower((unsigned char)s[r--])) return 0;
    return 1;
}
int main(void) { printf("%s\n", is_pal("Madam") ? "yes" : "no"); return 0; }
```

### 4. Reverse the words
```c
#include <stdio.h>
#include <string.h>
static void rev(char *l, char *r) { while (l < r) { char t = *l; *l++ = *r; *r-- = t; } }
void reverse_words(char *s) {
    size_t n = strlen(s);
    if (n == 0) return;
    rev(s, s + n - 1);                        /* "C evol I" */
    char *w = s;
    for (char *p = s; ; p++) {
        if (*p == ' ' || *p == '\0') { rev(w, p - 1); if (*p == '\0') break; w = p + 1; }
    }
}
int main(void) { char s[] = "I love C"; reverse_words(s); printf("%s\n", s); return 0; }  /* C love I */
```

### 5. Count character types
```c
#include <stdio.h>
#include <ctype.h>
int main(void) {
    const char *s = "Hi 2 you";
    int v = 0, c = 0, d = 0, sp = 0;
    for (; *s; s++) {
        unsigned char ch = (unsigned char)*s;
        if (isalpha(ch)) { char l = (char)tolower(ch); (l=='a'||l=='e'||l=='i'||l=='o'||l=='u') ? v++ : c++; }
        else if (isdigit(ch)) d++;
        else if (ch == ' ') sp++;
    }
    printf("%d %d %d %d\n", v, c, d, sp);   /* 3 3 1 2 */
    return 0;
}
```

### 6. atoi
```c
#include <stdio.h>
#include <limits.h>
int my_atoi(const char *s) {
    while (*s == ' ') s++;
    int sign = 1;
    if (*s == '-' || *s == '+') { if (*s == '-') sign = -1; s++; }
    long r = 0;
    while (*s >= '0' && *s <= '9') {
        r = r * 10 + (*s++ - '0');
        if (r * sign > INT_MAX) return INT_MAX;     /* clamp on overflow */
        if (r * sign < INT_MIN) return INT_MIN;
    }
    return (int)(r * sign);
}
int main(void) { printf("%d %d\n", my_atoi("  -123"), my_atoi("42abc")); return 0; }   /* -123 42 */
```

### 7. itoa
```c
#include <stdio.h>
void my_itoa(int n, char *buf) {
    char tmp[12]; int i = 0;
    unsigned int u = (n < 0) ? 0u - (unsigned)n : (unsigned)n;   /* safe for INT_MIN */
    do { tmp[i++] = (char)('0' + u % 10); u /= 10; } while (u);
    if (n < 0) *buf++ = '-';
    while (i) *buf++ = tmp[--i];
    *buf = '\0';
}
int main(void) { char b[12]; my_itoa(-450, b); printf("%s\n", b); my_itoa(0, b); printf("%s\n", b); return 0; }
```

### 8. First non-repeating character
```c
#include <stdio.h>
char first_unique(const char *s) {
    int cnt[256] = {0};
    for (const char *p = s; *p; p++) cnt[(unsigned char)*p]++;
    for (const char *p = s; *p; p++) if (cnt[(unsigned char)*p] == 1) return *p;
    return '\0';
}
int main(void) { printf("%c\n", first_unique("swiss")); return 0; }   /* w */
```

### 9. Anagram check
```c
#include <stdio.h>
int is_anagram(const char *a, const char *b) {
    int cnt[256] = {0};
    for (; *a; a++) cnt[(unsigned char)*a]++;
    for (; *b; b++) cnt[(unsigned char)*b]--;
    for (int i = 0; i < 256; i++) if (cnt[i]) return 0;
    return 1;
}
int main(void) { printf("%s\n", is_anagram("listen", "silent") ? "yes" : "no"); return 0; }
```

### 10. Compress a string
```c
#include <stdio.h>
void compress(const char *s, char *out) {
    while (*s) {
        char c = *s; int n = 0;
        while (*s == c) { s++; n++; }
        out += sprintf(out, "%c%d", c, n);
    }
    *out = '\0';
}
int main(void) { char o[64]; compress("aaabbc", o); printf("%s\n", o); return 0; }   /* a3b2c1 */
```

### 11. malloc an array
```c
#include <stdio.h>
#include <stdlib.h>
int main(void) {
    int n = 4;
    int *a = malloc(n * sizeof *a);
    if (a == NULL) return 1;
    for (int i = 0; i < n; i++) a[i] = i + 1;
    for (int i = 0; i < n; i++) printf("%d ", a[i]);
    printf("\n");
    free(a);
    a = NULL;
    return 0;
}
```

### 12. Growing dynamic array
```c
#include <stdio.h>
#include <stdlib.h>
typedef struct { int *data; size_t len, cap; } vec;
int vec_push(vec *v, int x) {
    if (v->len == v->cap) {
        size_t nc = v->cap ? v->cap * 2 : 2;
        int *tmp = realloc(v->data, nc * sizeof *tmp);   /* tmp: don't lose the old block */
        if (tmp == NULL) return -1;
        v->data = tmp; v->cap = nc;
        printf("cap -> %zu\n", nc);
    }
    v->data[v->len++] = x;
    return 0;
}
int main(void) {
    vec v = {0};
    for (int i = 1; i <= 10; i++) vec_push(&v, i);   /* cap 2 4 8 16 */
    free(v.data);
    return 0;
}
```

### 13. strdup
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
char *my_strdup(const char *s) {
    size_t n = strlen(s) + 1;          /* + 1 for '\0' */
    char *d = malloc(n);
    if (d) memcpy(d, s, n);
    return d;
}
int main(void) { char *p = my_strdup("sai"); printf("%s\n", p); free(p); return 0; }
```

### 14. Why `strncpy` is dangerous
If the source has `n` or more characters, it copies exactly `n` bytes and **doesn't** add `'\0'`. The result isn't a valid string, so the next `strlen`/`printf` reads past the buffer. Use `snprintf(dst, size, "%s", src)`, or terminate it manually with `dst[size - 1] = '\0'`.

### 15. `alloc(int *p)` vs `alloc(int **p)`
C passes the pointer **by value**, so `p = malloc(...)` changes only the local copy. The caller's pointer stays NULL, and the block leaks. Passing `&q` (an `int **`) lets the function write through it: `*p = malloc(...)` sets the caller's `q`.
