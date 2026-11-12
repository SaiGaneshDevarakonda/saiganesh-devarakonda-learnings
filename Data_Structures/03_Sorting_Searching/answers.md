# Answers: Searching and Sorting

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz: Searching and sorting (16 questions)

1. **b)** Sorted
2. **a)** `10`: log2(1000) ≈ 10.
3. **b)** low + high can overflow an int
4. **b)** An infinite loop
5. **a)** `2, 4, 3`
6. **b)** `{1,4,2,5,8}`
7. **b)** `O(n)`
8. **a)** `{11,12,25,22,64}`
9. **c)** Insertion sort
10. **c)** `O(n²)`
11. **c)** `O(n)`
12. **d)** Quick
13. **c)** Selection
14. **b)** `O(n log n)`
15. **a)** `{2,5,4,1}`
16. **b)** Merge sort


<a id="practice-solutions"></a>
## Practice solutions

### 1–4. Searching
```c
#include <stdio.h>
int bs_iter(const int *a, int n, int key) {
    int lo = 0, hi = n - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] == key) return mid;
        if (a[mid] < key) lo = mid + 1; else hi = mid - 1;
    }
    return -1;
}
int bs_rec(const int *a, int lo, int hi, int key) {
    if (lo > hi) return -1;
    int mid = lo + (hi - lo) / 2;
    if (a[mid] == key) return mid;
    return a[mid] < key ? bs_rec(a, mid + 1, hi, key) : bs_rec(a, lo, mid - 1, key);
}
/* 2. first (want_first=1) or last (0) occurrence */
int bs_bound(const int *a, int n, int key, int want_first) {
    int lo = 0, hi = n - 1, ans = -1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] == key) { ans = mid; if (want_first) hi = mid - 1; else lo = mid + 1; }  /* keep searching */
        else if (a[mid] < key) lo = mid + 1; else hi = mid - 1;
    }
    return ans;
}
/* 3. integer square root: largest m with m*m <= x */
int isqrt(int x) {
    int lo = 0, hi = x, ans = 0;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if ((long)mid * mid <= x) { ans = mid; lo = mid + 1; } else hi = mid - 1;
    }
    return ans;
}
/* 4. rotated sorted array: one half is always sorted */
int search_rotated(const int *a, int n, int key) {
    int lo = 0, hi = n - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] == key) return mid;
        if (a[lo] <= a[mid]) {                                   /* left half sorted */
            if (key >= a[lo] && key < a[mid]) hi = mid - 1; else lo = mid + 1;
        } else {                                                 /* right half sorted */
            if (key > a[mid] && key <= a[hi]) lo = mid + 1; else hi = mid - 1;
        }
    }
    return -1;
}
int main(void) {
    int a[] = {1, 3, 5, 7, 9, 11}, d[] = {1, 2, 2, 2, 3}, r[] = {4, 5, 6, 7, 0, 1, 2};
    printf("%d %d\n", bs_iter(a, 6, 7), bs_rec(a, 0, 5, 7));          /* 3 3 */
    printf("%d %d\n", bs_bound(d, 5, 2, 1), bs_bound(d, 5, 2, 0));    /* 1 3 */
    printf("%d\n", isqrt(17));                                        /* 4 */
    printf("%d\n", search_rotated(r, 7, 0));                          /* 4 */
    return 0;
}
```

### 5–11. Sorting exercises
```c
#include <stdio.h>
#include <string.h>
static void swap(int *x, int *y) { int t = *x; *x = *y; *y = t; }
static void show(const int *a, int n) { for (int i = 0; i < n; i++) printf("%d ", a[i]); printf("\n"); }

/* 5. bubble sort, returns the number of passes */
int bubble(int *a, int n) {
    int passes = 0;
    for (int i = 0; i < n - 1; i++) {
        int swapped = 0; passes++;
        for (int j = 0; j < n - 1 - i; j++) if (a[j] > a[j + 1]) { swap(&a[j], &a[j + 1]); swapped = 1; }
        if (!swapped) break;
    }
    return passes;
}
/* 6. selection sort, returns the number of swaps */
int selection(int *a, int n) {
    int swaps = 0;
    for (int i = 0; i < n - 1; i++) {
        int m = i;
        for (int j = i + 1; j < n; j++) if (a[j] < a[m]) m = j;
        if (m != i) { swap(&a[i], &a[m]); swaps++; }
    }
    return swaps;
}
/* 7. insertion sort, both versions */
void insertion_swap(int *a, int n) {
    for (int i = 1; i < n; i++) for (int j = i; j > 0 && a[j - 1] > a[j]; j--) swap(&a[j - 1], &a[j]);
}
void insertion_key(int *a, int n) {
    for (int i = 1; i < n; i++) {
        int key = a[i], j = i - 1;
        while (j >= 0 && a[j] > key) { a[j + 1] = a[j]; j--; }
        a[j + 1] = key;
    }
}
/* 8. generic sort with a comparator (insertion sort inside) */
typedef int (*cmp_t)(int, int);
int asc(int x, int y)  { return x - y; }
int desc(int x, int y) { return y - x; }
void sort_cmp(int *a, int n, cmp_t cmp) {
    for (int i = 1; i < n; i++) {
        int key = a[i], j = i - 1;
        while (j >= 0 && cmp(a[j], key) > 0) { a[j + 1] = a[j]; j--; }
        a[j + 1] = key;
    }
}
/* 9. sort structs by marks */
struct student { char name[16]; int marks; };
void sort_students(struct student *s, int n) {
    for (int i = 1; i < n; i++) {
        struct student key = s[i]; int j = i - 1;        /* struct assignment copies */
        while (j >= 0 && s[j].marks > key.marks) { s[j + 1] = s[j]; j--; }
        s[j + 1] = key;
    }
}
/* 10. Dutch national flag: low | mid | high */
void sort012(int *a, int n) {
    int lo = 0, mid = 0, hi = n - 1;
    while (mid <= hi) {
        if (a[mid] == 0) swap(&a[lo++], &a[mid++]);
        else if (a[mid] == 1) mid++;
        else swap(&a[mid], &a[hi--]);                    /* don't move mid: recheck the swapped-in value */
    }
}
/* 11. merge two sorted arrays */
void merge_arrays(const int *a, int na, const int *b, int nb, int *out) {
    int i = 0, j = 0, k = 0;
    while (i < na && j < nb) out[k++] = (a[i] <= b[j]) ? a[i++] : b[j++];
    while (i < na) out[k++] = a[i++];
    while (j < nb) out[k++] = b[j++];
}
int main(void) {
    int b[] = {1, 2, 3, 5, 4};          printf("passes %d: ", bubble(b, 5)); show(b, 5);     /* 2 */
    int s[] = {64, 25, 12, 22, 11};     printf("swaps %d: ", selection(s, 5)); show(s, 5);
    int i1[] = {5, 2, 4, 1, 3}, i2[] = {5, 2, 4, 1, 3};
    insertion_swap(i1, 5); show(i1, 5); insertion_key(i2, 5); show(i2, 5);
    int g[] = {3, 1, 2}; sort_cmp(g, 3, asc); show(g, 3); sort_cmp(g, 3, desc); show(g, 3);
    struct student st[] = {{"Ravi", 78}, {"Anu", 92}, {"Kiran", 85}};
    sort_students(st, 3); for (int i = 0; i < 3; i++) printf("%s:%d ", st[i].name, st[i].marks); printf("\n");
    int f[] = {2, 0, 1, 2, 0}; sort012(f, 5); show(f, 5);
    int x[] = {1, 4, 7}, y[] = {2, 3, 9}, m[6]; merge_arrays(x, 3, y, 3, m); show(m, 6);
    return 0;
}
```
The comparator `x - y` can overflow for extreme values. The fully safe form is `(x > y) - (x < y)`.

### 12–13. Quick sort and merge sort
```c
#include <stdio.h>
static void swap(int *x, int *y) { int t = *x; *x = *y; *y = t; }
void quick(int *a, int lo, int hi) {
    if (lo >= hi) return;
    int p = a[hi], i = lo;
    for (int j = lo; j < hi; j++) if (a[j] < p) swap(&a[i++], &a[j]);
    swap(&a[i], &a[hi]);
    quick(a, lo, i - 1);
    quick(a, i + 1, hi);
}
void merge_sort(int *a, int *tmp, int lo, int hi) {
    if (lo >= hi) return;
    int mid = lo + (hi - lo) / 2;
    merge_sort(a, tmp, lo, mid);
    merge_sort(a, tmp, mid + 1, hi);
    int i = lo, j = mid + 1, k = lo;
    while (i <= mid && j <= hi) tmp[k++] = (a[i] <= a[j]) ? a[i++] : a[j++];
    while (i <= mid) tmp[k++] = a[i++];
    while (j <= hi)  tmp[k++] = a[j++];
    for (k = lo; k <= hi; k++) a[k] = tmp[k];
}
int main(void) {
    int q[] = {7, 2, 9, 4, 3}; quick(q, 0, 4);
    for (int i = 0; i < 5; i++) printf("%d ", q[i]);
    printf("\n");
    int m[] = {38, 27, 43, 3}, t[4]; merge_sort(m, t, 0, 3);
    for (int i = 0; i < 4; i++) printf("%d ", m[i]);
    printf("\n");
    return 0;
}
```

### 14. Merge sort on a linked list
```c
#include <stdio.h>
#include <stdlib.h>
struct Node { int v; struct Node *next; };
static struct Node *merge(struct Node *a, struct Node *b) {
    struct Node d = {0, NULL}, *t = &d;
    while (a && b) { if (a->v <= b->v) { t->next = a; a = a->next; } else { t->next = b; b = b->next; } t = t->next; }
    t->next = a ? a : b;
    return d.next;
}
struct Node *list_sort(struct Node *h) {
    if (!h || !h->next) return h;
    struct Node *slow = h, *fast = h->next;          /* find the middle */
    while (fast && fast->next) { slow = slow->next; fast = fast->next->next; }
    struct Node *right = slow->next;
    slow->next = NULL;                               /* split into two lists */
    return merge(list_sort(h), list_sort(right));    /* only relinks nodes: O(1) extra memory */
}
int main(void) {
    int v[] = {4, 1, 3, 2};
    struct Node *h = NULL;
    for (int i = 3; i >= 0; i--) { struct Node *n = malloc(sizeof *n); n->v = v[i]; n->next = h; h = n; }
    h = list_sort(h);
    for (struct Node *p = h; p; p = p->next) printf("%d ", p->v);   /* 1 2 3 4 */
    printf("\n");
    while (h) { struct Node *n = h->next; free(h); h = n; }
    return 0;
}
```

### 15. Insertion sort vs quick sort
Choose insertion sort for **small** arrays (roughly n < 16–32) or **nearly sorted** data. It runs in about O(n), works in place, is stable, uses no recursion or stack, and the code is tiny. That makes it ideal on an MCU. Real libraries switch quick sort to insertion sort for small partitions.

### 16. Quick sort's worst case
With the last element as the pivot, sorted input always picks the maximum. Every split is then n−1 and 0, giving n levels × n work = **O(n²)**, plus O(n) recursion depth, which risks a stack overflow. Avoid it with a **random pivot** or **median-of-three**, and recurse into the smaller part first to limit the stack depth.
