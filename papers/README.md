# Papers to Read

Two lists: **(A) core video-coding papers** to understand H.265, and **(B) Prof. Shreyas Sen's own papers** most relevant to your topic — read these to connect HEVC to his lab's actual research and to have something concrete to discuss in your first meeting.

**Habit:** for every paper you read, add a one-line takeaway below it (the problem it solves + one thing you learned). That habit *is* how researchers read.

---

## A. Core video-coding papers (read these to learn HEVC)

1. **G. J. Sullivan, J.-R. Ohm, W.-J. Han, T. Wiegand — "Overview of the High Efficiency Video Coding (HEVC) Standard,"** *IEEE Trans. CSVT*, 2012 [[S3]](../SOURCES.md#s3). ⭐ **Start here.** Readable and authoritative; worth reading twice — once shallow for orientation, once deep for detail.
   - _Takeaway:_ _______________

2. **J.-R. Ohm et al. — "Comparison of the Coding Efficiency of Video Coding Standards—Including HEVC,"** *IEEE TCSVT*, 2012 [[S4]](../SOURCES.md#s4). Where the "~50% of H.264" claim is measured.
   - _Takeaway:_ _______________

3. **B. Bross et al. — "Overview of the Versatile Video Coding (VVC) Standard,"** *IEEE TCSVT*, 2021 [[S5]](../SOURCES.md#s5). The successor (H.266).
   - _Takeaway:_ _______________

4. **Y. Chen et al. — "An Overview of Core Coding Tools in the AV1 Video Codec,"** PCS 2018 [[S6]](../SOURCES.md#s6). The royalty-free competitor.
   - _Takeaway:_ _______________

> Book (reference, not front-to-back): **Sze, Budagavi, Sullivan (eds.), *High Efficiency Video Coding (HEVC): Algorithms and Architectures*, Springer 2014** [[S7]](../SOURCES.md#s7) — the architecture/hardware chapters align closely with Prof. Sen's lab.

---

## B. Prof. Shreyas Sen (SPARC Lab) — papers relevant to your topic

Sen's group works on **energy-efficient / approximate hardware for image & video compression** — the exact bridge between HEVC and his lab. This is your strongest angle for framing the topic (transform/quantization datapaths, approximate computing, low-power real-time compression on sensor nodes).

**Read first (most relevant to H.265/compression):**

1. **M.-C. Li, A. Ghosh, S. Sen — "Approximate DCT and Quantization Techniques for Energy-Constrained Image Sensors,"** *IEEE TCAD*, 2024 [[S10]](../SOURCES.md#s10). ⭐ **Read this one.** Approximate division (bit-shifts) for quantization, loop perforation, precision scaling on a multiplier-less fast DCT → ~36% energy saving for ~2% quality loss; ~15 µW for the DCT+quantization stages.
   - Why it matters to you: it's literally the **transform + quantization** block of a codec ([notes: transform](../notes/what-is-transform-coding-and-why-dct.md), [quantization](../notes/what-is-quantization-and-qp.md)) optimized for energy — the clearest link from HEVC to this lab.
   - Open access: arXiv:2406.16358 · SPARC Lab site (publication J97).
   - _Takeaway:_ _______________

2. **"dAJC: A 2.02-mW 50-Mb/s Direct Analog-to-MJPEG Converter for Video Sensor Nodes …"** *IEEE JSSC*, 2025 (SPARC Lab J106; earlier CICC 2023 version C116) [[S11]](../SOURCES.md#s11). Compress *in the analog domain* on a video sensor node — skips digitizing every pixel.
   - Why it matters: shows the "compress at the sensor / hardware co-design" philosophy; contrast with HEVC's general-purpose approach.
   - _Takeaway:_ _______________

3. **"A 2 pJ/pixel Time-Domain Weight and Integrating-MAC based direct-Analog-to-MJPEG Compression for Video Sensor Nodes,"** *CICC*, 2025 (SPARC Lab C136) [[S12]](../SOURCES.md#s12).
   - _Takeaway:_ _______________

**Related (in-sensor / hardware efficiency — skim for context):**

4. **"A 65nm 21.9 pJ/Sa Pixel-to-PWM Conversion SoC … for ULP Body-Worn Video Sensor Nodes with Distributed Real-Time Inference,"** *CICC*, 2024 (C129) [[S13]](../SOURCES.md#s13).
   - _Takeaway:_ _______________

5. **"A Real-Time Memory-Less In-Sensor Time-Domain Convolution Processor with Programmable Kernel for Feature Extraction,"** *ISCAS*, 2025 (C137) [[S14]](../SOURCES.md#s14).
   - _Takeaway:_ _______________

> **Full, up-to-date list:** SPARC Lab publications [[S15]](../SOURCES.md#s15) — https://engineering.purdue.edu/~shreyas/SparcLab/publications.html
> **Note:** Sen's *main* research is mixed-signal circuits, Internet of Bodies, and hardware security; compression appears specifically through the energy-efficient-hardware angle above. Confirm with him whether he wants HEVC framed toward **hardware/energy efficiency** (likely) or toward **coding algorithms**.

---

## How to talk about these in your first meeting
- Lead with paper B1: "I read your approximate-DCT/quantization work — HEVC's transform+quantization stage is exactly that datapath, but general-purpose and much heavier. Is the interest in bringing that energy-efficiency approach to a standardized video codec like HEVC/VVC?"
- That single sentence shows you (a) learned the standard, (b) read his work, and (c) can see the connection. That's a strong first impression.
