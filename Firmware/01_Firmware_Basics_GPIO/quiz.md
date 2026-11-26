# Quiz: Firmware Basics and GPIO

[← Back to notes](README.md)

Answer every question before you check the [answer sheet](answers.md). Pass mark: **80%**.

**Q1.** What makes code "firmware"?
- a) It is written in C
- b) It runs from flash
- c) It is tied to specific hardware and controls it directly, usually without a full OS
- d) It has no functions

**Q2.** In freestanding firmware, who calls `main()`?
- a) The OS loader
- b) The reset handler / startup code
- c) The C standard library
- d) The debugger

**Q3.** `DDRB |= (1U << 5);` does what?
- a) Sets PB5 HIGH
- b) Makes PB5 an output
- c) Reads PB5
- d) Enables the pull-up on PB5

**Q4.** Which register gives the **real level** of an input pin?
- a) `PORTx`
- b) `DDRx`
- c) `PINx`
- d) `MCUCR`

**Q5.** Pin configured as input, `PORTD |= (1U << 2);` does what?
- a) Drives PD2 HIGH
- b) Turns on PD2's pull-up resistor
- c) Clears PD2
- d) Nothing

**Q6.** Board pin 10 on the Uno-style mapping is:
- a) PD2
- b) PB2
- c) PC2
- d) PB10

**Q7.** `uint8_t r; r = (PIND >> 13) & 1U;` always gives:
- a) The pin 13 level
- b) 1
- c) 0
- d) The pin 5 level

**Q8.** Why is `volatile` used on the counter of an empty delay loop?
- a) To make it faster
- b) So the compiler doesn't delete the loop
- c) To put it in flash
- d) To make it atomic

**Q9.** With a pull-up and the button wired to GND, a pressed button reads:
- a) 1
- b) 0
- c) Floating
- d) 5

**Q10.** Which document tells you which chip leg connects to which board header?
- a) The chip datasheet
- b) The board schematic
- c) The compiler manual
- d) The ISA manual
