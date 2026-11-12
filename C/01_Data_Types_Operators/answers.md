# Answers: Data Types and Operators

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz 1: Warm-up (12 questions)

1. **b)** B: b is converted to unsigned → 0xFFFFFFFF (UINT_MAX), which is > 1. The condition is false, so it prints B.
2. **b)** `4, 8, 8`: LP64: int = 4, long = 8, pointer = 8. On 32-bit ARM (ILP32) all three are 4. Windows 64-bit (LLP64) has long = 4. Use uint32_t etc. in firmware.
3. **c)** -1 on x86 GCC, 255 on ARM GCC: The signedness of plain char is implementation-defined. x86 GCC: signed → -1. ARM GCC: unsigned by default → 255. Use signed char / unsigned char / uint8_t explicitly.
4. **d)** Undefined behaviour: i is modified twice with no sequence point in between → UB. In an interview say 'UB' and explain why; don't guess a number.
5. **b)** `4 10`: sizeof is computed at compile time and its operand is NOT evaluated (except VLAs). i++ never runs → 4 10.
6. **b)** `8`: + binds tighter than <<, so it is x << (1+1) = 2 << 2 = 8. Always bracket shifts: (x << 1) + 1.
7. **c)** `300 44`: a and b are promoted to int → 300. Stored in int: 300. Stored in unsigned char: 300 mod 256 = 44 (well-defined).
8. **b)** `4.0`: 5/2 = 2 (integer division), then 2 * 2.0 = 4.0 (double).
9. **c)** `1 0`: a++ returns 0 (then a = 1). && short-circuits, so b++ never runs → 1 0.
10. **b)** UINT_MAX + 1u: Unsigned arithmetic wraps modulo 2^N → 0. Signed overflow and shifting a 1 into the sign bit are UB. Use 1u << 31 for bit-31 masks.
11. **b)** `15`: The comma operator evaluates left to right and returns the last value → 15. (Without brackets, x = 5, 10, 15; assigns 5.)
12. **b)** `4`: In C a character constant has type int → 4. In C++ it is char → 1.

<a id="quiz-2"></a>
## Quiz 2: Retest (10 questions)

1. **b)** `1`: a becomes unsigned, so a + b = -2 as unsigned = 4294967294, which is > 0 → 1.
2. **b)** `4`: 260 as int, stored in unsigned char: 260 - 256 = 4.
3. **b)** `4`: Both shorts are promoted to int before adding → the result is int → 4.
4. **a)** `3`: sizeof never evaluates its operand → ++i never runs → 3.
5. **a)** Y: == binds tighter than &: x & (2 == 2) = 1 & 1 = 1 → Y. The intended (x & 2) == 2 would give N.
6. **a)** `0`: a is true, so || short-circuits and b++ never runs → 0.
7. **b)** `a[i] = i++;`: In a[i] = i++, i is read and modified with no sequence point → UB. The comma operator and && ARE sequence points.
8. **b)** `-128`: 0x80 = 1000 0000. Top bit set → 128 - 256 = -128.
9. **b)** No: 0.1 is not exact in binary. The float copy is less precise than the double literal 0.1 → not equal → No.
10. **b)** -3 -1: Since C99 division truncates toward zero → -3. The remainder takes the sign of the dividend → -1.

<a id="quiz-3"></a>
## Quiz 3: Mega test 1 (50 questions)

1. **b)** `24`: 010 is octal = 8, 0x10 is hex = 16 → 24. A leading 0 means octal.
2. **c)** `10 6`: x++ uses 5, then increments. 5*2 = 10, then x = 6.
3. **a)** `6 12`: Pre-increment: x becomes 6 first, then 6*2 = 12.
4. **b)** `1`: The remainder takes the sign of the dividend (7) → 1.
5. **b)** `-1`: Sign of the dividend (-7) → -1.
6. **b)** `16`: + before <<: 1 << 4 = 16.
7. **b)** `0`: Logical NOT of any non-zero value is 0.
8. **c)** `1`: !5 = 0, !0 = 1. !! normalises any value to 0 or 1.
9. **c)** `-1`: ~0 = all 32 bits set = 0xFFFFFFFF. Printed as signed → -1.
10. **c)** `4294967295`: All bits set as unsigned = UINT_MAX.
11. **a)** `1`: 101 & 011 = 001 = 1.
12. **c)** `7`: 101 | 011 = 111 = 7.
13. **b)** `6`: ^ is XOR, not power: 101 ^ 011 = 110 = 6.
14. **b)** `20`: a = a + (5*2) = 20.
15. **b)** `15`: x *= (2+3) → 3 * 5 = 15. The whole right side is evaluated first.
16. **b)** `0`: (10 > 5) = 1, then 1 > 2 = 0.
17. **c)** `0`: (3 == 3) = 1, then 1 == 3 = 0.
18. **b)** F: = is assignment (legal, just a warning). a becomes 0 → false → F.
19. **c)** UB: i is modified twice (by = and ++) with no sequence point → UB. It still compiles.
20. **b)** `0`: sizeof returns size_t (unsigned). -1 converts to SIZE_MAX, so 4 > SIZE_MAX is false → 0.
21. **b)** pos: u + s is unsigned: -10 → 4294967286 > 0 → pos.
22. **c)** Infinite loop: An unsigned value is always >= 0. After 0, i-- wraps to 4294967295 → the loop never ends.
23. **b)** C: 'A' = 65, + 2 = 67 = 'C'.
24. **b)** `32`: 97 - 65 = 32. That is why c ^ 0x20 toggles letter case.
25. **b)** A: %c prints the ASCII character 65 = 'A'.
26. **a)** `3`: 7 / 2.0 = 3.5 (double), stored in int → truncated to 3.
27. **c)** `UB (garbage)`: 7/2 is an int, but %f expects a double. A format/type mismatch is UB; printf does not convert for you.
28. **b)** `3`: Casting to int truncates toward zero; no rounding.
29. **b)** `-3`: Truncation is toward zero → -3.
30. **b)** `8`: A floating literal without a suffix is a double → 8.
31. **b)** `4`: The f suffix makes it a float → 4.
32. **a)** 1 always, by definition: The C standard defines sizeof(char) == 1.
33. **b)** `10`: 5 > 10 is false → b = 10.
34. **b)** `10`: ?: has lower precedence than +, so the else branch is (20+5). The condition is true → 10.
35. **b)** `1`: && before ||: (1 && 0) || 1 = 1. No side effects, so no UB.
36. **c)** `2`: Each comma operator is a sequence point → defined. a goes 0 → 1 → 2; the last value is 2.
37. **d)** UB: Commas between function arguments are NOT sequence points. x is read and modified unsequenced → UB.
38. **c)** `4 5`: a is modified once → defined. b = 3 + 2 = 5, then a = 4.
39. **b)** `255`: -1 mod 256 = 255 (all 8 bits set).
40. **b)** `-56`: ≥ 128 → value - 256 = -56.
41. **b)** `-25536`: short is 16-bit (max 32767). 40000 - 65536 = -25536.
42. **c)** `2147483648`: 1u makes the addition unsigned → no signed overflow. 0x80000000 = 2147483648.
43. **a)** `-1`: Right-shifting a negative value is implementation-defined; GCC copies the sign bit (arithmetic shift) → stays -1.
44. **b)** `268435455`: An unsigned right shift fills with 0s: 0x0FFFFFFF = 268435455.
45. **b)** `x << 32`: Shifting by ≥ the bit width is UB.
46. **a)** `10`: (10/3)*3 = 9, plus 10%3 = 1 → 10. Identity: (a/b)*b + a%b == a.
47. **b)** `0`: f has float precision; promoted to double it differs from d → 0.
48. **a)** Yes: 0.5 = 2^-1 is exact in float and double → Yes.
49. **b)** `5`: The sizeof operand is not executed → 5.
50. **b)** `0`: == first: b == 3 → 1. Then 2 & 1 = 0.

<a id="quiz-4"></a>
## Quiz 4: Mega test 2, weak areas (50 questions)

1. **b)** `44`: 300 mod 256 = 44. An unsigned value can never be negative.
2. **a)** `-126`: 130 - 256 = -126.
3. **c)** `127`: Below -128 → add 256: 127.
4. **d)** `246`: 256 - 10 = 246.
5. **b)** `-32768`: 32768 - 65536 = -32768.
6. **c)** `65535`: unsigned short is 16 bits → all 16 bits set = 65535.
7. **d)** `-128`: c + 1 = 128 as int (no overflow), stored back → 128 - 256 = -128.
8. **a)** `255`: -1 stored in unsigned char → 255.
9. **b)** B: sizeof is size_t (unsigned). n converts to a huge value → false → B.
10. **c)** `4294967294`: 2^32 - 2 = 4294967294.
11. **b)** `-1`: 1111 1111 as signed → -1.
12. **d)** `254`: 256 - 2 = 254.
13. **a)** `-4`: Arithmetic shift copies the sign bit → -4.
14. **b)** `2147483644`: -8 → 4294967288, then >> 1 = divide by 2 → 2147483644.
15. **c)** `0`: Unsigned wraps → 0. Defined.
16. **c)** T: Legal: assigns 10 (non-zero) → T.
17. **d)** UB: x modified by = and ++ without a sequence point → UB.
18. **c)** UB: x modified twice in one expression → UB.
19. **d)** UB: Argument commas are not sequence points → UB.
20. **a)** `2 1`: && is a sequence point → defined. a++ is 1 (true), then a = 2 (true) → b = 1.
21. **c)** UB: Signed overflow is UB.
22. **b)** `x = 0, defined`: Unsigned arithmetic always wraps → 0.
23. **d)** UB: A double passed to %d → format mismatch → UB.
24. **a)** `3 5`: The compiler takes the longest token first: a++ + b = 2 + 3 = 5, then a = 3.
25. **c)** Compile error: Assigning to a const variable is a compile error.
26. **b)** UB: Shifting a 1 into the sign bit of a signed int is UB in C. Use 1u << 31.
27. **d)** UB: i is read (index) and modified (++) unsequenced → UB.
28. **c)** UB: x is modified and read in the same expression with no sequence point → UB.
29. **c)** `int x = 5;;`: The extra ; is an empty statement. 2x is a bad name, 08 is an invalid octal digit, == is a syntax error in a declaration.
30. **b)** A: The ; after the if is an empty body; printf is outside the if → always A.
31. **c)** `2 6`: x-- returns the old value 3 → y = 6, then x = 2.
32. **a)** `2 4`: --x changes first → 2, y = 4.
33. **b)** `12`: Comma operator is a sequence point: a = 6, then a*2 = 12.
34. **c)** `3 9`: a = 3, then the last operand a*3 = 9.
35. **a)** `5`: = binds tighter than the comma: (x = 5), 10; → 5.
36. **d)** `1 20`: x++ returns 0 (false) → 20; x becomes 1. ?: is a sequence point.
37. **b)** `4`: Checks 0<3, 1<3, 2<3, 3<3 (false) — ++ runs every time → 4.
38. **b)** `-2`: x = 4 - 6 = -2.
39. **b)** `2`: x = 10 / 5 = 2.
40. **c)** `8`: x = 2 << 2 = 8.
41. **a)** `10`: The else branch is (2*5) → 10.
42. **c)** `1`: a is true → 1.
43. **c)** `1`: x > 3 ? (x < 10 ? 1 : 2) : 3 → 1.
44. **b)** `15`: Octal 017 = 1*8 + 7 = 15.
45. **a)** `39`: 31 + 8 = 39.
46. **c)** `8`: L = long = 8 bytes on LP64.
47. **d)** `4`: float * int → float → 4.
48. **b)** `8`: 2.0 is a double → float * double → double → 8.
49. **c)** Yes: 0.25 = 2^-2 is exact → Yes.
50. **b)** No: 0.1, 0.2, 0.3 are not exact in binary → 0.30000000000000004 ≠ 0.3 → No. Compare floats with fabs(a - b) < eps.

<a id="quiz-5"></a>
## Quiz 5: UB and bit-pattern drill (15 questions)

1. **c)** UB: x has ++ and appears again with no separator → UB.
2. **b)** `5`: Separate statements → defined. y = 2, x = 3, then y = 5.
3. **c)** UB: i has ++ (index) and is read on the right → UB.
4. **a)** `1 2`: No side effect on i → reading it twice is fine.
5. **b)** UB: int passed for %f → format mismatch → UB.
6. **c)** `3`: The cast makes it an int first → matches %d → 3.
7. **d)** `0`: Unsigned wraps → 0.
8. **c)** Compile error: A leading 0 means octal and octal digits are only 0–7 → compile error.
9. **b)** `2147483648`: 1u is unsigned → fine → 0x80000000.
10. **c)** Only x + 1: Signed overflow = UB; unsigned wraps = defined.
11. **c)** `1073741820`: -16 → 4294967280; >> 2 = divide by 4 → 1073741820.
12. **a)** `7`: 263 mod 256 = 7.
13. **d)** `4464`: 70000 - 65536 = 4464.
14. **c)** B: size_t is unsigned → -5 becomes huge → false → B.
15. **b)** `3`: 0<2 (a=1), 1<2 (a=2), 2<2 false but ++ still runs (a=3) → 3.


<a id="practice-solutions"></a>
## Practice solutions

### 1. Sizes and ranges
```c
#include <stdio.h>
#include <limits.h>
int main(void) {
    printf("char      %zu  %d .. %d\n", sizeof(char), CHAR_MIN, CHAR_MAX);
    printf("short     %zu  %d .. %d\n", sizeof(short), SHRT_MIN, SHRT_MAX);
    printf("int       %zu  %d .. %d\n", sizeof(int), INT_MIN, INT_MAX);
    printf("long      %zu  %ld .. %ld\n", sizeof(long), LONG_MIN, LONG_MAX);
    printf("long long %zu  %lld .. %lld\n", sizeof(long long), LLONG_MIN, LLONG_MAX);
    printf("unsigned  %zu  0 .. %u\n", sizeof(unsigned), UINT_MAX);
    return 0;
}
```

### 2. Swap without a temp
```c
#include <stdio.h>
void swap_add(int *a, int *b) { if (a == b) return; *a = *a + *b; *b = *a - *b; *a = *a - *b; }
void swap_xor(int *a, int *b) { if (a == b) return; *a ^= *b; *b ^= *a; *a ^= *b; }
int main(void) {
    int x = 3, y = 5;
    swap_add(&x, &y); printf("%d %d\n", x, y);   // 5 3
    swap_xor(&x, &y); printf("%d %d\n", x, y);   // 3 5
    return 0;
}
```
When they break:
- **`+`/`-`**: `*a + *b` can overflow, and signed overflow is UB.
- **XOR**: if `a` and `b` point to the **same** variable, `x ^= x` gives 0 and the value is lost. That's why both versions check `a == b`.

### 3. Overflow-safe add
```c
#include <stdio.h>
#include <limits.h>
int safe_add(int a, int b, int *res) {
    if ((b > 0 && a > INT_MAX - b) || (b < 0 && a < INT_MIN - b))
        return -1;                     // would overflow: never do the add
    *res = a + b;
    return 0;
}
int main(void) {
    int r;
    printf("%d\n", safe_add(INT_MAX, 1, &r));          // -1
    if (safe_add(10, 20, &r) == 0) printf("%d\n", r);  // 30
    return 0;
}
```

### 4. Reverse digits with overflow check
```c
#include <stdio.h>
#include <limits.h>
int reverse(int x) {
    int r = 0;
    while (x != 0) {
        int d = x % 10;                       // keeps the sign: -123 % 10 = -3
        if (r > INT_MAX / 10 || (r == INT_MAX / 10 && d > 7)) return 0;
        if (r < INT_MIN / 10 || (r == INT_MIN / 10 && d < -8)) return 0;
        r = r * 10 + d;
        x /= 10;
    }
    return r;
}
int main(void) {
    printf("%d %d %d\n", reverse(123), reverse(-450), reverse(1534236469)); // 321 -54 0
    return 0;
}
```

### 5. Is plain char signed?
```c
#include <stdio.h>
int main(void) {
    char c = (char)0xFF;                     // all bits set
    printf("plain char is %s\n", c < 0 ? "signed" : "unsigned");
    return 0;
}
```

### 6. Why `uint32_t` in firmware?
`int` changes size between platforms (16-bit on some MCUs, 32-bit on ARM). Register widths are fixed, so `uint32_t` guarantees exactly 32 bits. It's unsigned, so shifts and masks are well-defined, and it documents the intent.

### 7. Implementation-defined vs undefined
- **Implementation-defined:** the compiler must pick one behaviour and document it. Examples: the signedness of `char`, and `-1 >> 1`.
- **Undefined:** the standard makes no promise at all, and the optimiser may assume it never happens. Examples: signed overflow, `i = i++`, out-of-bounds access.
