# 01 · Linked Lists (Singly and Doubly)

**Answers and solutions:** [answers.md](answers.md) · The linked-list quiz questions are part of [C/08 quiz](../../C/08_Functions_Recursion_Embedded/quiz.md) (Q16–Q20).

---

## Theory

### 1. Singly linked list (SLL)
```
 head
  │
  ▼
 ┌────┬──────┐   ┌────┬──────┐   ┌────┬──────┐
 │ 10 │ next ┼──►│ 20 │ next ┼──►│ 30 │ NULL │
 └────┴──────┘   └────┴──────┘   └────┴──────┘
```
```c
struct Node { int v; struct Node *next; };    /* a struct can hold a POINTER to itself, not itself */
```

### 2. To change `head`, pass `struct Node **`
```c
void push(struct Node *head, int v)  { ... head = n; }   /* ✗ changes only the copy; the node leaks   */
void push(struct Node **head, int v) { ... *head = n; }  /* ✓ call push(&list, 5)                    */
```
It's the same rule as `swap(int *a, int *b)`: to change the caller's variable, pass its address.

### 3. The patterns you'll be asked about
**Reverse (3 pointers)**
```
 prev = NULL, cur = head
 while (cur) { next = cur->next; cur->next = prev; prev = cur; cur = next; }
 head = prev

 1 → 2 → 3 → NULL     becomes     NULL ← 1 ← 2 ← 3   (head = 3)
```
**Slow/fast pointers**
```
 slow += 1 step, fast += 2 steps
 fast reaches the end  → slow is at the MIDDLE            1→2→3→4→5 : middle = 3
 slow == fast          → there is a LOOP (Floyd)          O(n) time, O(1) memory
 n-th from end         → move fast n steps first, then move both until fast hits NULL
```
**Safe free:** save `next` **before** freeing:
```c
while (cur) { struct Node *next = cur->next; free(cur); cur = next; }   /* free(cur); cur = cur->next; is use-after-free */
```

### 4. Doubly linked list (DLL)
```
 NULL ← [prev | 10 | next] ⇄ [prev | 20 | next] ⇄ [prev | 30 | next] → NULL
          head                                      tail
```
```c
struct DNode { int v; struct DNode *prev, *next; };
```
| Operation | Pointers to fix |
|---|---|
| insert at head | `n->next = head; if (head) head->prev = n; head = n;` |
| insert after node x | `n->prev = x; n->next = x->next; if (x->next) x->next->prev = n; x->next = n;` |
| delete node x | `if (x->prev) x->prev->next = x->next; else head = x->next;` and `if (x->next) x->next->prev = x->prev;` |
| reverse | swap `prev`/`next` in every node; the new head is the old tail |

**Always handle three cases:** the node is the **head**, in the **middle**, or the **tail** (and the list may be empty or have one node).

| | SLL | DLL |
|---|---|---|
| Memory per node | 1 pointer | 2 pointers |
| Delete when you only have a pointer to the node | O(n) (need the previous node) | **O(1)** |
| Traverse backwards | ❌ | ✅ |

---

## Practice Questions
Use `struct Node **head` wherever head can change. Solutions are in [answers.md](answers.md#practice-solutions).

**Singly linked list**
| # | Problem | Input → Output |
|---|---|---|
| 1 | Insert at head, insert at tail, print | → `1→2→3` |
| 2 | Delete a node by value | `1→2→3`, del 2 → `1→3` |
| 3 | Reverse: iterative and recursive | `1→2→3` → `3→2→1` |
| 4 | Middle node, and the n-th node from the end | `1→2→3→4→5` → mid `3`, 2nd-from-end `4` |
| 5 | Detect a loop, then **remove** it | loop at 3 → loop removed |
| 6 | Merge two **sorted** lists | `1→3→5`, `2→4` → `1→2→3→4→5` |
| 7 | Free the whole list safely | no leak |

**Doubly linked list**
| # | Problem | Input → Output |
|---|---|---|
| 8 | Insert at head and at tail | → `10⇄20⇄30` |
| 9 | Print forward **and** backward | `10 20 30` / `30 20 10` |
| 10 | Insert after a given value | after 20 insert 25 → `10⇄20⇄25⇄30` |
| 11 | Delete by value: head, middle, tail | del 10 → `20⇄30` |
| 12 | Reverse a DLL | `10⇄20⇄30` → `30⇄20⇄10` |
| 13 | Delete all occurrences of a value | `1⇄2⇄1⇄3`, del 1 → `2⇄3` |
| 14 | Free the whole DLL. Why set `head = NULL` afterwards? | no leak, no dangling pointer |

---

## Interview One-liner
*"Change head through a double pointer. Reverse with prev/cur/next. Use slow/fast for the middle and for loops. Save next before you free."*
