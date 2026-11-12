# Quiz: Searching and Sorting

[← Back to notes](README.md)

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz: Searching and sorting (16 questions)


**Q1.**

Binary search only works correctly if the array is…

- a) Of even length
- b) Sorted
- c) Free of duplicates
- d) On the heap


**Q2.**

Worst-case comparisons for binary search on 1000 sorted elements, roughly?

- a) `10`
- b) `100`
- c) `500`
- d) `1000`


**Q3.**

Why write mid = low + (high - low) / 2 instead of (low + high) / 2?

- a) Faster
- b) low + high can overflow an int
- c) It rounds up
- d) No difference


**Q4.**

In binary search, writing low = mid (instead of mid + 1) can cause…

- a) A faster search
- b) An infinite loop
- c) A compile error
- d) Nothing


**Q5.**

```c
Binary search for 7 in {1,3,5,7,9,11}. Values of mid, in order?
```

- a) `2, 4, 3`
- b) `3`
- c) `2, 3`
- d) `5, 4, 3`


**Q6.**

```c
Bubble sort {5,1,4,2,8}. Array after the FIRST pass?
```

- a) `{1,2,4,5,8}`
- b) `{1,4,2,5,8}`
- c) `{1,5,4,2,8}`
- d) `{5,1,4,2,8}`


**Q7.**

Bubble sort with the 'swapped' flag on a sorted array runs in…

- a) `O(1)`
- b) `O(n)`
- c) `O(n log n)`
- d) `O(n²)`


**Q8.**

```c
Selection sort {64,25,12,22,11}. Array after 2 passes?
```

- a) `{11,12,25,22,64}`
- b) `{11,12,22,25,64}`
- c) `{11,25,12,22,64}`
- d) `{12,11,25,22,64}`


**Q9.**

Best sort for a SMALL, NEARLY SORTED array on a low-RAM MCU?

- a) Merge sort
- b) Quick sort
- c) Insertion sort
- d) Selection sort


**Q10.**

Quick sort (last element pivot) on an already SORTED array runs in…

- a) `O(n)`
- b) `O(n log n)`
- c) `O(n²)`
- d) `O(log n)`


**Q11.**

Extra memory for merge sort on an array?

- a) `O(1)`
- b) `O(log n)`
- c) `O(n)`
- d) `O(n²)`


**Q12.**

Which sort is NOT stable?

- a) Insertion
- b) Merge
- c) Bubble
- d) Quick


**Q13.**

Flash writes are expensive. Which sort does the FEWEST swaps?

- a) Bubble
- b) Insertion
- c) Selection
- d) Quick


**Q14.**

Average time complexity of quick sort?

- a) `O(n)`
- b) `O(n log n)`
- c) `O(n²)`
- d) `O(log n)`


**Q15.**

```c
Insertion sort {5,2,4,1}. Array after i = 1?
```

- a) `{2,5,4,1}`
- b) `{2,4,5,1}`
- c) `{1,2,4,5}`
- d) `{5,2,4,1}`


**Q16.**

Best sort for a LINKED LIST?

- a) Quick sort
- b) Merge sort
- c) Binary insertion
- d) Selection


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
