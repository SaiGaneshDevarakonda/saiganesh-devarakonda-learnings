# Firmware

Embedded firmware from the ground up: what firmware is, talking to hardware registers, what runs before `main()`, and interrupts with shared data.

Each topic folder has three files:

| File | What it has |
|---|---|
| `README.md` | **Theory** (diagrams), **Practice Questions**, **Interview One-liner** |
| `quiz.md` | Multiple-choice quiz (questions only) with a link to its answer sheet |
| `answers.md` | Quiz answer key **and** solutions to the practice questions |

## Topics
| # | Topic | Highlights |
|---|---|---|
| 01 | [Firmware Basics and GPIO](01_Firmware_Basics_GPIO/README.md) | firmware vs OS, freestanding C, ports and bits, board pin vs chip pin, a register-only button/LED program |
| 02 | [Register Access](02_Register_Access/README.md) | single-bit and field mask writes, W1C registers, `volatile const`, macro vs struct overlay, `->` vs `.`, register-map padding |
| 03 | [Memory Layout, Boot and Startup](03_Memory_Layout_Boot_Startup/README.md) | sections, ROM/Flash/RAM, vector table, `Reset_Handler`, linker symbols, MCU vs SoC boot chain |
| 04 | [Interrupts and Concurrency](04_Interrupts_Concurrency/README.md) | ISR rules, top/bottom half, thread–ISR locking, mutex vs semaphore, priority inversion, race vs deadlock |

Suggested order: 01 → 04, each one's quiz after its notes.
