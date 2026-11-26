# Answers: Interrupts and Concurrency

[← Notes](README.md) · [Quiz](quiz.md)

## Quiz answer key
1. **b)** It's a compiler optimisation, not a cache effect.
2. **c)** Short, non-blocking work only.
3. **b)** On one core, the ISR can't run while interrupts are off.
4. **b)** Read-modify-write is not atomic.
5. **c)** Circular wait.
6. **b)** Mutex = owner; semaphore = signalling.
7. **b)** So the medium task can't preempt the holder.
8. **b)** Top half acks and exits; bottom half does the work.
9. **c)** Everything else is shared.
10. **a)** Until the source is cleared (often W1C), the line stays asserted.

## Practice question solutions
1. The thread holds the lock, the IRQ preempts it on the same core, and the ISR spins waiting for a lock that can only be released by the thread it interrupted.
2. Thread: `spin_lock_irqsave(&L, flags); ... spin_unlock_irqrestore(&L, flags);` ISR: `spin_lock(&L); ... spin_unlock(&L);`
3. A mutex may sleep when contended, and an ISR has no task context that can sleep.
4. Both threads load 5; A adds and stores 6; B adds and stores 6. Expected 7.
5. A race gives a wrong result depending on timing; a deadlock makes threads wait forever on each other's locks.
6. **Semaphore.** It has no owner, so the ISR can give it and the task can take it.
7. Low holds the mutex, High blocks on it, Medium preempts Low, so High waits behind Medium. Fix: priority inheritance raises Low to High's priority until it releases the mutex.
8. The status bit stays set, so the interrupt line stays asserted. The ISR must clear it, usually by writing `1U << pos` to the W1C status register.
