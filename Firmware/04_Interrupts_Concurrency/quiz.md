# Quiz: Interrupts and Concurrency

[← Back to notes](README.md)

Answer every question before you check the [answer sheet](answers.md). Pass mark: **80%**.

**Q1.** Without `volatile`, a flag set by an ISR may never be seen by `main` because:
- a) The CPU cache hides it
- b) The compiler keeps it in a register and stops re-reading memory
- c) The ISR runs on another core
- d) Flash is read-only

**Q2.** Which is allowed inside an ISR?
- a) `mutex_lock()`
- b) `sleep()`
- c) Clearing a status bit and setting a flag
- d) `printf` of 1 KB

**Q3.** Thread and ISR share data on a single core. The thread should:
- a) Take a mutex
- b) Disable local interrupts while accessing it
- c) Do nothing; `volatile` is enough
- d) Sleep until the ISR finishes

**Q4.** `count++` from two threads can lose updates because:
- a) It is one instruction
- b) It is load, add, store and can interleave
- c) `int` is too small
- d) The stack overflows

**Q5.** Thread A holds L1 and waits for L2; thread B holds L2 and waits for L1. This is:
- a) A race condition
- b) Priority inversion
- c) Deadlock
- d) Starvation by design

**Q6.** The key difference between a mutex and a binary semaphore:
- a) Speed
- b) Ownership: only the locker can unlock a mutex
- c) Semaphores only work between processes
- d) Mutexes can be given from an ISR

**Q7.** Priority inheritance:
- a) Lowers the high task's priority
- b) Temporarily raises the lock holder to the waiter's priority
- c) Kills the medium task
- d) Disables interrupts

**Q8.** Heavy processing after an interrupt belongs in:
- a) The ISR
- b) The bottom half (workqueue/tasklet/task)
- c) The vector table
- d) The startup code

**Q9.** What does each thread have privately?
- a) Heap
- b) Global variables
- c) Stack, registers and program counter
- d) Open files

**Q10.** An error interrupt fires continuously. Most likely:
- a) The status bit was never cleared
- b) The stack is too big
- c) `volatile` is missing
- d) The clock is too slow
