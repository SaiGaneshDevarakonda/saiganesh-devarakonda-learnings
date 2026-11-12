# 04 · Strings and Dynamic Memory

**Quiz:** [quiz.md](quiz.md) · **Answers and solutions:** [answers.md](answers.md)

---

## Theory

### 1. A string is a char array plus `'\0'`
```
 char s[] = "hello";
 ┌───┬───┬───┬───┬───┬────┐
 │ h │ e │ l │ l │ o │ \0 │      sizeof(s) = 6   (counts '\0')
 └───┴───┴───┴───┴───┴────┘      strlen(s) = 5   (stops BEFORE '\0')
 Without '\0' → strlen / printf("%s") keep reading past the array → UB
```
`char s[] = "hi\0there";` gives sizeof = 9 and strlen = 2 (strlen stops at the **first** `'\0'`).

### 2. Array vs pointer to a literal
| | `char s[] = "hi";` | `char *p = "hi";` |
|---|---|---|
| Where it lives | own copy (stack) | p on the stack → literal in `.rodata` |
| `sizeof` | 3 | 8 (pointer) |
| Modify `s[0]` / `p[0]` | ✅ OK | ❌ UB |
| `s = "x"` / `p = "x"` | ❌ compile error | ✅ OK |

### 3. String function traps
```
 strcpy(dst, src)      no bounds check → overflow if dst is too small (needs strlen + 1!)
 strncpy(dst, src, n)  does NOT add '\0' if src has ≥ n chars
 s1 == s2              compares ADDRESSES, not text → use strcmp
 strcmp(a, b)          < 0 a before b  |  0 equal  |  > 0 a after b  (only the sign is guaranteed)
 strlen returns size_t (UNSIGNED) → strlen("ab") - strlen("abc") = huge, not -1
 '\0' = 0     '0' = 48     NULL = (void *)0      digit → int:  c - '0'
```

### 4. The heap
```
             ┌───────────────── HEAP ─────────────────┐
 malloc(n)   │ n bytes, GARBAGE values                 │
 calloc(k,n) │ k*n bytes, all ZERO                     │
 realloc     │ grow/shrink; may MOVE to a new address  │
 free(p)     │ gives it back; p still holds the old    │ → set p = NULL
             │ address (dangling)                      │
             └─────────────────────────────────────────┘
 Always check:  if (p == NULL) → allocation failed
```

### 5. Heap bugs
| Bug | Code | Result |
|---|---|---|
| Leak | `p = malloc(..);` never freed | memory lost |
| Double free | `free(p); free(p);` | UB |
| Use after free | `free(p); *p = 5;` | UB |
| Off by one | `malloc(strlen(s))` then `strcpy` | overflow, since there's no room for `'\0'` |
| Lost on realloc | `p = realloc(p, n);` fails → `p = NULL` | old block leaked. Use `tmp` |
| ✅ Safe | `free(NULL)` | does nothing |

### 6. Changing a pointer inside a function needs `**`
```c
void alloc(int *p)  { p = malloc(sizeof(int)); }   /* ✗ changes only the copy; caller's q stays NULL, block leaks */
void alloc(int **p) { *p = malloc(sizeof(int)); } /* ✓ call alloc(&q) */
```
It's the same rule as `swap`: to change something in the caller, pass **its address**.

---

## Practice Questions
Don't use `<string.h>` unless a problem says so. Solutions are in [answers.md](answers.md#practice-solutions).

| # | Problem | Input → Output |
|---|---|---|
| 1 | `my_strlen`, `my_strcpy`, `my_strcmp` | `"abc","abd"` → `cmp < 0` |
| 2 | Reverse a string in place | `"hello"` → `"olleh"` |
| 3 | Palindrome check, ignoring case | `"Madam"` → `yes` |
| 4 | Reverse the **words** of a sentence | `"I love C"` → `"C love I"` |
| 5 | Count vowels, consonants, digits and spaces | `"Hi 2 you"` → `3 2 1 2` |
| 6 | `my_atoi` with sign and leading spaces | `"  -123"` → `-123` |
| 7 | `my_itoa(int n, char *buf)` | `-450` → `"-450"` |
| 8 | First non-repeating character | `"swiss"` → `'w'` |
| 9 | Anagram check | `"listen","silent"` → `yes` |
| 10 | Compress a string | `"aaabbc"` → `"a3b2c1"` |
| 11 | `malloc` an array of n ints, fill it, print it, free it | `n=4` → `1 2 3 4` |
| 12 | Dynamic array that doubles with `realloc` (`tmp` pattern) | add 10 → `cap 2→4→8→16` |
| 13 | `char *my_strdup(const char *s)` | `"sai"` → new heap copy |

**Explain out loud:**
14. Why is `strncpy` dangerous?
15. Why does `alloc(int *p)` fail, and how does `int **` fix it?

---

## Interview One-liner
*"Every string needs strlen + 1 bytes. Check every malloc for NULL, free once, and set the pointer to NULL."*
