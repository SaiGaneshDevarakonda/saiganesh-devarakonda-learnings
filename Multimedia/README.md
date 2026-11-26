# Multimedia

Each topic folder has a `README.md` with the theory, diagrams and an interview one-liner.

Diagrams are in each topic's `assets/` folder. Animations are GIFs that play right on GitHub, and the `.html` files are interactive versions: download one and open it in a browser.

## Topics
| # | Topic | Highlights |
|---|---|---|
| 01 | [Camera ISP: from light to NV12](01_Camera_ISP/README.md) | photodiodes, 4T pixel, CDS and the ramp ADC (animated), Bayer filter, MIPI CSI-2, every ISP stage with before/after pictures (animated), YUV and 4:2:0, NV12 and other formats, hard vs soft ISP, IBA and OBA, main and self paths, LILO and LIMO |
| 02 | [From the ISP to the video encoder](02_ISP_to_Video_Encoder/README.md) | frame-buffer vs low-latency mode (animated), the Sync IP, how buffers get DDR addresses (V4L2, CMA, dma-buf, mailbox), H.264 vs H.265, why frames are split into blocks, macroblocks vs CTUs on a real picture, rate-distortion optimisation, slices with a calculator |

### Coming next
Bandwidth and clock budget (sensor → ISP → DDR → encoder) · Display pipeline · GPU basics
