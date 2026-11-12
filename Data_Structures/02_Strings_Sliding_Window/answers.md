# Answers: Substrings and Sliding Window

[← Notes](README.md)


<a id="practice-solutions"></a>
## Practice solutions

### 1. Print all substrings
```c
#include <stdio.h>
#include <string.h>
void print_substrings(const char *s) {
    int n = (int)strlen(s);
    for (int i = 0; i < n; i++)              /* start */
        for (int j = i; j < n; j++)          /* end */
            printf("%.*s ", j - i + 1, s + i);
    printf("\n");
}
int main(void) { print_substrings("abc"); return 0; }   /* a ab abc b bc c */
```

### 2. Longest unique substring: simple O(n²)
```c
#include <stdio.h>
int longest_simple(const char *s) {
    int best = 0;
    for (int i = 0; s[i]; i++) {
        int seen[256] = {0}, count = 0;           /* fresh table for every start */
        for (int j = i; s[j]; j++) {
            unsigned char c = (unsigned char)s[j];
            if (seen[c]) break;                   /* repeat → stop this start */
            seen[c] = 1;
            count++;
        }
        if (count > best) best = count;
    }
    return best;
}
int main(void) { printf("%d %d\n", longest_simple("welcome all"), longest_simple("abcabcbb")); return 0; }   /* 7 3 */
```

### 3. Longest unique substring: sliding window O(n)
```c
#include <stdio.h>
int longest_fast(const char *s) {
    int last[256];
    for (int i = 0; i < 256; i++) last[i] = -1;   /* -1 = never seen (0 is a real index) */
    int left = 0, best = 0;
    for (int right = 0; s[right]; right++) {
        unsigned char c = (unsigned char)s[right];
        if (last[c] >= left) left = last[c] + 1;  /* repeat inside the window → jump past it */
        last[c] = right;
        if (right - left + 1 > best) best = right - left + 1;
    }
    return best;
}
int main(void) {
    printf("%d %d %d %d %d\n", longest_fast("welcome all"), longest_fast("abcabcbb"),
           longest_fast("abba"), longest_fast("bbbbb"), longest_fast(""));          /* 7 3 2 1 0 */
    return 0;
}
```
Dry run on `"abba"`:

| right | ch | last[ch] | ≥ left? | left | window | best |
|---|---|---|---|---|---|---|
| 0 | a | −1 | no | 0 | a | 1 |
| 1 | b | −1 | no | 0 | ab | 2 |
| 2 | b | 1 | yes | 2 | b | 2 |
| 3 | a | 0 | **no** (0 < 2) | 2 | ba | 2 |

### 4. Find a substring
```c
#include <stdio.h>
int find(const char *s, const char *t) {
    for (int i = 0; s[i]; i++) {
        int j = 0;
        while (t[j] && s[i + j] == t[j]) j++;
        if (t[j] == '\0') return i;             /* matched all of t */
    }
    return (*t == '\0') ? 0 : -1;
}
int main(void) { printf("%d %d\n", find("hello", "ell"), find("hello", "xyz")); return 0; }   /* 1 -1 */
```

### 5. Max sum of k consecutive elements
```c
#include <stdio.h>
int max_sum_k(const int *a, int n, int k) {
    int sum = 0;
    for (int i = 0; i < k; i++) sum += a[i];      /* first window */
    int best = sum;
    for (int i = k; i < n; i++) {
        sum += a[i] - a[i - k];                   /* slide: add the new, drop the old */
        if (sum > best) best = sum;
    }
    return best;
}
int main(void) { int a[] = {2, 1, 5, 1, 3, 2}; printf("%d\n", max_sum_k(a, 6, 3)); return 0; }   /* 9 */
```

### 6. Smallest subarray with sum ≥ target
```c
#include <stdio.h>
int min_len(const int *a, int n, int target) {
    int left = 0, sum = 0, best = n + 1;
    for (int right = 0; right < n; right++) {
        sum += a[right];
        while (sum >= target) {                   /* shrink while still valid */
            if (right - left + 1 < best) best = right - left + 1;
            sum -= a[left++];
        }
    }
    return best == n + 1 ? 0 : best;
}
int main(void) { int a[] = {2, 3, 1, 2, 4, 3}; printf("%d\n", min_len(a, 6, 7)); return 0; }   /* 2 ({4,3}) */
```

### 7. At most k distinct characters
```c
#include <stdio.h>
int longest_k_distinct(const char *s, int k) {
    int cnt[256] = {0}, distinct = 0, left = 0, best = 0;
    for (int right = 0; s[right]; right++) {
        if (cnt[(unsigned char)s[right]]++ == 0) distinct++;
        while (distinct > k)
            if (--cnt[(unsigned char)s[left++]] == 0) distinct--;
        if (right - left + 1 > best) best = right - left + 1;
    }
    return best;
}
int main(void) { printf("%d\n", longest_k_distinct("eceba", 2)); return 0; }   /* 3 ("ece") */
```
