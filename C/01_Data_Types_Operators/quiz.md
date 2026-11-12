# Quiz: Data Types and Operators

[← Back to notes](README.md)

> Assume 32-bit `int`, GCC on x86-64 Linux, unless a question says otherwise.

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz 1: Warm-up (12 questions)


**Q1.**

```c
unsigned int a = 1; int b = -1;
if (b < a) printf("A"); else printf("B");
```

- a) A
- b) B
- c) Compile error
- d) Undefined behaviour


**Q2.**

On 64-bit Linux (LP64), what are sizeof(int), sizeof(long), sizeof(void\*)?

- a) `4, 4, 8`
- b) `4, 8, 8`
- c) `8, 8, 8`
- d) `4, 4, 4`


**Q3.**

```c
char c = 255;
printf("%d", c);
```

- a) 255 always
- b) -1 always
- c) -1 on x86 GCC, 255 on ARM GCC
- d) Compile error


**Q4.**

```c
int i = 5;
printf("%d", i++ + ++i);
```

- a) `11`
- b) `12`
- c) `13`
- d) Undefined behaviour


**Q5.**

```c
int i = 10;
int s = sizeof(i++);
printf("%d %d", s, i);
```

- a) `4 11`
- b) `4 10`
- c) `10 11`
- d) Compile error


**Q6.**

```c
int x = 2;
int y = x << 1 + 1;
printf("%d", y);
```

- a) `5`
- b) `8`
- c) `4`
- d) `6`


**Q7.**

```c
unsigned char a = 200, b = 100;
int c = a + b;
unsigned char d = a + b;
printf("%d %d", c, d);
```

- a) `300 300`
- b) `44 44`
- c) `300 44`
- d) Undefined behaviour


**Q8.**

```c
printf("%.1f", 5 / 2 * 2.0);
```

- a) `5.0`
- b) `4.0`
- c) `4`
- d) `2.0`


**Q9.**

```c
int a = 0, b = 0;
if (a++ && b++) { }
printf("%d %d", a, b);
```

- a) `1 1`
- b) `0 0`
- c) `1 0`
- d) `0 1`


**Q10.**

Which expression is WELL-DEFINED in C (32-bit int)?

- a) INT_MAX + 1
- b) UINT_MAX + 1u
- c) `1 << 31`
- d) `-INT_MIN`


**Q11.**

```c
int x;
x = (5, 10, 15);
printf("%d", x);
```

- a) `5`
- b) `15`
- c) `30`
- d) Compile error


**Q12.**

```c
In C (not C++), printf("%zu", sizeof('A')); prints?
```

- a) `1`
- b) `4`
- c) `2`
- d) `8`


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---

## Quiz 2: Retest (10 questions)


**Q1.**

```c
int a = -5; unsigned int b = 3;
printf("%d", a + b > 0);
```

- a) `0`
- b) `1`
- c) `-2`
- d) UB


**Q2.**

```c
unsigned char x = 250;
x = x + 10;
printf("%d", x);
```

- a) `260`
- b) `4`
- c) `-6`
- d) `255`


**Q3.**

```c
short s = 100;
printf("%zu", sizeof(s + s));
```

- a) `2`
- b) `4`
- c) `8`
- d) `200`


**Q4.**

```c
int i = 3;
int j = sizeof(++i + 10);
printf("%d", i);
```

- a) `3`
- b) `4`
- c) `14`
- d) UB


**Q5.**

```c
int x = 1;
if (x & 2 == 2) printf("Y"); else printf("N");
```

- a) Y
- b) N
- c) Compile error
- d) UB


**Q6.**

```c
int a = 1, b = 0;
if (a || b++) { }
printf("%d", b);
```

- a) `0`
- b) `1`
- c) UB
- d) `2`


**Q7.**

Which one is undefined behaviour?

- a) `i = i + 1;`
- b) `a[i] = i++;`
- c) `i++, i++;`
- d) `x = (i++ > 0) && i;`


**Q8.**

```c
signed char c = 0x80;
printf("%d", c);
```

- a) `128`
- b) `-128`
- c) `0`
- d) `-127`


**Q9.**

```c
float f = 0.1;
if (f == 0.1) printf("Yes"); else printf("No");
```

- a) Yes
- b) No
- c) Compile error
- d) UB


**Q10.**

```c
printf("%d %d", -7 / 2, -7 % 2);
```

- a) -4 1
- b) -3 -1
- c) -3 1
- d) -4 -1


**[Answer sheet for this quiz →](answers.md#quiz-2)**

---

## Quiz 3: Mega test 1 (50 questions)


**Q1.**

```c
printf("%d", 010 + 0x10);
```

- a) `20`
- b) `24`
- c) `26`
- d) `18`


**Q2.**

```c
int x = 5;
printf("%d ", x++ * 2);
printf("%d", x);
```

- a) `12 6`
- b) `10 5`
- c) `10 6`
- d) `12 5`


**Q3.**

```c
int x = 5;
int y = ++x * 2;
printf("%d %d", x, y);
```

- a) `6 12`
- b) `5 10`
- c) `6 10`
- d) `5 12`


**Q4.**

```c
printf("%d", 7 % -3);
```

- a) `-1`
- b) `1`
- c) `-2`
- d) `2`


**Q5.**

```c
printf("%d", -7 % -3);
```

- a) `1`
- b) `-1`
- c) `2`
- d) `-2`


**Q6.**

```c
printf("%d", 1 << 3 + 1);
```

- a) `9`
- b) `16`
- c) `8`
- d) `5`


**Q7.**

```c
printf("%d", !5);
```

- a) `-5`
- b) `0`
- c) `1`
- d) `-6`


**Q8.**

```c
printf("%d", !!5);
```

- a) `5`
- b) `0`
- c) `1`
- d) `-5`


**Q9.**

```c
printf("%d", ~0);
```

- a) `0`
- b) `1`
- c) `-1`
- d) `4294967295`


**Q10.**

```c
printf("%u", ~0u);
```

- a) `-1`
- b) `0`
- c) `4294967295`
- d) `2147483647`


**Q11.**

```c
printf("%d", 5 & 3);
```

- a) `1`
- b) `7`
- c) `6`
- d) `0`


**Q12.**

```c
printf("%d", 5 | 3);
```

- a) `8`
- b) `1`
- c) `7`
- d) `6`


**Q13.**

```c
printf("%d", 5 ^ 3);
```

- a) `125`
- b) `6`
- c) `7`
- d) `2`


**Q14.**

```c
int a = 10;
a += 5 * 2;
printf("%d", a);
```

- a) `30`
- b) `20`
- c) `25`
- d) `15`


**Q15.**

```c
int x = 3;
x *= 2 + 3;
printf("%d", x);
```

- a) `9`
- b) `15`
- c) `11`
- d) `6`


**Q16.**

```c
printf("%d", 10 > 5 > 2);
```

- a) `1`
- b) `0`
- c) Compile error
- d) UB


**Q17.**

```c
printf("%d", 3 == 3 == 3);
```

- a) `1`
- b) `3`
- c) `0`
- d) Compile error


**Q18.**

```c
int a = 5;
if (a = 0) printf("T"); else printf("F");
```

- a) T
- b) F
- c) Compile error
- d) UB


**Q19.**

```c
int i = 0;
i = i++;
printf("%d", i);
```

- a) `0`
- b) `1`
- c) UB
- d) Compile error


**Q20.**

```c
printf("%d", sizeof(int) > -1);
```

- a) `1`
- b) `0`
- c) `4`
- d) UB


**Q21.**

```c
unsigned int u = 10; int s = -20;
printf("%s", (u + s > 0) ? "pos" : "neg");
```

- a) neg
- b) pos
- c) UB
- d) Compile error


**Q22.**

```c
unsigned int i;
for (i = 5; i >= 0; i--) printf("%u ", i);
```

- a) Prints 5 4 3 2 1 0
- b) Prints 5 4 3 2 1
- c) Infinite loop
- d) Compile error


**Q23.**

```c
char c = 'A' + 2;
printf("%c", c);
```

- a) A2
- b) C
- c) `67`
- d) B


**Q24.**

```c
printf("%d", 'a' - 'A');
```

- a) `26`
- b) `32`
- c) `-32`
- d) `0`


**Q25.**

```c
printf("%c", 65);
```

- a) `65`
- b) A
- c) a
- d) UB


**Q26.**

```c
int x = 7 / 2.0;
printf("%d", x);
```

- a) `3`
- b) `4`
- c) `3.5`
- d) UB


**Q27.**

```c
printf("%f", 7 / 2);
```

- a) `3.000000`
- b) `3.500000`
- c) `UB (garbage)`
- d) Compile error


**Q28.**

```c
printf("%d", (int)3.99);
```

- a) `4`
- b) `3`
- c) `3.99`
- d) UB


**Q29.**

```c
printf("%d", (int)-3.99);
```

- a) `-4`
- b) `-3`
- c) `3`
- d) UB


**Q30.**

```c
printf("%zu", sizeof(3.14));
```

- a) `4`
- b) `8`
- c) `3`
- d) `16`


**Q31.**

```c
printf("%zu", sizeof(3.14f));
```

- a) `8`
- b) `4`
- c) `2`
- d) `1`


**Q32.**

```c
sizeof(char) is…
```

- a) 1 always, by definition
- b) Depends on the platform
- c) 2 on Windows
- d) 8 bits, so 8


**Q33.**

```c
int a = 5, b = 10;
printf("%d", a > b ? a : b);
```

- a) `5`
- b) `10`
- c) `0`
- d) `1`


**Q34.**

```c
int x = 1;
int y = (x == 1) ? 10 : 20 + 5;
printf("%d", y);
```

- a) `15`
- b) `10`
- c) `25`
- d) `30`


**Q35.**

```c
printf("%d", 5 > 3 && 2 > 4 || 1);
```

- a) `0`
- b) `1`
- c) `5`
- d) UB


**Q36.**

```c
int a = 0;
int b = (a++, a++, a);
printf("%d", b);
```

- a) `0`
- b) `1`
- c) `2`
- d) UB


**Q37.**

```c
int x = 10;
printf("%d %d", x, x++);
```

- a) `10 10`
- b) `11 10`
- c) `10 11`
- d) UB


**Q38.**

```c
int a = 3;
int b = a++ + 2;
printf("%d %d", a, b);
```

- a) `4 6`
- b) `3 5`
- c) `4 5`
- d) UB


**Q39.**

```c
unsigned char c = -1;
printf("%d", c);
```

- a) `-1`
- b) `255`
- c) `0`
- d) `1`


**Q40.**

```c
signed char c = 200;
printf("%d", c);
```

- a) `200`
- b) `-56`
- c) `-55`
- d) `56`


**Q41.**

```c
short s = 40000;
printf("%d", s);
```

- a) `40000`
- b) `-25536`
- c) `25536`
- d) `-40000`


**Q42.**

```c
int x = 0x7FFFFFFF;
unsigned int y = x + 1u;
printf("%u", y);
```

- a) UB
- b) `0`
- c) `2147483648`
- d) `-2147483648`


**Q43.**

```c
printf("%d", -1 >> 1); // GCC
```

- a) `-1`
- b) `2147483647`
- c) `0`
- d) UB


**Q44.**

```c
printf("%u", 0xFFFFFFFFu >> 4);
```

- a) `4294967295`
- b) `268435455`
- c) `-1`
- d) `16`


**Q45.**

For a 32-bit int x, which one is UB?

- a) `x << 31u (x unsigned)`
- b) `x << 32`
- c) `x >> 1 (x positive)`
- d) `x & 0xFF`


**Q46.**

```c
printf("%d", 10 / 3 * 3 + 10 % 3);
```

- a) `10`
- b) `9`
- c) `11`
- d) `13`


**Q47.**

```c
float f = 1.0f / 3;
double d = 1.0 / 3;
printf("%d", f == d);
```

- a) `1`
- b) `0`
- c) UB
- d) Compile error


**Q48.**

```c
if (0.5 == 0.5f) printf("Yes"); else printf("No");
```

- a) Yes
- b) No
- c) UB
- d) Compile error


**Q49.**

```c
int x = 5;
int s = sizeof(x = 10);
printf("%d", x);
```

- a) `10`
- b) `5`
- c) `4`
- d) UB


**Q50.**

```c
int a = 2, b = 3;
printf("%d", a & b == 3);
```

- a) `2`
- b) `0`
- c) `1`
- d) `3`


**[Answer sheet for this quiz →](answers.md#quiz-3)**

---

## Quiz 4: Mega test 2, weak areas (50 questions)


**Q1.**

```c
unsigned char c = 300;
printf("%d", c);
```

- a) `300`
- b) `44`
- c) `255`
- d) `-44`


**Q2.**

```c
signed char c = 130;
printf("%d", c);
```

- a) `-126`
- b) `130`
- c) `-130`
- d) `126`


**Q3.**

```c
signed char c = -129;
printf("%d", c);
```

- a) `-129`
- b) `-128`
- c) `127`
- d) `-1`


**Q4.**

```c
unsigned char c = -10;
printf("%d", c);
```

- a) `-10`
- b) `10`
- c) `245`
- d) `246`


**Q5.**

```c
short s = 32768;
printf("%d", s);
```

- a) `32768`
- b) `-32768`
- c) `32767`
- d) `0`


**Q6.**

```c
unsigned short us = -1;
printf("%u", us);
```

- a) `4294967295`
- b) `-1`
- c) `65535`
- d) `32767`


**Q7.**

```c
signed char c = 127;
c = c + 1;
printf("%d", c);
```

- a) `128`
- b) `0`
- c) UB
- d) `-128`


**Q8.**

```c
unsigned char c = 0;
c = c - 1;
printf("%d", c);
```

- a) `255`
- b) `-1`
- c) `0`
- d) UB


**Q9.**

```c
int n = -1;
if (n < sizeof(int)) printf("A"); else printf("B");
```

- a) A
- b) B
- c) UB
- d) Compile error


**Q10.**

```c
unsigned int u = -2;
printf("%u", u);
```

- a) `-2`
- b) `2`
- c) `4294967294`
- d) `4294967295`


**Q11.**

```c
printf("%d", (signed char)0xFF);
```

- a) `255`
- b) `-1`
- c) `-255`
- d) `0`


**Q12.**

```c
printf("%d", (unsigned char)-2);
```

- a) `-2`
- b) `253`
- c) `2`
- d) `254`


**Q13.**

```c
printf("%d", -8 >> 1); // GCC
```

- a) `-4`
- b) `4`
- c) `2147483644`
- d) UB


**Q14.**

```c
printf("%u", (unsigned)-8 >> 1);
```

- a) `-4`
- b) `2147483644`
- c) `4294967292`
- d) `4`


**Q15.**

```c
unsigned int a = 0xFFFFFFFF;
a = a + 1;
printf("%u", a);
```

- a) `4294967296`
- b) UB
- c) `0`
- d) `1`


**Q16.**

```c
int a = 5;
if (a = 10) printf("T"); else printf("F");
```

- a) F
- b) Compile error
- c) T
- d) UB


**Q17.**

```c
int x = 5;
x = x++ + 1;
printf("%d", x);
```

- a) `6`
- b) `7`
- c) Compile error
- d) UB


**Q18.**

```c
int x = 5;
int y = x++ + x++;
```

- a) `y = 10`
- b) `y = 11`
- c) UB
- d) Compile error


**Q19.**

```c
int i = 1;
printf("%d %d", i++, i++);
```

- a) `1 2`
- b) `2 1`
- c) Compile error
- d) UB


**Q20.**

```c
int a = 1;
int b = a++ && a;
printf("%d %d", a, b);
```

- a) `2 1`
- b) UB
- c) `1 1`
- d) `2 0`


**Q21.**

```c
int x = INT_MAX;
x = x + 1;
```

- a) `x = INT_MIN, defined`
- b) `x = 0`
- c) UB
- d) Compile error


**Q22.**

```c
unsigned int x = UINT_MAX;
x = x + 1;
```

- a) UB
- b) `x = 0, defined`
- c) Compile error
- d) `x = UINT_MAX`


**Q23.**

```c
printf("%d", 3.5);
```

- a) `3`
- b) `4`
- c) Compile error
- d) UB


**Q24.**

```c
int a = 2, b = 3;
int c = a+++b;
printf("%d %d", a, c);
```

- a) `3 5`
- b) `2 6`
- c) Compile error
- d) UB


**Q25.**

```c
const int x = 5;
x = 10;
```

- a) x becomes 10
- b) UB
- c) Compile error
- d) x stays 5 silently


**Q26.**

```c
int x = 1 << 31;  // 32-bit signed int, C
```

- a) `x = 2147483648`
- b) UB
- c) Compile error
- d) `x = 0`


**Q27.**

```c
int arr[3] = {0};
int i = 0;
arr[i] = i++;
```

- a) `arr[0] = 0`
- b) `arr[1] = 0`
- c) Compile error
- d) UB


**Q28.**

```c
int x = 5;
int y = (x = 3) + x;
```

- a) `y = 6`
- b) `y = 8`
- c) UB
- d) Compile error


**Q29.**

Which line compiles WITHOUT error?

- a) `int 2x = 5;`
- b) `int x = 08;`
- c) `int x = 5;;`
- d) `int x == 5;`


**Q30.**

```c
int x = 5;
if (x == 10);
printf("A");
```

- a) Nothing printed
- b) A
- c) Compile error
- d) UB


**Q31.**

```c
int x = 3;
int y = x-- * 2;
printf("%d %d", x, y);
```

- a) `2 4`
- b) `3 6`
- c) `2 6`
- d) `3 4`


**Q32.**

```c
int x = 3;
int y = --x * 2;
printf("%d %d", x, y);
```

- a) `2 4`
- b) `2 6`
- c) `3 6`
- d) `3 4`


**Q33.**

```c
int a = 5;
int b = (a++, a * 2);
printf("%d", b);
```

- a) `10`
- b) `12`
- c) `5`
- d) UB


**Q34.**

```c
int a = 1, b;
b = (a += 2, a * 3);
printf("%d %d", a, b);
```

- a) `1 3`
- b) `3 3`
- c) `3 9`
- d) UB


**Q35.**

```c
int x;
x = 5, 10;
printf("%d", x);
```

- a) `5`
- b) `10`
- c) `15`
- d) Compile error


**Q36.**

```c
int x = 0;
int y = x++ ? 10 : 20;
printf("%d %d", x, y);
```

- a) `0 20`
- b) `1 10`
- c) UB
- d) `1 20`


**Q37.**

```c
int a = 0;
while (a++ < 3);
printf("%d", a);
```

- a) `3`
- b) `4`
- c) `2`
- d) Infinite loop


**Q38.**

```c
int x = 4;
x -= 2 * 3;
printf("%d", x);
```

- a) `6`
- b) `-2`
- c) `2`
- d) `-6`


**Q39.**

```c
int x = 10;
x /= 2 + 3;
printf("%d", x);
```

- a) `8`
- b) `2`
- c) `5`
- d) `3`


**Q40.**

```c
int x = 2;
x <<= 1 + 1;
printf("%d", x);
```

- a) `5`
- b) `4`
- c) `8`
- d) `16`


**Q41.**

```c
int a = 0;
int y = a ? 1 : 2 * 5;
printf("%d", y);
```

- a) `10`
- b) `1`
- c) `2`
- d) `5`


**Q42.**

```c
int a = 1;
int y = a ? 1 : 2 * 5;
printf("%d", y);
```

- a) `10`
- b) `5`
- c) `1`
- d) `2`


**Q43.**

```c
int x = 5;
int y = x > 3 ? x < 10 ? 1 : 2 : 3;
printf("%d", y);
```

- a) `3`
- b) `2`
- c) `1`
- d) Compile error


**Q44.**

```c
printf("%d", 017);
```

- a) `17`
- b) `15`
- c) `23`
- d) `7`


**Q45.**

```c
printf("%d", 0x1F + 010);
```

- a) `39`
- b) `41`
- c) `29`
- d) `47`


**Q46.**

```c
On 64-bit Linux: printf("%zu", sizeof(10L));
```

- a) `4`
- b) `2`
- c) `8`
- d) `10`


**Q47.**

```c
printf("%zu", sizeof(1.0f * 2));
```

- a) `8`
- b) `2`
- c) `1`
- d) `4`


**Q48.**

```c
printf("%zu", sizeof(1.0f * 2.0));
```

- a) `4`
- b) `8`
- c) `12`
- d) `16`


**Q49.**

```c
if (0.25 == 0.25f) printf("Yes"); else printf("No");
```

- a) No
- b) UB
- c) Yes
- d) Compile error


**Q50.**

```c
if (0.1 + 0.2 == 0.3) printf("Yes"); else printf("No");
```

- a) Yes
- b) No
- c) UB
- d) Compile error


**[Answer sheet for this quiz →](answers.md#quiz-4)**

---

## Quiz 5: UB and bit-pattern drill (15 questions)


**Q1.**

```c
int x = 2;
int y = x + x++;
```

- a) `y = 4`
- b) `y = 5`
- c) UB
- d) Compile error


**Q2.**

```c
int x = 2;
int y = x++;
y += x;
printf("%d", y);
```

- a) UB
- b) `5`
- c) `4`
- d) `6`


**Q3.**

```c
int a[5], i = 0;
a[i++] = i;
```

- a) `a[0] = 1`
- b) `a[0] = 0`
- c) UB
- d) Compile error


**Q4.**

```c
int i = 1;
printf("%d %d", i, i + 1);
```

- a) `1 2`
- b) UB
- c) `2 1`
- d) `1 1`


**Q5.**

```c
printf("%f", 3);
```

- a) `3.000000`
- b) UB
- c) Compile error
- d) 0.000000 always


**Q6.**

```c
printf("%d", (int)3.5);
```

- a) UB
- b) `4`
- c) `3`
- d) Compile error


**Q7.**

```c
unsigned char c = 255;
c++;
printf("%d", c);
```

- a) `256`
- b) UB
- c) `-1`
- d) `0`


**Q8.**

```c
int x = 09;
```

- a) `x = 9`
- b) `x = 0`
- c) Compile error
- d) UB


**Q9.**

```c
unsigned int x = 1u << 31;
printf("%u", x);
```

- a) UB
- b) `2147483648`
- c) `-2147483648`
- d) `0`


**Q10.**

```c
int x = INT_MAX;
unsigned int y = UINT_MAX;
Which is UB: x + 1 or y + 1?
```

- a) Both
- b) Only y + 1
- c) Only x + 1
- d) Neither


**Q11.**

```c
printf("%u", (unsigned)-16 >> 2);
```

- a) `-4`
- b) `4294967276`
- c) `1073741820`
- d) `1073741824`


**Q12.**

```c
unsigned char c = 263;
printf("%d", c);
```

- a) `7`
- b) `263`
- c) `-7`
- d) `255`


**Q13.**

```c
unsigned short s = 70000;
printf("%d", s);
```

- a) `70000`
- b) `-4464`
- c) `65535`
- d) `4464`


**Q14.**

```c
if (-5 < sizeof(char)) printf("A"); else printf("B");
```

- a) A
- b) UB
- c) B
- d) Compile error


**Q15.**

```c
int a = 0;
while (a++ < 2);
printf("%d", a);
```

- a) `2`
- b) `3`
- c) Infinite loop
- d) `1`


**[Answer sheet for this quiz →](answers.md#quiz-5)**

---
