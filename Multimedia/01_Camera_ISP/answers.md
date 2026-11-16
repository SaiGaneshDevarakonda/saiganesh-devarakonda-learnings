# Answers: Camera ISP

[← Notes](README.md) · [Quiz](quiz.md)


## Quiz answer key

<a id="quiz-1"></a>
## Quiz: Camera ISP, from light to NV12 (20 questions)

1. **b)** How much light arrived: A photodiode is colour-blind. It only counts light.
2. **c)** A single colour filter sits on top of each photodiode: The filter decides which colour that pixel measures.
3. **c)** 1 red, 2 green, 1 blue: The R, G, G, B group repeats across the sensor.
4. **b)** The human eye is most sensitive to green: Extra green samples make the picture look sharper.
5. **b)** 0 to 1023: 10 bits give 1,024 levels.
6. **c)** Later stages multiply the values, and too few levels cause visible banding: More bits also give more dynamic range.
7. **b)** Bits travel one after another on a few wires: One clock lane plus one to four data lanes.
8. **c)** To let the receiver detect damaged pixel data: A mismatch raises a checksum (CRC) error.
9. **b)** Pixels reading a small non-zero value in total darkness: That offset is subtracted from every pixel.
10. **c)** Nearby pixels of the same colour: Neighbours with a different filter measure a different colour.
11. **c)** Fills in the two missing colours at every pixel using neighbours: It removes the mosaic.
12. **b)** RGB, three numbers per pixel: Demosaic is the step where Bayer becomes RGB.
13. **c)** The colour of the light source tints the whole picture: Gains on red and blue make white look white again.
14. **a)** Much too dark: The sensor is linear, but eyes are more sensitive in dark areas.
15. **c)** A black-and-white picture: Y is brightness only. U and V carry the colour.
16. **c)** 4 pixels: One 2×2 square shares one U and one V.
17. **b)** 12: 8 bits of Y plus 4 bits of shared U and V per pixel.
18. **c)** Two blocks: Y, then U and V interleaved: That is what semi-planar means.
19. **b)** Every step has its own circuit, and all of them work at the same time on different pixels: It is a pipeline, like an assembly line.
20. **b)** A hard ISP gives real-time speed at far lower power and leaves the CPU free: Soft is flexible, hard is efficient.


<a id="practice-solutions"></a>
## Practice solutions

### 1. Size of one 640 × 480 frame
Pixels = 640 × 480 = 307,200.

| Format | Calculation | Size |
|---|---|---|
| RAW10, packed | 307,200 × 10 ÷ 8 | 384,000 bytes |
| RGB888 | 307,200 × 3 | 921,600 bytes |
| YUYV (4:2:2) | 307,200 × 2 | 614,400 bytes |
| NV12 (4:2:0) | 307,200 × 1.5 | 460,800 bytes |

### 2. A 1280 × 720 NV12 frame
- Y values: 1280 × 720 = **921,600**
- 2×2 squares: 640 × 360 = 230,400, so **230,400 U values** and **230,400 V values**
- The Y plane comes first, so the UV plane starts at byte offset **921,600** (assuming no padding at the end of each line)
- Total size: 921,600 + 460,800 = 1,382,400 bytes

### 3. 1920 × 1080 NV12 at 30 frames per second
- One frame: 1920 × 1080 × 1.5 = 3,110,400 bytes
- One second: 3,110,400 × 30 = 93,312,000 bytes, about **93.3 MB**
- In bits: 93,312,000 × 8 = about 746.5 Mbit/s
- Compression ratio at 4 Mbit/s: 746.5 ÷ 4 = about **187 to 1**

### 4. Bayer pattern and one green pixel
```
        col1 col2 col3 col4
 row1    R    G    R    G
 row2    G    B    G    B
 row3    R    G    R    G
 row4    G    B    G    B
```
The green pixel at row 1, column 2 has **red on its left and right** (row 1, columns 1 and 3) and **blue above and below**. It sits on the top edge, so only the blue below it (row 2, column 2) exists. Real ISPs handle edges by mirroring or repeating the nearest pixels.

Notice that a green pixel in a red row gets red from the sides and blue from above and below. A green pixel in a blue row is the other way round.

### 5. Memory order of a 4 × 2 image
Pixels are Y0 to Y3 in the first row and Y4 to Y7 in the second.

```
 NV12 (12 bytes):  Y0 Y1 Y2 Y3 Y4 Y5 Y6 Y7 | U0 V0 U1 V1
 I420 (12 bytes):  Y0 Y1 Y2 Y3 Y4 Y5 Y6 Y7 | U0 U1 | V0 V1
 YUYV (16 bytes):  Y0 U0 Y1 V0 Y2 U1 Y3 V1   Y4 U2 Y5 V2 Y6 U3 Y7 V3
```
In NV12 and I420, the pair U0, V0 is shared by Y0, Y1, Y4, Y5, and U1, V1 by Y2, Y3, Y6, Y7. In YUYV each pair is shared by only two pixels side by side, which is why it needs four pairs.

### 6. Which stage to check
| Symptom | Stage |
|---|---|
| (a) Grey-looking blacks | Black level correction |
| (b) A few bright dots that never move | Defective pixel correction |
| (c) Dark corners | Lens shading correction |
| (d) An orange tint indoors | White balance |

### 7. Why convert to YUV at all?
After demosaic, white balance, colour correction and gamma, the picture is RGB and already looks normal, and a display wants RGB anyway. YUV adds nothing to how it looks. It is done because the next step is storing or compressing: separating brightness from colour lets the colour be stored at lower detail (4:2:0 halves the data), and video encoders take YUV as their input.

### 8. Three reasons to use a hard ISP
- **Speed:** it processes a pixel every clock tick, so high resolutions and frame rates run in real time.
- **Power:** a fixed circuit uses a small fraction of the energy a CPU would need for the same work.
- **CPU load and delay:** the CPU stays free for the rest of the system, and pixels flow straight through instead of waiting for a whole frame to be stored first.

### 9. Many checksum errors, no frame sync errors
Look at the **physical link first**: the cable or connector, the lane speed and timing settings, and signal quality. Checksum errors mean pixel bits are being flipped in transit, which is an electrical problem. The absence of frame sync errors shows that frames still start and end where expected, so the sensor and receiver agree on the configuration.
