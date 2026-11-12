# Answers: Linked Lists

[← Notes](README.md)


<a id="practice-solutions"></a>
## Practice solutions

### Singly linked list: problems 1–7 in one program
```c
#include <stdio.h>
#include <stdlib.h>

struct Node { int v; struct Node *next; };

static struct Node *new_node(int v) {
    struct Node *n = malloc(sizeof *n);
    if (!n) exit(1);
    n->v = v; n->next = NULL;
    return n;
}
/* 1. insert at head / tail, print */
void push_head(struct Node **head, int v) { struct Node *n = new_node(v); n->next = *head; *head = n; }
void push_tail(struct Node **head, int v) {
    struct Node *n = new_node(v), **pp = head;
    while (*pp) pp = &(*pp)->next;        /* walk to the last next-pointer */
    *pp = n;
}
void print(const struct Node *h) { for (; h; h = h->next) printf("%d%s", h->v, h->next ? "->" : "\n"); }

/* 2. delete first node with value v */
void delete_val(struct Node **head, int v) {
    for (struct Node **pp = head; *pp; pp = &(*pp)->next)
        if ((*pp)->v == v) { struct Node *dead = *pp; *pp = dead->next; free(dead); return; }
}

/* 3. reverse: iterative and recursive */
struct Node *reverse_it(struct Node *head) {
    struct Node *prev = NULL, *cur = head;
    while (cur) { struct Node *next = cur->next; cur->next = prev; prev = cur; cur = next; }
    return prev;
}
struct Node *reverse_rec(struct Node *head) {
    if (!head || !head->next) return head;
    struct Node *rest = reverse_rec(head->next);
    head->next->next = head;              /* the node after head points back to head */
    head->next = NULL;
    return rest;
}

/* 4. middle, and n-th from the end */
struct Node *middle(struct Node *h) {
    struct Node *slow = h, *fast = h;
    while (fast && fast->next) { slow = slow->next; fast = fast->next->next; }
    return slow;
}
struct Node *nth_from_end(struct Node *h, int n) {
    struct Node *fast = h, *slow = h;
    while (n-- > 0) { if (!fast) return NULL; fast = fast->next; }
    while (fast) { fast = fast->next; slow = slow->next; }
    return slow;
}

/* 5. detect and remove a loop (Floyd) */
int remove_loop(struct Node *h) {
    struct Node *slow = h, *fast = h;
    while (fast && fast->next) {
        slow = slow->next; fast = fast->next->next;
        if (slow == fast) break;
    }
    if (!fast || !fast->next) return 0;               /* no loop */
    slow = h;                                          /* both now move 1 step: they meet at the loop start */
    if (slow == fast) { while (fast->next != slow) fast = fast->next; }
    else { while (slow->next != fast->next) { slow = slow->next; fast = fast->next; } }
    fast->next = NULL;                                 /* break the loop */
    return 1;
}

/* 6. merge two sorted lists */
struct Node *merge(struct Node *a, struct Node *b) {
    struct Node dummy = {0, NULL}, *t = &dummy;
    while (a && b) {
        if (a->v <= b->v) { t->next = a; a = a->next; } else { t->next = b; b = b->next; }
        t = t->next;
    }
    t->next = a ? a : b;
    return dummy.next;
}

/* 7. free safely */
void free_list(struct Node **head) {
    struct Node *cur = *head;
    while (cur) { struct Node *next = cur->next; free(cur); cur = next; }
    *head = NULL;
}

int main(void) {
    struct Node *h = NULL;
    push_tail(&h, 2); push_tail(&h, 3); push_head(&h, 1); print(h);    /* 1->2->3 */
    delete_val(&h, 2); print(h);                                        /* 1->3 */
    push_tail(&h, 4); push_tail(&h, 5);
    h = reverse_it(h); print(h);                                        /* 5->4->3->1 */
    h = reverse_rec(h); print(h);                                       /* 1->3->4->5 */
    printf("mid %d, 2nd from end %d\n", middle(h)->v, nth_from_end(h, 2)->v);   /* mid 4 (2nd of two middles), 4 */

    h->next->next->next->next = h->next;     /* make a loop: 5 -> 3 */
    printf("loop removed: %d\n", remove_loop(h)); print(h);            /* 1 */

    struct Node *a = NULL, *b = NULL;
    push_tail(&a, 1); push_tail(&a, 3); push_tail(&a, 5);
    push_tail(&b, 2); push_tail(&b, 4);
    struct Node *m = merge(a, b); print(m);                             /* 1->2->3->4->5 */

    free_list(&h); free_list(&m);
    return 0;
}
```
`struct Node **pp` points at "the pointer that points to the current node", so deleting or inserting at the head needs no special case.

### Doubly linked list: problems 8–14 in one program
```c
#include <stdio.h>
#include <stdlib.h>

struct DNode { int v; struct DNode *prev, *next; };

static struct DNode *dnew(int v) {
    struct DNode *n = malloc(sizeof *n);
    if (!n) exit(1);
    n->v = v; n->prev = n->next = NULL;
    return n;
}
/* 8. insert at head / tail */
void d_push_head(struct DNode **head, int v) {
    struct DNode *n = dnew(v);
    n->next = *head;
    if (*head) (*head)->prev = n;
    *head = n;
}
void d_push_tail(struct DNode **head, int v) {
    struct DNode *n = dnew(v);
    if (!*head) { *head = n; return; }
    struct DNode *t = *head;
    while (t->next) t = t->next;
    t->next = n; n->prev = t;
}
/* 9. print forward and backward */
void d_print(const struct DNode *h) {
    const struct DNode *t = NULL;
    for (; h; h = h->next) { printf("%d ", h->v); t = h; }
    printf("/ ");
    for (; t; t = t->prev) printf("%d ", t->v);
    printf("\n");
}
/* 10. insert after the first node with value x */
int d_insert_after(struct DNode *head, int x, int v) {
    for (struct DNode *c = head; c; c = c->next)
        if (c->v == x) {
            struct DNode *n = dnew(v);
            n->prev = c; n->next = c->next;
            if (c->next) c->next->prev = n;
            c->next = n;
            return 0;
        }
    return -1;
}
/* unlink and free one node: works for head, middle and tail */
static void d_unlink(struct DNode **head, struct DNode *x) {
    if (x->prev) x->prev->next = x->next; else *head = x->next;
    if (x->next) x->next->prev = x->prev;
    free(x);
}
/* 11. delete the first node with value v */
void d_delete(struct DNode **head, int v) {
    for (struct DNode *c = *head; c; c = c->next) if (c->v == v) { d_unlink(head, c); return; }
}
/* 12. reverse: swap prev/next everywhere */
void d_reverse(struct DNode **head) {
    struct DNode *c = *head, *last = NULL;
    while (c) {
        struct DNode *t = c->next;
        c->next = c->prev; c->prev = t;
        last = c; c = t;
    }
    if (last) *head = last;
}
/* 13. delete all occurrences */
void d_delete_all(struct DNode **head, int v) {
    struct DNode *c = *head;
    while (c) { struct DNode *next = c->next; if (c->v == v) d_unlink(head, c); c = next; }
}
/* 14. free all, then NULL the head so no dangling pointer is left behind */
void d_free(struct DNode **head) {
    struct DNode *c = *head;
    while (c) { struct DNode *next = c->next; free(c); c = next; }
    *head = NULL;
}

int main(void) {
    struct DNode *h = NULL;
    d_push_tail(&h, 20); d_push_tail(&h, 30); d_push_head(&h, 10); d_print(h);  /* 10 20 30 / 30 20 10 */
    d_insert_after(h, 20, 25); d_print(h);                                      /* 10 20 25 30 */
    d_delete(&h, 10); d_print(h);                                               /* 20 25 30 */
    d_reverse(&h); d_print(h);                                                  /* 30 25 20 */
    d_free(&h);
    d_push_tail(&h, 1); d_push_tail(&h, 2); d_push_tail(&h, 1); d_push_tail(&h, 3);
    d_delete_all(&h, 1); d_print(h);                                            /* 2 3 */
    d_free(&h);
    printf("head is %s\n", h ? "dangling" : "NULL");
    return 0;
}
```
**Why `head = NULL` after freeing?** Freeing doesn't change the pointer value. Without the reset, `head` still holds the old address (a dangling pointer). Any later use or second free is UB, while `NULL` is safe to test and safe to `free`.
