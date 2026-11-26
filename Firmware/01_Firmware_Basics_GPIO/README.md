# 01 · Firmware Basics and GPIO

[Quiz](quiz.md) · [Answers](answers.md)

## Theory

### 1. What firmware is

A programming language does not decide whether code is firmware. **Where the code runs and what it controls** decides it. C is only the tool: the same language is used for applications, compilers and kernels. A Python developer can be called a "Python engineer", but a C developer is not automatically a firmware engineer, because firmware is defined by the layer, not the language.

```
┌──────────────────────┐
│  Apps (Python/Java/C) │  ← application engineer
├──────────────────────┤
│  OS kernel (C)        │  ← kernel / driver engineer
├──────────────────────┤
│  FIRMWARE (C / asm)   │  ← firmware engineer: talks to registers,
├──────────────────────┤     no OS (or a tiny RTOS), boots the chip
│  HARDWARE / SoC       │
└──────────────────────┘
```

**Firmware** is software permanently tied to one specific piece of hardware: a boot ROM, a platform/boot loader, ARM Trusted Firmware, a display or camera controller's code.

| | Firmware | OS |
|---|---|---|
| Purpose | Brings up and drives **one specific piece of hardware** | Manages shared resources for **many apps** |
| Runs on | Bare metal or an RTOS | Kernel plus user space |
| Has | Startup code, register access, ISRs | Scheduler, MMU/virtual memory, file system, driver framework |
| Examples | Boot ROM, first-stage loader, TF-A, U-Boot, peripheral controller code | Linux, QNX, Android |

### 2. "Pure C" = freestanding C

Firmware C is **freestanding**: no OS, often no standard library. It is just your code, the hardware registers, a startup file and a linker script. `main()` is called by your own reset handler, not by an OS, and `main()` must never return.

Arduino hides this: its own hidden `main()` calls `setup()` once and then `loop()` forever. If `loop()` is missing, the program fails at the **link** step ("undefined reference to `loop`"), not the compile step, so the board silently keeps running the old program.

### 3. Ports and bits

A **port** is a group of up to 8 (or 32) pins controlled by **one register**. The **bit number** is the pin's position inside that group. Every port has three registers (x = the port letter):

```
           Port B     Port C     Port D
DDRx   →   DDRB       DDRC       DDRD     "Data Direction Register"
PORTx  →   PORTB      PORTC      PORTD    "Data Register"
PINx   →   PINB       PINC       PIND     "Input Pins Address"
```

| Register | Datasheet name | What it does |
|---|---|---|
| `DDRx` | Data Direction Register | 1 = output, 0 = input |
| `PORTx` | Data Register | Output: 1 = HIGH, 0 = LOW. Input: 1 = pull-up on |
| `PINx` | Input Pins Address | **Read** the real pin level |

Reading one register box in a datasheet:

```
Bit     7      6      5      4      3      2      1      0
      PORTB7 PORTB6 PORTB5 PORTB4 PORTB3 PORTB2 PORTB1 PORTB0
R/W    R/W    R/W    R/W    R/W    R/W    R/W    R/W    R/W
Init    0      0      0      0      0      0      0      0
```
- **Bit row:** the bit position, i.e. `pos` in `1U << pos`
- **R/W:** read/write (R = read only)
- **Init:** value after reset

### 4. Board pin ≠ chip pin

The board prints its own labels (0–13, A0–A5). The chip only knows **port + bit**. The mapping is set in three layers:

```
Datasheet (chip)   →   Schematic (board)     →   Software
"physical leg 19   →   "leg 19 wired to the  →   "pin 13 = PORTB, bit 5"
 is PB5"                header labelled 13"       (lookup table in the board support code)
```

The board designer decides the mapping, so engineers keep a **pinout cheat sheet** rather than deriving it each time. Example (ATmega328P on an Uno-style board):

```
 TOP HEADER →  13  12  11  10   9   8  │  7   6   5   4   3   2   1   0
 chip pin   →  PB5 PB4 PB3 PB2 PB1 PB0 │ PD7 PD6 PD5 PD4 PD3 PD2 PD1 PD0
               └────── PORT B ───────┘   └──────────── PORT D ──────────┘
 BOTTOM     →  A0  A1  A2  A3  A4  A5
 chip pin   →  PC0 PC1 PC2 PC3 PC4 PC5   ← PORT C
```

| Board pin | Port | Bit |
|---|---|---|
| 0–7 | D | same number |
| 8–13 | B | pin − 8 (13 → bit 5) |
| A0–A5 | C | 0–5 |

In a real job this is daily work: read the **chip datasheet** plus the **board schematic**, find "LED is on GPIO bank B bit 5", and put it in a board header or a **device tree**.

### 5. Register-only GPIO program

LED on pin 13 (PB5) through a 220 Ω resistor, button on pin 2 (PD2) to GND:

```
  pin 13 ──[220Ω]──►|── GND        LED    → Port B, bit 5 → output
  pin 2 ──[button]── GND           Button → Port D, bit 2 → input + pull-up
                                            released = 1, pressed = 0 (active-low)
```

Each press toggles the LED once (edge detection + debounce):

```c
void setup() {
  DDRB  |=  (1U << 5);     // PB5 (pin 13) → output
  DDRD  &= ~(1U << 2);     // PD2 (pin 2)  → input
  PORTD |=  (1U << 2);     // PD2 pull-up on
}

uint8_t prev = 1;          // 1 = released

void loop() {
  uint8_t now = (PIND >> 2) & 1U;          // read PD2
  if (prev == 1 && now == 0) {             // released → pressed edge
    PORTB ^= (1U << 5);                    // toggle PB5
    for (volatile long i = 0; i < 30000; i++);   // debounce
  }
  prev = now;
}
```

The most common bug is using the **board pin number instead of the bit number**: `PORTD |= (1U << 13)` does nothing, because an 8-bit register only has bits 0–7, and `(PIND >> 13)` is always 0, so the button looks permanently pressed. **Always use the bit inside the port, and keep the shift within the register width.**

The empty delay loop uses `volatile`: without it the compiler deletes the loop as useless.

## Practice Questions
1. Why is a C programmer not automatically a firmware engineer?
2. What does "freestanding C" mean, and who calls `main()` in firmware?
3. Which port and bit is board pin 8? Board pin A0?
4. A student writes `PORTD |= (1U << 13);` for board pin 13. List two things wrong.
5. Why does the button code compare `prev` and `now` instead of just reading the pin?
6. Why is a missing `loop()` a link error and not a compile error?

## Interview One-liner
Firmware is defined by the layer, not the language: it is freestanding code tied to specific hardware, controlling it through registers addressed as port + bit, started by its own reset handler instead of an OS.
