# Answers: Firmware Basics and GPIO

[← Notes](README.md) · [Quiz](quiz.md)

## Quiz answer key
1. **c)** The layer and the hardware tie decide it, not the language.
2. **b)** The reset handler sets up memory, then calls `main()`.
3. **b)** DDR bit = 1 → output.
4. **c)** `PINx` reads the actual pin.
5. **b)** For an input, `PORTx` bit = 1 enables the pull-up.
6. **b)** Pins 8–13 are PB0–PB5, so 10 → PB2.
7. **c)** Shifting an 8-bit value right by 13 leaves 0.
8. **b)** Without `volatile` the empty loop has no visible effect and is removed.
9. **b)** Pressed connects the pin to GND → 0 (active-low).
10. **b)** The schematic shows board wiring; the datasheet only shows chip pins.

## Practice question solutions
1. Firmware is defined by **where the code runs and what it controls** (hardware, no full OS), not by the language. C is also used for apps, compilers and kernels.
2. Freestanding = no OS and usually no standard library. The **reset handler / startup code** calls `main()`, and `main()` must never return.
3. Pin 8 → **PB0**. A0 → **PC0**.
4. (a) 13 is the board number; the bit is **5**. (b) It uses **PORTD**, but pin 13 is on **Port B**. Also bit 13 doesn't exist in an 8-bit register.
5. To act only on the **edge** (released → pressed). Reading the level alone would toggle continuously while the button is held.
6. The compiler only checks each file. The hidden `main()` references `loop()`, and the **linker** fails to find that symbol when joining the objects.
