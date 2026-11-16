# Quiz: Camera ISP

[← Back to notes](README.md)

Answer every question before you check the answer sheet. Pass mark: **80%**.


## Quiz: Camera ISP, from light to NV12 (20 questions)


**Q1.**

What does a single photodiode measure?

- a) The colour of the light
- b) How much light arrived
- c) Red, green and blue separately
- d) The brightness and the colour together


**Q2.**

Why does each pixel of a colour sensor give only one colour?

- a) The ADC can convert only one colour at a time
- b) The MIPI link carries only one colour per packet
- c) A single colour filter sits on top of each photodiode
- d) The ISP removes the other two colours


**Q3.**

In the Bayer pattern, each 2×2 group contains:

- a) 1 red, 1 green, 2 blue
- b) 2 red, 1 green, 1 blue
- c) 1 red, 2 green, 1 blue
- d) 1 red, 1 green, 1 blue, 1 white


**Q4.**

Why are there two green pixels in every Bayer group?

- a) Green filters are cheaper to make
- b) The human eye is most sensitive to green
- c) Green light carries more energy
- d) It makes the sensor square


**Q5.**

In RAW10, each pixel value lies in the range:

- a) 0 to 255
- b) 0 to 1023
- c) 0 to 4095
- d) 0 to 10


**Q6.**

Why do ISPs usually take RAW10 or RAW12 input instead of RAW8?

- a) RAW8 cannot carry colour
- b) MIPI CSI-2 does not support 8 bits
- c) Later stages multiply the values, and too few levels cause visible banding
- d) RAW8 needs more memory


**Q7.**

In "MIPI CSI-2", what does "serial" mean?

- a) Each pixel has a serial number
- b) Bits travel one after another on a few wires
- c) Only one camera can be connected
- d) Frames are sent one per second


**Q8.**

What is the checksum at the end of a MIPI CSI-2 packet for?

- a) To compress the pixel data
- b) To tell the receiver which camera sent the packet
- c) To let the receiver detect damaged pixel data
- d) To mark the end of the frame


**Q9.**

Which fault does black level correction fix?

- a) Corners darker than the centre
- b) Pixels reading a small non-zero value in total darkness
- c) A few pixels stuck at full brightness
- d) An orange tint from indoor lighting


**Q10.**

A defective pixel is replaced using:

- a) The nearest green pixel
- b) The value from the previous frame
- c) Nearby pixels of the same colour
- d) Zero


**Q11.**

What does demosaic do?

- a) Removes noise from the picture
- b) Converts RGB to YUV
- c) Fills in the two missing colours at every pixel using neighbours
- d) Corrects the lens shading


**Q12.**

Straight after demosaic, the data is:

- a) Bayer, one number per pixel
- b) RGB, three numbers per pixel
- c) NV12
- d) YUV 4:2:0


**Q13.**

White balance exists because:

- a) The lens darkens the corners
- b) The sensor's colour filters overlap
- c) The colour of the light source tints the whole picture
- d) The sensor output is linear


**Q14.**

Without gamma correction, the picture looks:

- a) Much too dark
- b) Green
- c) Grainy
- d) Blurred


**Q15.**

In YUV, the Y component alone gives:

- a) A full colour picture
- b) Only the blue part of the picture
- c) A black-and-white picture
- d) Nothing viewable


**Q16.**

In 4:2:0, one U, V pair is shared by:

- a) 1 pixel
- b) 2 pixels
- c) 4 pixels
- d) The whole line


**Q17.**

How many bits per pixel does NV12 use on average?

- a) 8
- b) 12
- c) 16
- d) 24


**Q18.**

Which layout describes NV12?

- a) One block with Y, U and V all mixed
- b) Three blocks: Y, then U, then V
- c) Two blocks: Y, then U and V interleaved
- d) Two blocks: U and V, then Y


**Q19.**

Why can a hard ISP output one finished pixel on every clock tick, although each pixel needs many steps?

- a) It skips steps for most pixels
- b) Every step has its own circuit, and all of them work at the same time on different pixels
- c) It stores the whole frame first
- d) The CPU does half the work


**Q20.**

The main reason products still use a hard ISP, although a soft ISP can produce the same image, is:

- a) A soft ISP cannot do demosaic
- b) A hard ISP gives real-time speed at far lower power and leaves the CPU free
- c) A hard ISP is easier to update
- d) A soft ISP needs a special sensor


**[Answer sheet for this quiz →](answers.md#quiz-1)**

---
