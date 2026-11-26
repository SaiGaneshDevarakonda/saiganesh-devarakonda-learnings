# 04 · Interrupts and Concurrency

[Quiz](quiz.md) · [Answers](answers.md)

## Theory

### 1. `volatile`: what it really does

Without `volatile`, the **compiler** (not the CPU) may read a variable once, keep it in a CPU **register** and never read memory again, or drop writes it thinks are pointless. `volatile` forces a real read or write on every access. It does **not** bypass the hardware cache: that needs non-cacheable memory or cache maintenance.

Two classic cases:
```c
/* 1. Memory-mapped register: hardware changes it */
while (!(*(volatile uint32_t *)STATUS_REG & READY_BIT));

/* 2. Variable shared with an ISR */
volatile int data_ready = 0;
void isr(void) { data_ready = 1; }
while (!data_ready);          // main loop
```
`volatile` alone does **not** make sharing safe: it gives visibility, not atomicity. You still need locking or atomics.

### 2. ISR rules

Never sleep, wait, take a mutex or call anything that can block inside an ISR: there is no task context to put to sleep. Keep it short.

An error/status interrupt **keeps firing until its status bit is cleared**. The ISR must clear the source (often a write-1-to-clear register: `STATUS = (1U << pos);`) or it re-enters forever.

### 3. Top half / bottom half

Heavy work (e.g. parsing 1 KB of data when a frame arrives) must not run in the ISR.

```
IRQ ─► TOP HALF (the ISR)                     BOTTOM HALF (later, IRQs enabled)
       ack + clear status                     heavy processing
       copy a tiny bit of data       ──►      Linux: tasklet / workqueue / threaded IRQ
       signal and exit fast                   RTOS/bare metal: flag or queue → task
```

On Cortex-M there are also **two stacks**: **MSP** (main stack, used by ISRs and the kernel) and **PSP** (process stack, used by tasks).

### 4. Shared data between a thread and an ISR

An ISR can't use a mutex because a mutex may sleep. A plain spinlock alone **deadlocks on a single core**:

```
Thread: spin_lock(L) ──► [IRQ fires] ──► ISR: spin_lock(L) ... spins forever
                                          (thread can't run to release L)  ☠ DEADLOCK
```

Correct pattern:
```
Thread side:  spin_lock_irqsave(&L, flags);   // disable local IRQs + take lock
              shared = x;
              spin_unlock_irqrestore(&L, flags);

ISR side:     spin_lock(&L);                  // IRQs already off on this core
              shared = y;
              spin_unlock(&L);
```
- **Single core:** disabling interrupts in the thread is enough.
- **Multi-core:** disable interrupts **and** take the spinlock (the ISR may run on another core).
- **Alternatives:** lock-free single-producer/single-consumer ring buffer with memory barriers, atomics, or the ISR posting to a queue (`xQueueSendFromISR`) / setting a flag for a task.

Thread-to-thread sharing can use a mutex; thread-to-ISR sharing cannot.

### 5. Race condition

When the result depends on timing. `count++` is a read-modify-write: **load, add 1, store**.
```
count = 5
Thread A: load 5
Thread B: load 5
Thread A: add → 6, store 6
Thread B: add → 6, store 6   ❌ should be 7, one update lost
```
Fix: a mutex around it, or an atomic increment. A register mask write has the same problem when an ISR touches the same register.

### 6. Deadlock (not the same as a race)

Each thread holds one lock and waits forever for the other:
```
Thread A: lock(L1) ... wants L2 ──┐
Thread B: lock(L2) ... wants L1 ──┘  both wait forever
```
Fix: always take locks in the **same global order**.

### 7. Mutex vs binary semaphore

Both block a waiter, but the key difference is **ownership**, not threads vs processes:

| | Mutex | Binary semaphore |
|---|---|---|
| Owner | ✅ only the locker can unlock | ❌ anyone can give it, even an ISR |
| Priority inheritance | ✅ supported | ❌ |
| Used for | Protecting shared data | **Signalling** (ISR tells a task "data ready") |

### 8. Priority inversion and inheritance

```
Low task  : holds mutex M ─────────────╳ (preempted, can't release M)
Medium    :            wakes ▶▶▶▶▶▶▶▶▶ runs freely
High task :      needs M → BLOCKED, waiting behind Medium   ← inversion
```
The high task effectively waits for a lower-priority task. **Priority inheritance** fixes it: while High waits on M, Low is temporarily raised to High's priority, so Medium can't preempt it; Low finishes, releases M, drops back, and High runs. The famous real example is the Mars Pathfinder resets (1997).

### 9. Process vs thread

A process is a complete program with its own address space; it can contain multiple threads.

| Each thread owns | Threads in a process share |
|---|---|
| Stack, registers, program counter | Heap, globals, code, open files |

## Practice Questions
1. Why does a plain spinlock between a thread and an ISR deadlock on a single core?
2. Write the thread-side and ISR-side locking for shared data on Linux.
3. Why can't an ISR take a mutex?
4. Show, step by step, how `count++` loses an update.
5. What's the difference between a race condition and a deadlock?
6. An ISR must tell a task that data is ready. Mutex or semaphore? Why?
7. Explain priority inversion with three tasks, and its fix.
8. Why does an error ISR keep firing, and how do you stop it?

## Interview One-liner
ISRs never sleep, so thread–ISR sharing uses interrupt-disable plus spinlock (`spin_lock_irqsave` in the thread), not a mutex; keep ISRs short and defer heavy work to a bottom half; a mutex has an owner and priority inheritance, a semaphore is for signalling.
