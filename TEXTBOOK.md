# H.265/HEVC: The Complete Primer
### From First Principles to the Research Frontier

**Purpose of this book:** this is the textbook version of everything behind Presentation 1 ("Introduction to HEVC") and Presentation 2 ("Inside the HEVC Coding Loop") — written at the depth a PhD student should hold this material, not the depth a 30-minute talk can show. Every slide you present is the tip of a chapter here.

**How to use this book:**
1. Read a chapter start to finish, once, without stopping to look things up.
2. Re-read it a second time working through every equation and worked example by hand.
3. Answer the chapter quiz **closed-book**. Answers are in [Appendix C](#appendix-c--quiz-answer-key).
4. Watch at least one recommended video per chapter for visual intuition — reading equations and *seeing* a DCT basis function or a motion field are different kinds of understanding, and you need both.
5. Anything you can't answer cold goes into [open-questions.md](open-questions.md).

**Relationship to the rest of this repo:**
- [notes/](notes/README.md) — the fast, one-page-per-question version of this material. Use notes/ for a 5-minute refresher; use this book when you need the full argument.
- [presentation/1_introduction.pptx](presentation/1_introduction.pptx) — Presentation 1. Covered by **Parts I and II** of this book.
- [presentation/2_inside_hevc_coding_loop.pptx](presentation/2_inside_hevc_coding_loop.pptx) and its [study guide](presentation/2-study-guide.md) — Presentation 2. Covered by **Part III** of this book.
- [SOURCES.md](SOURCES.md) — every citation `[[S#]]` in this book resolves there. New sources introduced specifically for this book start at S46 and are appended there as they're needed.
- [papers/README.md](papers/README.md) — the primary-literature reading list this book keeps pointing back to.

**A note on depth:** this book does not shy away from equations, historical primary sources, or "why," not just "what." If a section feels like more than you need for a 30-minute talk, that's correct — a talk shows the audience the peak of the mountain; this book is what lets *you* stand at that peak without slipping.

**A note on citation reliability:** every source in [SOURCES.md](SOURCES.md) marked ⚠️ (which includes all of S1–S2 and most sources added specifically for this book, S42–S50) has its DOI, document number, or exact bibliographic detail recalled from training knowledge, not freshly verified against the publisher. The *papers and their content* are real and the facts drawn from them in this book are accurate to the best of that knowledge — but **do not cite a ⚠️ identifier in a real bibliography, proposal, or paper without independently confirming it first** (search the title on IEEE Xplore/Google Scholar and confirm volume, issue, page, and DOI match). Sources marked ✅ were actually opened and confirmed during this repo's research sessions and carry no such caveat.

---

## Table of Contents

**Part I — Foundations**
- [Chapter 1 — Why Compress Video At All?](#chapter-1--why-compress-video-at-all)
- [Chapter 2 — Digital Video Representation](#chapter-2--digital-video-representation)
- [Chapter 3 — Interlaced vs. Progressive Scan](#chapter-3--interlaced-vs-progressive-scan)
- [Chapter 4 — The Four Kinds of Redundancy in Video](#chapter-4--the-four-kinds-of-redundancy-in-video)
- [Chapter 5 — The Hybrid Codec Pipeline: A Historical and Architectural Overview](#chapter-5--the-hybrid-codec-pipeline-a-historical-and-architectural-overview)

**Part II — Prediction and Temporal Structure**
- [Chapter 6 — Frame Types: I, P, B and GOP Structure](#chapter-6--frame-types-i-p-b-and-gop-structure)
- [Chapter 7 — Intra Prediction](#chapter-7--intra-prediction)
- [Chapter 8 — Inter Prediction: Motion Estimation and Motion Compensation](#chapter-8--inter-prediction-motion-estimation-and-motion-compensation)
- [Chapter 9 — How HEVC Compares: H.264, AV1, and VVC](#chapter-9--how-hevc-compares-h264-av1-and-vvc)
- [Chapter 10 — Where H.265 Research Is Heading](#chapter-10--where-h265-research-is-heading)

**Part III — Inside the HEVC Coding Loop**
- [Chapter 11 — The Closed-Loop Principle, In Full Depth](#chapter-11--the-closed-loop-principle-in-full-depth)
- [Chapter 12 — Partitioning the Picture: CTU, CU, PU, TU](#chapter-12--partitioning-the-picture-ctu-cu-pu-tu)
- [Chapter 13 — Transform Coding and the DCT](#chapter-13--transform-coding-and-the-dct)
- [Chapter 14 — Quantization and the Rate–Quality Dial](#chapter-14--quantization-and-the-ratequality-dial)
- [Chapter 15 — CABAC: Entropy Coding](#chapter-15--cabac-entropy-coding)
- [Chapter 16 — In-Loop Filters: Deblocking and SAO](#chapter-16--in-loop-filters-deblocking-and-sao)
- [Chapter 17 — Rate–Distortion Optimization](#chapter-17--ratedistortion-optimization)
- [Chapter 18 — One Block's Complete Journey](#chapter-18--one-blocks-complete-journey)

**Part IV — The Research Frontier**
- [Chapter 19 — Searching HEVC's Decision Space Efficiently](#chapter-19--searching-hevcs-decision-space-efficiently)
- [Chapter 20 — Where This Connects to Prof. Sen's SPARC Lab](#chapter-20--where-this-connects-to-prof-sens-sparc-lab)

**Part V — Completing the Toolkit**
- [Chapter 21 — Quality Metrics and BD-Rate: How "50%" Is Actually Measured](#chapter-21--quality-metrics-and-bd-rate-how-50-is-actually-measured)
- [Chapter 22 — Bitstream Structure: NAL Units, Slices, Profiles, and Levels](#chapter-22--bitstream-structure-nal-units-slices-profiles-and-levels)
- [Chapter 23 — Reading Hardware and Approximate-Computing Papers Critically](#chapter-23--reading-hardware-and-approximate-computing-papers-critically)

**Back matter**
- [Appendix A — Glossary](#appendix-a--glossary)
- [Appendix B — Master Reading & Video List by Chapter](#appendix-b--master-reading--video-list-by-chapter)
- [Appendix C — Quiz Answer Key](#appendix-c--quiz-answer-key)
- [Appendix D — Formula Reference Sheet](#appendix-d--formula-reference-sheet)

---

# Part I — Foundations

*(Presentation 1, first third: why compression exists and what raw video looks like before anything is done to it.)*

## Chapter 1 — Why Compress Video At All?

### 1.1 The size problem, in real numbers

Start from the definition of raw (uncompressed) video: a sequence of frames, each frame a grid of pixels, each pixel a set of numbers describing color. Nothing about "compression" yet — just count bits.

Take a common case: **1920×1080 resolution, 8 bits per channel, 3 channels (RGB), 30 frames per second.**

```
bits per pixel        = 8 bits × 3 channels = 24 bits
pixels per frame       = 1920 × 1080 = 2,073,600
bits per frame          = 2,073,600 × 24 = 49,766,400 bits ≈ 6.22 MB
bits per second         = 6.22 MB × 30 fps ≈ 186.6 MB/s
bits per minute         = 186.6 MB/s × 60 ≈ 11.2 GB/minute
```

**One minute of raw, uncompressed 1080p30 video is over 11 gigabytes.** A two-hour movie would be roughly 1.3 terabytes. Compare that to a typical streamed movie file today: 2–8 GB for the same runtime. That's a **150×–650× reduction**, and that reduction is the entire subject of this book.

Push the numbers further: 4K (3840×2160) at 60fps, 10-bit — the kind of source Prof. Sen's lab and modern research work with — multiplies the raw rate by roughly 4× (resolution) × 2× (frame rate) × 1.25× (bit depth) ≈ **10× the 1080p30 rate**, i.e., well over 100 GB/minute raw. *(This "10×" is illustrative, holding the 3-channel assumption fixed on both sides for a clean comparison — a real 4:2:0 sensor pipeline would use Chapter 2's 1.5N sample count instead of 3N, changing the constant but not the conclusion: raw 4K is drastically larger than raw 1080p, by roughly the same order of magnitude either way.)* No consumer network, and no realistic on-chip memory bandwidth for a battery-powered sensor node, can move data at that rate. **Compression isn't an optimization — it's the precondition for video existing as a practical medium at all**, whether that medium is Netflix, a video call, or a wearable camera reporting to Prof. Sen's energy-constrained sensor nodes [[S10]](SOURCES.md#s10).

### 1.2 A short history of "how do we make this smaller"

- **1950s** — Cutler's DPCM patent: predict the next sample from the previous one, transmit only the difference. The ancestor of every predictive codec since.
- **1970s** — Habibi (1971) shows block transform coding (turning a block of pixels into frequency coefficients) outperforms simple sample-to-sample DPCM for images, because transforms decorrelate 2D blocks better than 1D prediction alone.
- **1979** — Netravali & Robbins [[S42]](SOURCES.md#s42) add *motion compensation*: predict a block of the current TV frame from a *displaced* block in the previous frame, not just the same spatial position. This is the birth of "the previous frame, shifted, is usually a great predictor of the current frame."
- **1985** — Musmann, Pirsch & Grallert [[S43]](SOURCES.md#s43) combine motion-compensated temporal prediction with block transform coding into what they name **hybrid coding** — predict temporally, transform the leftover spatially, and close the loop so encoder and decoder never disagree (you'll get the full mechanics of "closing the loop" in Chapter 11).
- **1988–1990** — ITU-T H.261 [[S45]](SOURCES.md#s45), the first international standard, formalizes exactly this hybrid architecture for videoconferencing.
- **1993–2003** — MPEG-1, MPEG-2 (DVDs, broadcast TV), H.263, MPEG-4 Part 2 refine the same architecture for growing use cases.
- **2003** — H.264/AVC standardized — still the most-deployed video codec on Earth today.
- **2013** — H.265/HEVC standardized — roughly **50% bitrate reduction at equal quality** versus H.264 [[S4]](SOURCES.md#s4), the number you'll be asked to justify in almost any HEVC conversation.
- **2020** — H.266/VVC standardized — another ~40–50% over HEVC [[S5]](SOURCES.md#s5).
- **Present** — AV1 (royalty-free, [[S6]](SOURCES.md#s6)) as a competing lineage, and a growing research thread on learned/neural compression [[S26]](SOURCES.md#s26).

**The one-sentence version you should be able to say without notes:** *every standard from H.261 to VVC is the same 1985 hybrid-coding architecture, with each generation adding more flexible tools (bigger and more shapes of blocks, more prediction modes, better entropy coding) inside that unchanged architecture.*

### 1.3 Two kinds of gain: engineering gain vs. fundamental limit

It's worth being precise about *why* compression works at all, because this is the difference between an engineering answer and an information-theory answer, and a PhD committee will sometimes want the second.

- **The information-theory ceiling**: Shannon's source coding theorem says a source with entropy `H` bits/symbol cannot be losslessly compressed below `H` bits/symbol on average, no matter how clever the coder. Video pixels are *not* independent random symbols — neighboring pixels and neighboring frames are highly statistically correlated — so the *true* entropy of a natural video signal is far lower than its raw bit count suggests. Compression is the practical art of getting *close* to that entropy. **A concrete illustration of the mechanism (this exact number resurfaces in Chapter 15 §15.3):** an 8-bit pixel is *stored* using 8 bits regardless of its value, but if, say, a specific residual value occurs with probability 0.98 in some context, its actual information content by Shannon's formula is only `−log2(0.98) ≈ 0.029` bits — meaning an ideal coder could represent that highly-predictable value using roughly 1/275th of a stored bit's worth of information, on average, whenever it recurs. That gap — between 8 bits *stored* and 0.029 bits *actually informative* for a highly predictable value — is the entire reason prediction, transform, and entropy coding exist: each one is a different tool for shrinking that gap for a different kind of redundancy (Chapter 4).
- **The engineering answer**: video codecs don't compute entropy directly; they exploit *redundancy* (repetition/correlation) with tractable models — predict, then code only what wasn't predictable. Chapter 4 formalizes exactly what "redundancy" means in this context, in four distinct flavors.

### Chapter 1 Quiz
1. Compute the raw bitrate (MB/s) for 1280×720, 8-bit, RGB, 24 fps. Show your work.
2. Name, in order, the four historical milestones between DPCM and the first hybrid-coding standard (H.261).
3. What specifically did Netravali & Robbins add in 1979 that plain DPCM didn't have?
4. State the Shannon source-coding theorem in one sentence, and explain why it doesn't mean "20:1 compression is exactly the theoretical maximum for video."
5. Why is raw video's size described as "a precondition problem," not an optimization problem, for use cases like Prof. Sen's sensor nodes?

**Further reading/watching:** [[S27]](SOURCES.md#s27) Digital Video Introduction, §"Basic terminology" · [[S42]](SOURCES.md#s42) Netravali & Robbins (history) · [[S43]](SOURCES.md#s43) Musmann et al. (history) — video: search "Reducible — How Video Compression Works" on YouTube for a visual walkthrough of the size problem.

---

## Chapter 2 — Digital Video Representation

### 2.1 From continuous light to discrete numbers

A camera sensor measures continuous light intensity; a digital video file stores discrete numbers. Two independent discretizations happen:

1. **Spatial sampling** — the image is divided into a grid of **pixels** (picture elements). Resolution (e.g., 1920×1080) is the grid size. More pixels = more spatial detail = more data.
2. **Amplitude quantization (bit depth)** — each pixel's intensity is stored with a finite number of bits. **8-bit** gives 256 levels per channel (0–255); **10-bit** (increasingly common, and relevant to HDR and Prof. Sen's sensor work) gives 1024 levels. More bits = smoother gradients, less "banding," but more data.

Frame rate (frames per second, fps) discretizes *time* the same way resolution discretizes *space* — common values are 24 (film), 25/30 (broadcast/video), 60+ (gaming, sports, sensor applications requiring low motion blur).

### 2.2 Color: RGB vs. YCbCr, and why codecs don't use RGB

Displays emit **RGB** (Red, Green, Blue) — but codecs almost universally encode in **YCbCr**:

- **Y** (luma) — brightness, essentially a weighted grayscale of R, G, B.
- **Cb, Cr** (chroma) — blue-difference and red-difference color information.

**Why the switch?** Human vision is far more sensitive to changes in *brightness* than to changes in *color* — a well-established property of the human visual system (far more rod/luminance-sensitive photoreceptor processing than fine color discrimination at high spatial frequency). YCbCr separates the component our eyes care about most (Y) from the components they care about less (Cb, Cr), so a codec can afford to store the color components at **lower resolution** than the brightness — a technique called **chroma subsampling** — with minimal *perceived* quality loss even though it's a measurable, real loss of color data.

### 2.3 Chroma subsampling: 4:2:0 and the arithmetic that halves your data

The notation `J:a:b` (e.g., 4:4:4, 4:2:2, 4:2:0) describes, for every 4 luma pixels horizontally:
- `J` = reference horizontal sample count (always 4 by convention)
- `a` = chroma samples in the first row of those 4
- `b` = chroma samples in the second row

**4:4:4** — full color resolution, Cb and Cr sampled at every luma pixel. No chroma compression.
**4:2:2** — chroma sampled at half horizontal resolution, full vertical. Common in professional/broadcast production.
**4:2:0** — chroma sampled at half horizontal **and** half vertical resolution. The default for consumer video (streaming, most HEVC content).

**The arithmetic — why 4:2:0 roughly halves total data:**

Take a 4×2 block of luma pixels (8 luma samples). In 4:4:4, you'd also need 8 Cb + 8 Cr samples = 16 chroma samples, for 24 samples total. In 4:2:0, chroma is subsampled 2× horizontally and 2× vertically, so that same 4×2 luma area needs only **1 Cb + 1 Cr** sample (a 2×1 chroma grid covering the 4×2 luma area) = 2 chroma samples, for **10 samples total** instead of 24.

More generally, for a full image with `N` luma samples:
```
4:4:4 total samples  = N (Y) + N (Cb) + N (Cr)         = 3N
4:2:0 total samples  = N (Y) + N/4 (Cb) + N/4 (Cr)      = 1.5N
```
`1.5N / 3N = 0.5` — **4:2:0 stores exactly half the total sample count of 4:4:4**, purely from the color-plane geometry, *before* any actual compression algorithm runs. This is why "shoot in 4:2:0" is already a 2× win, for free, exploiting a known limitation of human color perception rather than a mathematical redundancy in the signal — this is your first example of a **perceptual** (not statistical) redundancy, formalized in Chapter 4.

### 2.4 Why this matters for the rest of the book

Every later chapter operates on the Y, Cb, Cr planes independently (with Y almost always getting more coding "attention" — bigger transform priority, since it's what the eye is most sensitive to). When you see "block size" or "prediction" discussed generically in Chapters 6–17, understand it's applied per-plane, with the chroma planes inheriting a coarser geometry from the 4:2:0 subsampling described here.

### Chapter 2 Quiz
1. Define bit depth and explain the practical visual artifact of using too few bits (name it).
2. Why do codecs convert RGB to YCbCr before compressing, rather than compressing RGB directly?
3. Derive, from scratch, why 4:2:0 stores exactly half the samples of 4:4:4 (don't just recall the answer — redo the block arithmetic).
4. Is 4:2:0 subsampling a lossless or lossy step? Justify your answer precisely.
5. A sensor captures 3840×2160 at 10-bit, 4:2:0, 60fps. Compute the raw bitrate in Gbit/s. (Hint: total samples per frame = 1.5N where N = luma pixel count.)

**Further reading/watching:** [[S27]](SOURCES.md#s27) Digital Video Introduction §"Color" and §"Chroma subsampling" — video: search "4:2:0 chroma subsampling explained" for a visual grid diagram.

---

## Chapter 3 — Interlaced vs. Progressive Scan

### 3.1 Two ways to deliver a frame to a screen

**Progressive scan** draws every line of a frame in order, top to bottom, all belonging to the *same instant in time*. This is how essentially all modern displays, phones, and streaming content work.

**Interlaced scan** is a broadcast-television-era technique: each frame is split into two **fields** — one containing only the odd-numbered lines, one containing only the even-numbered lines — captured/displayed at *different* instants in time, alternating at twice the field rate. E.g., "1080i60" delivers 60 fields/second, each field only half-vertical-resolution, reconstructed by the eye/brain (and CRT phosphor persistence) into an apparent 30 full-frames/second image.

### 3.2 Why interlacing existed at all

Interlacing is a bandwidth trick from an era (1930s–2000s broadcast TV) when transmission bandwidth was far more constrained than today: it delivers **perceived motion smoothness at twice the field rate** while transmitting only half the vertical data per field, exploiting the fact that CRT phosphor persistence and human vision partially "fuse" the two fields. It was a clever bandwidth/quality tradeoff for analog and early digital broadcast (e.g., the NTSC/PAL/DVB standards, and H.262/MPEG-2's built-in interlace support), not an arbitrary historical accident.

### 3.3 Why it matters for a modern codec like HEVC

HEVC (and H.264 before it) supports interlaced content mainly for **backward compatibility with broadcast infrastructure**, not because interlacing is architecturally central to modern coding. Two practical consequences worth knowing:
- Interlaced content complicates prediction: a coding tool built for progressive frames (motion compensation assuming one coherent spatial grid per instant) needs field-aware variants when applied to interlaced fields, since two fields of the same "frame" are temporally offset.
- The overall industry trend, and HEVC/VVC's actual primary design target, is **progressive** — streaming, mobile, and sensor-node video are progressive by default. If asked "does HEVC handle interlaced video," the accurate answer is "yes, with legacy tools it inherited, but essentially all new content and all of Prof. Sen's sensor-node work is progressive."

### Chapter 3 Quiz
1. Explain, precisely, what a "field" is and how two fields relate to one interlaced frame.
2. Why did interlacing offer a genuine bandwidth advantage in analog broadcast, specifically?
3. Why is interlacing largely irrelevant to a battery-powered video sensor node's design considerations?

**Further reading:** [[S27]](SOURCES.md#s27) Digital Video Introduction §"Interlaced vs. progressive."

---

## Chapter 4 — The Four Kinds of Redundancy in Video

### 4.1 Why "redundancy" is the master concept of this entire field

Every compression technique in this book — prediction, transform, quantization, entropy coding — is best understood as *exploiting one specific kind of redundancy*. If you can name which redundancy a tool exploits, you can explain *why* the tool exists, not just *what* it does. This is the single most useful mental model in the whole subject.

### 4.2 The four kinds, defined precisely

1. **Spatial (intra-frame) redundancy** — neighboring pixels within the same frame tend to have similar values (a patch of sky is uniformly blue). Exploited by: **intra prediction** (Chapter 7) and the **spatial transform** (Chapter 13).
2. **Temporal (inter-frame) redundancy** — consecutive frames tend to look very similar, differing mainly by motion or lighting change. Exploited by: **inter prediction / motion compensation** (Chapter 8).
3. **Statistical redundancy** — after prediction and transform, the resulting symbols (residual coefficients, motion vectors, mode flags) are *not* uniformly distributed — some values (especially zero) are far more common than others. Exploited by: **entropy coding / CABAC** (Chapter 15).
4. **Perceptual (psychovisual) redundancy** — some information is present in the signal but the human visual system can't perceive it well (fine color detail, high-frequency detail in busy/textured regions, information hidden by "masking" near strong edges). Exploited by: **chroma subsampling** (Chapter 2) and **quantization** (Chapter 14) — this is the *only* one of the four that is deliberately **lossy**; the other three are, in principle, exploitable losslessly.

### 4.3 A worked mapping: which tool, which redundancy

| Redundancy | Codec tool | Lossy or lossless? | Chapter |
|---|---|---|---|
| Spatial | Intra prediction + transform | Lossless (prediction/transform), lossy only via quantization applied after | 7, 13 |
| Temporal | Inter prediction / motion compensation | Lossless in principle | 8 |
| Statistical | CABAC | Lossless | 15 |
| Perceptual | Chroma subsampling, quantization | Lossy | 2, 14 |

**The crucial insight to state clearly if asked "where is the actual quality loss":** three of the four redundancy-exploitation mechanisms (prediction, transform, entropy coding) are essentially lossless — they *reorganize* or *compact* information without throwing it away. **Only chroma subsampling and quantization are deliberately lossy.** This is worth having ready as a single confident sentence, because it's a question that comes up constantly and a surprising number of people get it wrong by assuming "compression = lossy" applies uniformly to every stage.

### Chapter 4 Quiz
1. Define all four redundancy types in your own words, no notes.
2. For each of the following tools, name which redundancy it primarily exploits: (a) motion compensation, (b) CABAC, (c) 4:2:0 subsampling, (d) intra prediction.
3. Which redundancy-exploiting stage(s) are lossy, and which are (near-)lossless? Justify why quantization specifically must be lossy for compression to work at low bitrates.
4. Explain, using perceptual redundancy, why coding a busy/textured region can tolerate a *coarser* quantization step than a smooth region without visible quality loss (this connects forward to Chapter 14's discussion of "masking").

**Further reading/watching:** [[S27]](SOURCES.md#s27) Digital Video Introduction §"Redundancy" · [[S30]](SOURCES.md#s30) Reducible — visual explanation of spatial/statistical/perceptual redundancy via JPEG (JPEG shares the transform+quantize+entropy-code machinery with HEVC, minus motion).

---

## Chapter 5 — The Hybrid Codec Pipeline: A Historical and Architectural Overview

### 5.1 The one diagram that is the entire field

```
PREDICT → SUBTRACT → TRANSFORM → QUANTIZE → ENTROPY-CODE → bitstream
 (guess)   (residual =  (concentrate   (discard the      (pack tightly;
            original −    energy into    perceptually-     short codes
            prediction)   few coeffs)    minor part —       for frequent
                                         the lossy step)     symbols)
```
plus a **reconstruction feedback path** (inverse quantize → inverse transform → add prediction back → filter → store as reference) that lets the encoder always predict from *exactly* what the decoder will have. This feedback path is the entire subject of Chapter 11 — for now, know that it exists and why in one sentence: **without it, encoder and decoder would slowly disagree about what past frames looked like, and the picture would degrade (drift) over time.**

### 5.2 Why this exact order, and not some other order

Each stage is a precondition for the next to work well:
1. **Predict** removes the redundancy that's cheapest to remove directly (spatial or temporal repetition) — this is "free" compression in the sense that a good predictor requires no loss of information, only computation.
2. **Transform** takes whatever prediction *couldn't* remove (the residual) and reorganizes it so that most of the signal's energy lands in a few coefficients — setting up quantization to be efficient.
3. **Quantize** is the only place actual information is discarded — and because the transform already concentrated energy into a few coefficients, quantization can zero out *many* coefficients cheaply, rather than uniformly degrading everything.
4. **Entropy-code** exploits the fact that the *quantized* symbol stream (lots of zeros, a few small numbers, some skewed statistics on modes/motion vectors) is highly non-uniform — squeezing out the last bit of statistical redundancy losslessly.

If you tried to entropy-code raw pixels directly (skip prediction and transform), you'd get almost nothing — raw pixel values are close to uniformly distributed and only weakly correlated pixel-to-pixel at the *sample* level (compared to how strongly correlated a whole *block* is to its temporal or spatial neighbor). The order encodes an implicit division of labor: **prediction and transform do the heavy lifting; quantization takes the loss; entropy coding cleans up what's left.**

### 5.3 "Hybrid" — hybrid of what?

The term, from Musmann/Pirsch/Grallert [[S43]](SOURCES.md#s43), specifically means the *hybrid* of **predictive coding** (DPCM-style: predict, code the difference) and **transform coding** (block-based frequency decomposition, from Habibi's line of work). Before hybrid coding, these were seen as competing techniques; the 1985 insight was that combining them — predict first (mostly temporal), then transform the leftover residual (mostly spatial) — outperforms either alone, because temporal and spatial redundancy are different phenomena best attacked by different tools.

### 5.4 What HEVC changed, and — just as important — what it didn't

**Unchanged since 1985:** the five-stage loop above, and the closed reconstruction feedback path.

**What HEVC actually added, relative to H.264 (each gets its own chapter):**
- Block sizes up to 64×64 (vs. H.264's fixed 16×16 macroblock), and a recursive quadtree instead of a fixed grid (Chapter 12).
- 35 intra prediction modes (vs. H.264's 9) (Chapter 7).
- Larger transform sizes, up to 32×32 (vs. H.264's max 8×8 in most profiles) (Chapter 13).
- CABAC as the *only* entropy coding method (H.264 offered a simpler alternative, CAVLC, as well) (Chapter 15).
- A brand-new filter, **SAO**, added after deblocking (Chapter 16).

Every one of these is a *refinement of a parameter or an added tool inside the unchanged 1985 architecture* — which is precisely why Chapter 1's "one-sentence" framing holds up under scrutiny.

### Chapter 5 Quiz
1. Draw the five-stage pipeline from memory, labeling which stage(s) are lossy.
2. Explain, in terms of "division of labor," why entropy-coding raw pixels directly would fail to compress well.
3. What does "hybrid" specifically refer to in "hybrid coding," and who coined the term (year and authors)?
4. List three specific tools HEVC added over H.264, and identify which of the five pipeline stages each one belongs to.
5. True or false, with justification: "HEVC uses a fundamentally different compression architecture than H.264." 

**Further reading/watching:** [[S43]](SOURCES.md#s43) Musmann et al. (the original hybrid-coding survey) · [[S33]](SOURCES.md#s33) Yao Wang (NYU) slides — the full loop diagram · [[S3]](SOURCES.md#s3) HEVC overview, §I–II.

---

*(End of Part I.)*

---

# Part II — Prediction and Temporal Structure

*(Presentation 1, remainder: how frames relate to each other in time, and the two prediction mechanisms — intra and inter — that do the actual redundancy removal.)*

## Chapter 6 — Frame Types: I, P, B and GOP Structure

### 6.1 Three frame types, three prediction sources

- **I-frame (Intra)** — predicted only from *itself* (spatial neighbors within the same frame, via intra prediction, Chapter 7). Can be decoded standalone, with no dependency on any other frame. Largest in size (no temporal redundancy exploited).
- **P-frame (Predictive)** — predicted from **one or more *previously decoded* frames** (temporally earlier in decode order), via inter prediction (Chapter 8). Much smaller than an I-frame for typical video, since consecutive frames are highly similar.
- **B-frame (Bi-predictive)** — predicted from **both a previous and a future frame** (future in *display* order, but already decoded by the time the B-frame is processed, because decode order ≠ display order for B-frames). Can average or select between two references, often giving the best compression of the three types, since it has the most prediction candidates to choose from.

### 6.2 GOP: Group of Pictures

A **GOP** is the span of frames between one I-frame and the next. GOP structure defines the pattern of I/P/B frames within that span, e.g.:
```
I B B P B B P B B P B B I B B P ...
```
**Why not all I-frames?** Because I-frames don't exploit temporal redundancy at all — an all-I-frame stream is essentially a sequence of independently compressed JPEG-like images, wasting the majority of available compression (recall Chapter 4: temporal redundancy is usually the *largest* single redundancy source in natural video, since consecutive frames at 24–60fps rarely change much).

**Why not all P/B with a single I-frame at the very start?** Two reasons:
1. **Random access / seeking** — a player needs an I-frame to jump into the middle of a video without decoding from the very beginning. GOP length directly trades off *seek granularity* against *compression efficiency* (longer GOP = better compression, fewer random-access points).
2. **Error resilience** — if a frame is lost or corrupted (streaming over an unreliable network), everything predicted from it (directly or transitively) is also corrupted, until the next I-frame "resets" the chain. Shorter GOPs limit how far an error can propagate.

### 6.3 Open GOP vs. closed GOP, and IDR vs. CRA

- **Closed GOP** — no frame in one GOP depends on any frame in a different GOP. Every GOP is fully self-contained; safest for seeking/splicing, at some compression cost.
- **Open GOP** — B-frames near a GOP boundary are allowed to reference into the *next* GOP for slightly better compression, at the cost of GOP independence.
- **IDR (Instantaneous Decoder Refresh)** — a special I-frame that also clears the entire reference picture buffer; nothing after it can reference anything before it. The strongest form of random-access point.
- **CRA (Clean Random Access)** — an I-frame that allows some following frames (in decode order, "leading pictures") to still reference frames *before* it, useful for open-GOP structures with better compression, common in broadcast and streaming.

### 6.4 Hierarchical B-pyramids

Modern encoders (including HEVC reference software and x265) commonly use a **hierarchical B structure**: B-frames themselves reference other B-frames in a layered pattern, e.g. a "B-pyramid" where a mid-GOP B-frame is predicted from the I/P frames at the GOP boundaries, then frames between *that* B-frame and the boundaries are predicted from it, recursively. This maximizes how much prediction "leverage" every frame gets, at the cost of a deeper decode dependency chain and higher decode-order-vs-display-order complexity, and is a major contributor to modern encoders' bitrate savings relative to older flat I-P-P-P-… structures.

### 6.5 A concrete example worth being able to draw

```
Display order:   I0  B1  B2  P3  B4  B5  P6
Decode order:    I0  P3  B1  B2  P6  B4  B5
```
Notice B1 and B2 (displayed *before* P3) are decoded *after* P3 — because they need P3 as a "future" reference. This decode/display reordering is precisely why players show a buffering delay before B-frame-heavy streams start playing, and is a genuinely good "gotcha" fact to have ready.

### Chapter 6 Quiz
1. Define I, P, and B frames purely in terms of *what they're allowed to predict from*.
2. Why does an all-I-frame video waste most of the available compression, in terms of Chapter 4's redundancy framework?
3. Explain the tradeoff GOP length controls, in two sentences.
4. What is the difference between IDR and CRA, and why would a broadcaster prefer one over the other?
5. Given the display/decode order example above, explain *why* B1's decode position is after P3's.

**Further reading/watching:** [[S28]](SOURCES.md#s28) Forasoft — GOP Structure & I/P/B Frames (bitrate ratios, open/closed GOP, IDR/CRA, B-pyramid) · [[S27]](SOURCES.md#s27) Digital Video Introduction §"Frame types."

---

## Chapter 7 — Intra Prediction

### 7.1 The problem intra prediction solves

Within a single frame, spatially neighboring blocks tend to look similar — a smooth gradient, a repeated texture, a continuing edge. Intra prediction guesses a block's content from **already-decoded neighboring pixels in the same frame** (always the blocks above and to the left, since those are the ones decoded first in raster/z-order), so only the *difference* from that guess needs to be coded.

### 7.2 From H.264's 9 modes to HEVC's 35

H.264 [[S31]](SOURCES.md#s31) defined 9 intra prediction modes for 4×4 luma blocks: DC (flat average) and 8 directional modes (predicting along specific angles from neighboring pixels — e.g., pure horizontal, pure vertical, and diagonal variants).

HEVC expands this dramatically to **35 modes** [[S32]](SOURCES.md#s32):
- **Mode 0 — Planar**: predicts a smooth gradient across the block by interpolating between the top-right and bottom-left neighboring samples — ideal for smoothly varying regions (sky, out-of-focus backgrounds) where a single flat or single-angle prediction would leave visible banding.
- **Mode 1 — DC**: a single flat value, the average of the neighboring samples — best for genuinely uniform, flat regions.
- **Modes 2–34 — Angular**: 33 directional predictors, spanning roughly 180° of orientation (an average spacing on the order of ~5–6° — the actual per-mode angle step is *not* perfectly uniform, since HEVC defines each angle via an integer `intraPredAngle` table tuned for cheap integer-arithmetic interpolation, not equal degree spacing; treat "~5.6°" as an intuition-building average, not an exact per-mode value), each extending neighboring pixels along that specific angle into the current block — ideal for regions with a dominant edge or texture direction (a diagonal roofline, striped fabric).

**Why so many more modes?** Because more angular resolution means a lower average prediction error across arbitrary real-world edge orientations — H.264's 8 directions leave large angular gaps where no mode is a great fit; HEVC's 33 angular modes leave much smaller gaps. This is a direct, deliberate trade: more signaling overhead (need `log2(35)`-ish bits to signal the chosen mode, mitigated by *most probable mode* prediction schemes) in exchange for a smaller residual on average — precisely the kind of rate-distortion tradeoff formalized in Chapter 17.

### 7.3 Signaling the chosen mode efficiently

Since a full 35-way selection per block would be expensive to signal for every block, HEVC uses a **most probable mode (MPM)** list built from the intra modes used by the already-coded left and above neighboring blocks — since neighboring blocks in a real image very often share a dominant edge direction (a continuing rooftop line, a continuing gradient), the actual chosen mode is very often already in this short candidate list, letting the encoder signal "use MPM candidate #k" cheaply instead of the full mode index.

### 7.4 Where intra prediction sits in the bigger picture

Intra prediction is used: (a) for **every block of an I-frame**, since I-frames have no other frame to reference, and (b) for **individual blocks within P/B frames** where inter prediction happens to do worse — e.g., a newly-revealed background area (occlusion uncovering) that has no good match in any previous frame, or highly detailed regions where intra genuinely wins the RDO comparison (Chapter 17) against every candidate inter option.

### Chapter 7 Quiz
1. Why must intra prediction only use *already-decoded* neighboring pixels, and specifically the above/left neighbors?
2. Contrast Planar mode and DC mode — what kind of image content is each best suited for?
3. Why does HEVC use 35 modes instead of H.264's 9? What's the actual tradeoff being made?
4. What is the Most Probable Mode (MPM) mechanism solving, and why does it work well in practice (what real-image property does it exploit)?
5. Name a scenario where intra prediction would be chosen for a block *inside* a P-frame, despite inter prediction usually winning in P-frames.

**Further reading/watching:** [[S31]](SOURCES.md#s31) vcodex — H.264/AVC Intra Prediction (learn the 9-mode case first, it's simpler) · [[S32]](SOURCES.md#s32) Elecard — Spatial (Intra) Prediction in HEVC (the 35-mode angular fan diagram — essential to actually *see* this, not just read about it).

---

## Chapter 8 — Inter Prediction: Motion Estimation and Motion Compensation

### 8.1 Two distinct operations people often conflate

- **Motion estimation (ME)** — the *search* process: given a current block, find the best-matching block in a reference frame, described as a **motion vector** (a 2D displacement, e.g., "shifted 3 pixels right, 1 pixel down"). This is a *search/optimization* problem, computationally the most expensive part of most encoders, and entirely an **encoder-side** decision — the decoder never re-derives motion vectors, only reads the ones the encoder signals.
- **Motion compensation (MC)** — the *application* step: given a motion vector, fetch the indicated block from the reference frame and use it as the prediction. This is comparatively cheap and required on **both** encoder and decoder (the decoder must reproduce exactly the same predicted block the encoder used, per Chapter 11's closed-loop requirement).

**The clean one-liner:** *motion estimation finds the vector; motion compensation uses it.* Search vs. apply. Expensive vs. cheap. Encoder-only vs. encoder-and-decoder.

### 8.2 Why motion vectors are usually not whole pixels

Real-world motion rarely aligns to exact integer pixel offsets. HEVC supports **quarter-pixel (quarter-pel) motion vector precision**, meaning the reference block position can be, e.g., 3.25 pixels to the right — computed via **interpolation filters** (multi-tap FIR filters) that estimate what the image would look like at non-integer sample positions. Sub-pixel motion compensation is one of the more substantial contributors to inter-prediction accuracy versus integer-pixel-only motion, because it lets the predictor match true continuous-world motion far more closely.

### 8.3 Multiple reference frames and bi-prediction

Unlike very early codecs limited to "the immediately previous frame only," HEVC allows referencing **multiple previous (and, for B-frames, future) decoded frames**, and B-frames can form a prediction as a **weighted average of two reference blocks** (one from each direction) — genuinely useful when, e.g., an object is partially occluded in one reference but visible in the other, or when averaging reduces noise.

### 8.4 The residual, again

Whatever inter prediction still gets wrong (occlusion, non-rigid motion, lighting changes, camera noise) becomes the **residual**, exactly the same concept as Chapter 5 — after motion compensation, the residual is coded by the identical transform → quantize → entropy-code pipeline used for intra residuals. This is worth stating explicitly: **inter and intra prediction differ entirely in how they predict; they are identical in how the leftover residual is then handled.**

### 8.5 Why motion estimation is where most encoder compute time goes

A brute-force motion search would check every possible displacement within a search window for every block — computationally enormous (this is exactly the kind of search space Chapter 19's "fast mode search" and Prof. Sen's hardware-acceleration research target). Real encoders use fast heuristic search patterns (diamond search, hexagon search, predictive starting points from neighboring blocks' already-found motion vectors) to approximate the best match without exhaustive search — trading a small amount of prediction quality for a large reduction in search compute, itself a rate-distortion-adjacent (really, a *distortion-vs-compute*) tradeoff.

### Chapter 8 Quiz
1. State the difference between motion estimation and motion compensation in one sentence each, and say which is encoder-only vs. required on both sides.
2. Why does sub-pixel (quarter-pel) motion compensation improve prediction accuracy over integer-pixel-only search?
3. Give a concrete scenario where bi-prediction (averaging two references) outperforms single-reference prediction.
4. True or false: "the residual after inter prediction is handled completely differently from the residual after intra prediction." Justify.
5. Why is exhaustive (brute-force) motion search impractical, and what class of technique replaces it in real encoders?

**Further reading/watching:** [[S3]](SOURCES.md#s3) HEVC overview §"Inter prediction" · search YouTube "motion estimation motion compensation video compression" for a visual side-by-side.

---

## Chapter 9 — How HEVC Compares: H.264, AV1, and VVC

### 9.1 The numbers you should have memorized

| Standard | Year | vs. predecessor | Royalty status |
|---|---|---|---|
| H.264/AVC | 2003 | baseline | Licensed (patent pool) |
| H.265/HEVC | 2013 | **~50% bitrate reduction vs. H.264** at equal quality [[S4]](SOURCES.md#s4) | Licensed — fragmented, multiple patent pools, a factor in slower adoption than H.264 |
| AV1 | 2018 | ~30–50% vs. HEVC depending on content/settings [[S6]](SOURCES.md#s6) | **Royalty-free** (Alliance for Open Media) |
| H.266/VVC | 2020 | ~40–50% vs. HEVC [[S5]](SOURCES.md#s5) | Licensed, similar fragmentation concerns as HEVC |

### 9.2 Why licensing is not a footnote — it shaped real-world adoption

HEVC's technical superiority over H.264 did **not** translate into H.264-level ubiquity, largely because of **licensing fragmentation**: multiple separate patent pools (MPEG LA, HEVC Advance, Velos Media) with unclear, sometimes overlapping and expensive terms, made adoption legally risky/costly for browser vendors and device makers in a way H.264's single, clearer pool never was. This directly motivated **AV1**'s creation by the Alliance for Open Media (Google, Netflix, Amazon, Mozilla, and others) as a royalty-free alternative — a case study in how licensing economics, not just compression efficiency, determines which codec actually ships in your browser or phone.

### 9.3 Architectural differences worth knowing, not just efficiency numbers

- **AV1** shares the same broad hybrid-coding lineage (prediction, transform, quantization, entropy coding) but introduces its own tool set: larger superblocks (up to 128×128), a wider transform tool set, and its own entropy coder family; it's a *sibling* architecture to HEVC, not a *descendant*.
- **VVC (H.266)** is explicitly HEVC's direct successor and evolutionary continuation — CTU sizes up to 128×128, an even more flexible partitioning scheme (quadtree + binary + ternary splits, versus HEVC's quadtree-only), more intra modes (67 vs. HEVC's 35), and refinements throughout the same pipeline you're learning in this book. **If you understand HEVC's architecture deeply, VVC is "the same ideas, more flexible" — not new material.**

### 9.4 The honest framing for a research conversation

Coding *efficiency* keeps improving roughly every 6–8 years by ~40–50% per generation — a genuinely fast pace for a mature engineering field — but each generation also increases **encoder computational complexity** substantially (more modes, more block shapes, more RDO search space to evaluate), which is precisely the tension Chapter 19 and Prof. Sen's research address: the coding-efficiency gains are real, but they come from a *combinatorially larger search space*, and searching that space efficiently (in time and energy) is now as much the research problem as inventing new coding tools.

### Chapter 9 Quiz
1. State the approximate bitrate-savings figure for HEVC vs. H.264, and for VVC vs. HEVC.
2. Why did HEVC not achieve H.264-level adoption speed despite being more efficient? Name the specific mechanism.
3. Is AV1 a descendant of HEVC or a sibling architecture? Justify.
4. Name two specific tools VVC adds beyond HEVC, and identify which HEVC chapter each one extends (e.g., "128×128 CTUs extend Chapter 12's partitioning").
5. Explain, in your own words, why "better compression" and "more computationally expensive to encode" have gone hand-in-hand across every codec generation so far.

**Further reading/watching:** [[S5]](SOURCES.md#s5) Bross et al., VVC overview · [[S6]](SOURCES.md#s6) Chen et al., AV1 core tools · [[S29]](SOURCES.md#s29) Videospan — Video Codecs Explained (plain-English landscape view) · [[S34]](SOURCES.md#s34) Wikipedia — HEVC (licensing/adoption history section specifically).

---

## Chapter 10 — Where H.265 Research Is Heading

### 10.1 Two axes of open research, not one

It's tempting to think "video coding research = inventing new compression tools," but for a mature, widely-deployed standard like HEVC, most *active* research sits on a different axis entirely: **not "can we compress more," but "can we search the existing tool space faster and cheaper."** This is the pivot point of this entire book — Part III will show you *why* the tool space (CTU/CU/PU/TU choices, prediction modes, RDO) is so large, and Part IV shows *why searching it is now the bottleneck*.

### 10.2 The four active research directions (previewed here, detailed in Chapter 19)

1. **Fast mode/partition search** — early-termination heuristics and increasingly, learned (ML-based) predictors that skip evaluating most of the RDO search space.
2. **Parallel processing** — Tiles and Wavefront Parallel Processing (WPP), letting multiple cores decode/encode simultaneously despite CABAC's inherently serial design (Chapter 15).
3. **Hardware acceleration** — dedicated silicon (ASICs, specialized datapaths) for the most compute-heavy stages: motion estimation, the transform, entropy coding.
4. **Approximate computing** — deliberately trading a small amount of accuracy for large energy savings in the transform/quantization datapath — **this is precisely Prof. Sen's SPARC Lab angle** [[S10]](SOURCES.md#s10), covered in full in Chapter 20.

### 10.3 Beyond HEVC itself: learned/neural compression

A separate, faster-moving research thread replaces hand-designed tools (the DCT, hand-crafted intra modes) with learned neural network components — autoencoder-style compression pipelines trained end-to-end on the same rate-distortion objective (Chapter 17) but with the "prediction" and "transform" stages replaced by learned nonlinear functions [[S26]](SOURCES.md#s26). This is a genuinely open question in the field for whether/when neural approaches will displace hand-designed hybrid coding for mainstream deployment, given the huge computational cost of neural inference versus decades-optimized hand-crafted transforms — an efficiency tension that echoes the same "coding gain vs. compute cost" tension from Chapter 9.

### Chapter 10 Quiz
1. Contrast the two axes of research described in §10.1 — "more compression" vs. "faster search of existing compression" — and explain why the second is now the dominant active research question for HEVC specifically.
2. Name all four directions from §10.2 and, for each, state whether it's primarily a software, hardware, or algorithmic contribution.
3. Where specifically does approximate computing fit relative to the standard pipeline (which stage(s))?
4. Why does neural/learned compression face an efficiency tension analogous to the one described in Chapter 9?

**Further reading/watching:** [[S16]](SOURCES.md#s16) SPARC Lab home · [[S10]](SOURCES.md#s10) Prof. Sen's approximate DCT/quantization paper (read this one in full — it's the bridge to Chapter 20) · [[S26]](SOURCES.md#s26) CompressAI (explore a learned-codec implementation hands-on).

---

*(End of Part II.)*

---

# Part III — Inside the HEVC Coding Loop

*(Presentation 2, in full: partitioning, the residual pipeline, filtering, and the optimization engine that ties every decision together.)*

## Chapter 11 — The Closed-Loop Principle, In Full Depth

### 11.1 Why this chapter comes first in Part III

Everything else in Part III — partitioning (Ch. 12), the transform (Ch. 13), quantization (Ch. 14), CABAC (Ch. 15), filters (Ch. 16), RDO (Ch. 17) — is a *component* that sits inside the loop this chapter describes. Understanding this chapter first means every later chapter answers a question you already know to ask: *"and how does this component participate in keeping the loop closed?"*

### 11.2 The historical lineage (full detail; summarized already in Chapter 1 and Chapter 5)

The closed-loop principle is not an HEVC invention — it is the direct descendant of a specific, traceable lineage:
- **Cutler's DPCM (1950s)** — predict, transmit only the difference. No feedback/reconstruction concept yet, because DPCM was originally conceived for lossless or near-lossless audio-like signals where drift wasn't yet the central problem.
- **Netravali & Robbins, "Motion-Compensated Television Coding: Part I" (1979)** [[S42]](SOURCES.md#s42) — the paper that, for the first time, rigorously analyzes **quantization noise feedback** in a motion-compensated predictive video coder, and shows that predicting from the *reconstructed* (post-quantization) picture rather than the original is required for a stable, non-diverging system. This is, quite literally, the origin of the box on slide 3 you're studying.
- **Musmann, Pirsch & Grallert (1985)** [[S43]](SOURCES.md#s43) — names "hybrid coding," formalizes the combined predict + transform + closed-loop-feedback architecture as the de facto standard structure, drawing the same block diagram (predictor, transform, quantizer, entropy coder, inverse path, loop filter, frame store) that appears — with 40 more years of refinement to the *contents* of each box — in the HEVC standard today.
- **H.261 (1988–1990)** [[S45]](SOURCES.md#s45) — the first ITU-T standard to mandate this closed-loop architecture, for videoconferencing over ISDN lines.

### 11.3 The formal model

Let `s[n]` denote the original signal (a pixel block, indexed by spatial position and/or time). Define:

```
p[n]      = predictor output (intra: spatial neighbors; inter: motion-compensated
                               reference block)
e[n]      = s[n] − p[n]                          (residual)
Ê[n]      = Q( T( e[n] ) )                        (transform + quantize — LOSSY)
ê[n]      = T⁻¹( Q⁻¹( Ê[n] ) )                     (encoder's local reconstruction
                                                   of the residual; ê[n] ≠ e[n] in
                                                   general because Q is lossy)
ŝ[n]      = p[n] + ê[n]                           (reconstructed sample)
ŝ_f[n]    = SAO( Deblock( ŝ[n] ) )                 (filtered reconstruction — THIS
                                                   becomes the reference for
                                                   future prediction)
```

The single substitution that defines the entire closed-loop principle: **`p[n+1]` is a function of `ŝ_f[n]`, never of `s[n]`.**

### 11.4 The drift derivation — do this on a whiteboard until it's automatic

Suppose, hypothetically, an encoder predicted from the *original* signal instead:
```
p_open[n+1] = f( s[n] )        ← uses the ORIGINAL frame n, not the reconstruction
```
The decoder never receives `s[n]` — only the entropy-decoded `Ê[n]`, from which it computes:
```
ŝ_decoder[n] = p[n] + T⁻¹(Q⁻¹(Ê[n]))
```
This differs from the true original by exactly the quantization error at step `n`:
```
s[n] − ŝ_decoder[n] = q[n]        (nonzero whenever quantization is lossy)
```
At step `n+1`, the **encoder** computed its residual using `f(s[n])`, but the **decoder** can only ever reproduce `f(ŝ_decoder[n])`. Because `f(s[n]) ≠ f(ŝ_decoder[n])`, the decoder's `ŝ_decoder[n+1]` is now wrong by an amount that traces back to `q[n]` — **and this becomes part of the reference for frame `n+2`, `n+3`, and onward.** The error is never corrected; it compounds. This phenomenon is called **drift**, and it is *usefully analogized to*, though not formally identical to, instability in a classical feedback-control system: a loop with disturbance (quantization error) injected every step but no corrective feedback path has no mechanism to prevent that disturbance from accumulating. (Be precise about this if pressed: this is a helpful engineering analogy for intuition, not a claim that Netravali & Robbins invoke formal control-theoretic stability proofs — their actual 1979 contribution [[S42]](SOURCES.md#s42) is an empirical/analytical demonstration that closed-loop, reconstruction-based prediction avoids the divergence that open-loop, original-based prediction exhibits.)

**Making "compounds" concrete — a small numeric simulation.** Suppose (illustrative numbers, a single sample tracked across frames) each frame's prediction is simply "copy the previous reconstructed value" (`f(x) = x`, a static scene), true signal `s[n] = 100` for all `n`, and quantization always introduces a fixed error of `+3` on whatever it encodes (a simplification, but suffices to show the mechanism). **Closed-loop** (correct): frame 0 encodes `e[0] = 100 − p[0]`; whatever `p[0]` is, the reconstruction is `ŝ[0] = p[0] + e[0] + 3`. Frame 1 predicts from `ŝ[0]`, encodes `e[1] = 100 − ŝ[0]`, reconstructs `ŝ[1] = ŝ[0] + e[1] + 3 = 100 + 3 = 103`. Frame 2 predicts from `103`, encodes `e[2] = 100 − 103 = −3`, reconstructs `ŝ[2] = 103 + (−3) + 3 = 103`. **The error stabilizes at a constant +3 offset and stops growing** — each frame's residual automatically corrects for the previous frame's error, because the encoder can *see* the error it's correcting for (it has `ŝ[n]`, not `s[n]`).

**Open-loop** (broken): now suppose the encoder instead predicts from the *original* `s[n]=100` every time, oblivious to what the decoder actually reconstructed. Frame 0: same as above, decoder reconstructs `103`. Frame 1: **encoder** computes `e[1] = 100 − 100 = 0` (predicting from the original, thinking nothing changed) and transmits that. But the **decoder**, predicting from its own `103` (all it has), computes `ŝ[1] = 103 + 0 + 3 = 106` — the decoder is now off by `+6`, and the gap **grew**, purely because the encoder's residual was computed against a reference the decoder never had. Continue this for a few more frames and the gap keeps widening every step, with no correction mechanism anywhere in the loop — this is drift, shown as an actual growing number rather than just asserted in prose.

**The closed-loop fix removes drift entirely, not just partially.** Because both encoder and decoder predict from `ŝ_f[n]`, and because `T⁻¹`, `Q⁻¹`, deblocking, and SAO are all specified **bit-exactly** in the standard (exact integer arithmetic, not floating point — floating-point rounding differences between "equivalent" implementations would themselves reintroduce drift), the encoder's internal `ŝ_f[n]` and the decoder's `ŝ_f[n]` are **identical at every step, forever**. The only distortion anywhere in the system is `q[n]` introduced at the *current* step. There is no accumulated term. This is a strong, provable engineering claim, not a hand-wave — and it is the actual mathematical content behind the slide-3 diagram.

### 11.5 The standardization consequence — decoders are specified; encoders are not

Because bit-exactness is what makes zero-drift possible, **every video standard (H.264, HEVC, VVC) is fundamentally a specification of the decoder**, down to exact integer arithmetic for every inverse operation and filter. The **encoder** side — how partitioning, prediction mode, and motion vectors are *chosen* (all of RDO, Chapter 17) — is left entirely unspecified by the standard. Any bitstream that a standard-conformant decoder can correctly decode is legal, however the encoder internally arrived at it.

**This is the single most important fact for framing your research contribution.** It means:
- Different encoders (x265, the HM reference software, a custom hardware encoder) can differ enormously in *how* they search and *how much energy/time* they spend, while producing bitstreams that any compliant decoder decodes identically.
- **Approximate/energy-efficient hardware innovation is entirely fair game on the encoder side** — an approximate transform or quantization datapath (Prof. Sen's exact research area, [[S10]](SOURCES.md#s10)) does not need to match any reference implementation bit-for-bit; it only needs to produce a legal, decodable bitstream. The decoder, receiving that bitstream, reconstructs perfectly using its own exact, standard-specified inverse operations — the "approximation" lives entirely in the encoder's search/computation, invisible to the standard.

### 11.6 In-loop filters are part of the loop, not an afterthought

A detail easy to glide past: deblocking and SAO (Chapter 16) are applied to `ŝ[n]` **before** it is stored as `ŝ_f[n]`, the actual reference used for future prediction — not merely as a "make the displayed picture prettier" cosmetic step. This means in-loop filtering has a genuine **compression** effect: a cleaner reference produces smaller residuals for every future frame that predicts from it, not just a subjectively nicer picture today. This is precisely why they're called *in-loop* filters, as opposed to *post* filters (applied only at display time, with zero effect on future coding efficiency).

### 11.7 A worked micro-example, fully computed

Suppose `s[n] = 100` (a single sample, for simplicity), `p[n] = 98`, so `e[n] = 2`. Suppose quantization is coarse enough that `Q(T(e[n]))`, when inverse-transformed and inverse-quantized, yields `ê[n] = 4` (a plausible outcome — quantization doesn't guarantee `ê[n] = e[n]`, only that it's a lossy approximation). Then:
```
ŝ[n] = p[n] + ê[n] = 98 + 4 = 102     (reconstructed value — note it's NOT 100!)
q[n] = s[n] − ŝ[n] = 100 − 102 = −2    (the coding error introduced at step n)
```
Both encoder and decoder now hold `ŝ[n] = 102` (after filtering, `ŝ_f[n]`, say unchanged at 102 for simplicity). At step `n+1`, **both** predict from 102, not from the true 100. This is by design — the −2 error is "priced in" once, and does not get worse at step `n+1` because both sides agree on 102 as the reference. Had the encoder instead predicted from the true `s[n]=100` while the decoder only had 102 available, step `n+1`'s residual computation would mismatch between the two sides, and the discrepancy would compound — exactly the drift failure mode of §11.4.

### Chapter 11 Quiz
1. Write the five defining equations of the closed-loop model from memory (§11.3).
2. Derive, without looking, why open-loop prediction (predicting from the original rather than the reconstruction) causes drift. Use the notation from §11.4.
3. Explain the control-theory analogy for why an unclosed predictive loop is "unstable," in your own words.
4. Why must `T⁻¹` and `Q⁻¹` be specified in exact integer arithmetic rather than floating point?
5. State precisely which part of a video codec is normatively specified by the standard, and which part is left to implementers. Why does this split exist?
6. Using the worked micro-example in §11.7, redo the arithmetic with `p[n]=50`, `s[n]=55`, and `ê[n]=3`. Compute `ŝ[n]` and `q[n]`.
7. Explain why deblocking/SAO being "in-loop" rather than "post-processing" has a genuine compression benefit, not just a cosmetic one.

**Further reading/watching:** [[S42]](SOURCES.md#s42) Netravali & Robbins — the origin paper (check Purdue library access) · [[S43]](SOURCES.md#s43) Musmann et al. — the hybrid-coding survey · [[S33]](SOURCES.md#s33) Yao Wang (NYU) — full loop diagram slides · [[S3]](SOURCES.md#s3) HEVC overview §II.

---

## Chapter 12 — Partitioning the Picture: CTU, CU, PU, TU

### 12.1 The motivating problem

A single fixed block size cannot efficiently represent every part of an image: a smooth sky region wants one large block (cheap signaling, one prediction covers a lot of area); a detailed, busy region wants many small blocks (precise prediction, at the cost of more signaling overhead per pixel). HEVC's answer is a **recursive, adaptive partitioning hierarchy** — four distinct structures, each answering a different question.

### 12.2 CTU — Coding Tree Unit: "where is the encoder currently working?"

The picture is tiled into **CTUs**, each up to **64×64 luma samples** (with matching, smaller chroma blocks per the 4:2:0 geometry from Chapter 2). This is HEVC's analog to H.264's 16×16 macroblock — but **16× the area**. Every downstream decision (CU, PU, TU) happens *inside* one CTU at a time; think of the CTU as the encoder's current "workspace."

### 12.3 CU — Coding Unit: "should this region split further?"

Within a CTU, a **recursive quadtree** decides whether to keep a region as one CU or split it into four smaller CUs, down to a minimum of **8×8**. Each CU is the unit at which the encoder commits to **intra vs. inter** mode. The split/no-split decision at every node is itself a rate-distortion decision (Chapter 17): splitting adds signaling bits (the split flag itself, plus more per-block prediction/residual info) but can reduce distortion — worth it only when the distortion reduction outweighs the added bit cost.

### 12.4 PU — Prediction Unit: "how is it predicted?"

Within a CU, one or more **PU shapes** define the prediction geometry: **2N×2N** (the whole CU, one prediction), **2N×N** (split horizontally into two), **N×2N** (split vertically into two), or **N×N** (split into four, available only for certain CU sizes and only for intra, or for inter at the smallest CU size). Intra PUs carry a chosen angular/Planar/DC mode; inter PUs carry a reference-frame index and motion vector. A 2N×N split, for instance, lets the top and bottom halves of a CU use different motion vectors — useful when an object boundary crosses horizontally through the block.

**Asymmetric Motion Partitioning (AMP)** — an inter-only refinement worth knowing by name: beyond the symmetric 2N×N/N×2N splits, HEVC allows *asymmetric* splits — **2N×nU, 2N×nD** (horizontal splits at 1/4 and 3/4 height instead of the middle) and **nL×2N, nR×2N** (vertical splits at 1/4 and 3/4 width) — for CU sizes 16×16 and larger. The motivation is the same as everywhere else in this chapter: an object boundary rarely falls exactly at the midline of a block, and AMP lets the PU boundary track a boundary that's, say, in the top quarter of the CU rather than forcing a symmetric split (or no split) to approximate it. Like every other partition choice in this book, whether AMP is used is decided by RDO (Chapter 17) weighing its extra signaling cost against the prediction improvement.

### 12.5 TU — Transform Unit: "how is the residual transformed?"

The **Residual Quad-Tree (RQT)** splits a CU's residual into TUs (**4×4 up to 32×32**) **independently** of how the PU divided the prediction — this is the detail that trips up almost everyone on first exposure. Why keep PU and TU independent? Because "the best way to predict this area" and "the best way to transform its error" are genuinely different optimization questions: a CU predicted as one large 2N×2N block can still have a residual that's spatially uneven (sharp near an edge, smooth elsewhere), which benefits from a *smaller* TU specifically in the busy sub-region, even though the whole CU was predicted as one unit.

### 12.6 The four-question mental model (the fastest way to keep this straight under pressure)

| Level | Question it answers | Analogy |
|---|---|---|
| CTU | Where is the encoder working? | The plot of land |
| CU | Should this region split? | How the plot is divided into parcels |
| PU | How is it predicted? | The prediction geometry drawn on each parcel |
| TU | How is the residual transformed? | The (independent) transform geometry drawn on each parcel |

### 12.7 A worked example: one CTU, four different needs

Picture a single 64×64 CTU containing: a smooth sky region (top-left), a sharp building edge (crossing diagonally), and fine roof-tile texture (bottom-right). A reasonable (illustrative) outcome:
- The sky region: **one large 32×32 CU**, `2N×2N` PU (single flat/Planar intra prediction), a correspondingly large **32×32 TU** (residual is nearly zero everywhere — cheap to code as one large near-empty transform block).
- The edge region: CU splits down to **16×16**, with a `2N×N` or `N×2N` PU split so each half can use the angular intra mode best matching the local edge direction, but a **smaller 8×8 TU** on the exact edge crossing (residual is spatially concentrated right at the edge, benefiting from finer transform granularity there).
- The texture region: CU splits all the way to **8×8**, each with its own PU/TU, since fine detail needs fine-grained everything.

**This is exactly what "one CTU can adapt to smooth and detailed regions" (a real slide in Presentation 2) is illustrating** — and now you can explain *why*, not just point at the picture.

**Running example, introduced here:** take that texture-region **8×8 CU**, predicted with a `2N×2N` PU using inter prediction (a motion vector pointing into the previous decoded frame). Call this **Block X**. Chapters 13 through 18 will follow Block X's actual numbers — transform output, quantized coefficients, entropy-coding cost, filtering, and RDO score — all the way through the pipeline, so you see one concrete block's full journey with real numbers attached at every stage, not eight disconnected examples.

### Chapter 12 Quiz
1. State, precisely, the question each of CTU/CU/PU/TU answers.
2. What is the maximum CTU size, and how does it compare to H.264's macroblock size (give the ratio)?
3. Explain why PU and TU trees are independent, with a concrete example of when this independence helps.
4. Why is the CU split/no-split decision described as "itself an RDO decision"?
5. Using the worked example in §12.7, explain why the edge region benefits from a *smaller* TU than its PU/CU size might suggest.
6. What specific problem does Asymmetric Motion Partitioning (AMP) solve that symmetric 2N×N/N×2N splits don't, and what governs whether an encoder actually uses it?

**Further reading/watching:** [[S35]](SOURCES.md#s35) vcodex — HEVC: An Introduction to High Efficiency Coding (the best single source for this whole chapter) · [[S3]](SOURCES.md#s3) HEVC overview §"Coding tree units and coding tree blocks."

---

## Chapter 13 — Transform Coding and the DCT

### 13.1 What the transform is actually doing, mathematically

The transform converts a block of *spatial-domain* residual values into a block of *frequency-domain* coefficients. HEVC uses an **integer approximation of the Discrete Cosine Transform (DCT)**, in sizes from 4×4 to 32×32 (plus a Discrete Sine Transform, DST, specifically for 4×4 intra luma residuals, which better fits the statistical properties of intra residuals near a prediction boundary).

**Why does this help compression at all?** Natural image/residual blocks are typically **spatially correlated** — nearby residual values tend to be similar in magnitude (a residual isn't random noise; it reflects imperfect but *structured* prediction error). The DCT is close to the **Karhunen–Loève Transform (KLT)** — the theoretically optimal decorrelating transform for signals with this kind of correlation structure — for a wide, well-studied class of natural image statistics, which is *why* the DCT (not some arbitrary transform) was chosen: it's a fixed, fast-to-compute, close approximation of the data-optimal transform, without needing to compute or signal a per-image-adaptive basis.

### 13.1b The actual formula, and HEVC's integer version of it

The 1D DCT-II — the specific DCT variant used here — transforms `N` spatial samples `x[0..N-1]` into `N` frequency coefficients `X[0..N-1]`:
```
X[k] = c(k) · Σ (n=0 to N-1) x[n] · cos( π/N · (n + 1/2) · k )
```
where `c(k)` is a normalization constant (`c(0) = √(1/N)`, `c(k>0) = √(2/N)`). `X[0]` (k=0, no oscillation) is the **DC coefficient** — proportional to the block's average — exactly the largest-magnitude value you saw at position (0,0) in §13.3's worked matrix. Higher `k` corresponds to higher spatial frequency (faster oscillation across the block), matching §13.2's "low-frequency energy, high-frequency near-zero" pattern directly to this formula: energy compaction *is* the statement that for correlated input, `X[k]` decays quickly as `k` increases.

A 2D block transform (what HEVC actually applies to an 8×8 or 16×16 residual block) is computed by applying this 1D transform along rows, then again along columns of the result — this separability is exactly why the complexity analysis in §13.4 talks about `N` 1D transforms rather than one large 2D operation.

**HEVC doesn't use the exact floating-point DCT-II above** — it uses an **integer approximation**, both for bit-exact reproducibility across all decoders (recall Chapter 11 §11.5: floating-point rounding differences between implementations would themselves cause drift) and for cheaper hardware/software implementation (integer arithmetic, no floating-point unit required). The real HEVC 4-point core integer transform matrix is:
```
      [ 64   64   64   64 ]
T4  = [ 83   36  -36  -83 ]
      [ 64  -64  -64   64 ]
      [ 36  -83   83  -36 ]
```
Row 0 (all identical values) is the DC/average-extracting row — matching `k=0`'s constant cosine term above. Row 1 (83, 36, -36, -83) is the lowest-frequency oscillating row — one sign change across the block. Notice this is exactly the DCT-II basis pattern, scaled and rounded to integers (the values 64, 83, 36 approximate `cos` values scaled by a fixed factor, chosen so the whole transform-plus-inverse-transform round-trip stays exact in integer arithmetic). The forward transform is computed as `T4 · x` (a matrix-vector product), and the inverse as `T4ᵀ · X` (scaled appropriately) — the same matrix, transposed, doing the reverse operation, which is precisely the `T⁻¹` in Chapter 11 §11.3's closed-loop equations.

### 13.2 Energy compaction — the core property being exploited

The DCT's key property for compression is **energy compaction**: for spatially correlated input, most of the signal's energy ends up concentrated in a small number of **low-frequency** coefficients (top-left of the coefficient matrix, by convention), while high-frequency coefficients (representing fine, rapidly-varying detail) are typically small or near-zero. **This step is (near-)lossless by itself** — it's a reorganization of information, not a discarding of it (the integer approximation introduces a small amount of unavoidable rounding, but this is negligible compared to what quantization, next chapter, deliberately discards).

### 13.3 A concrete worked example — Block X, continued from Chapter 12

Recall **Block X** from Chapter 12 §12.7 — the 8×8 texture-region CU, inter-predicted from the previous frame. Its residual (original minus motion-compensated prediction) is transformed by the 8×8 DCT into the following coefficient matrix (illustrative values, matching the pattern used in Presentation 2, slide 11 — we'll carry these exact numbers through quantization in Chapter 14, entropy coding in Chapter 15, and RDO in Chapter 17):
```
 42  -12   4   1   0   0   0   0
 -9    5  -2   0   0   0   0   0
  3   -1   1   0   0   0   0   0
  1    0   0   0   0   0   0   0
  0    0   0   0   0   0   0   0
  ... (remaining rows near-zero)
```
Notice: the largest-magnitude value (42) sits at position (0,0) — the **DC coefficient**, representing the block's average value. Moving away from the top-left, magnitudes shrink rapidly, and most of the matrix is already zero or near-zero *before any quantization has happened*. This is energy compaction, directly visible: **the transform has already done most of the work of "finding what matters"; quantization (Chapter 14) just has to decide how aggressively to discard what's left.**

### 13.4 Why larger transform sizes help (and why they're not free)

A larger transform (up to 32×32 in HEVC, versus H.264's typical maximum of 8×8) can capture correlation across a wider spatial area in one operation — beneficial for large, smooth residual regions where a big single transform is more efficient than several small ones. But larger transforms are also more computationally expensive (an N×N transform is roughly O(N² log N) with fast algorithms, versus doing many small transforms) and less able to adapt to spatially localized detail (recall Chapter 12 §12.7's edge example — a large TU there would "smear" the sharp edge's energy across the whole block instead of concentrating it locally). This is exactly why TU size is itself an adaptive per-region decision (the RQT, Chapter 12 §12.5), not a fixed global choice.

### Chapter 13 Quiz
1. What specific mathematical property of the DCT makes it well-suited to residual coding, and why (name the theoretically optimal transform it approximates)?
2. Define "energy compaction" and explain why the worked example in §13.3 demonstrates it.
3. Is the transform step lossy or lossless? Justify precisely, distinguishing "reorganizing information" from "discarding information."
4. Why does HEVC support transform sizes up to 32×32 rather than a single fixed size, and what's the tradeoff of using a larger transform?
5. Why does HEVC use a DST (not DCT) specifically for 4×4 intra luma residuals?
6. Write the DCT-II formula from memory and identify which term corresponds to the DC coefficient. Why does HEVC use an integer approximation of it rather than computing the exact floating-point version?

**Further reading/watching:** [[S38]](SOURCES.md#s38) vcodex — H.264/AVC 4×4 Transform and Quantization (same core concept, smaller scale) · [[S30]](SOURCES.md#s30) Reducible — visual DCT intuition (watch this one; DCT basis functions are much easier to grasp visually than algebraically) · [[S3]](SOURCES.md#s3) HEVC overview §"Transform coding."

---

## Chapter 14 — Quantization and the Rate–Quality Dial

### 14.1 The one deliberately lossy step

Recall Chapter 4's mapping: prediction, transform, and entropy coding are all (near-)lossless. **Quantization is where actual information is discarded**, and it is where the encoder trades compression ratio against reconstructed quality. If asked, in any context, "where does the quality loss in video compression actually happen?" — the precise, correct answer is: **quantization, and nowhere else in the pipeline.**

### 14.2 The mechanism

Quantization divides each transform coefficient by a **step size** and rounds to the nearest integer:
```
quantized_coefficient = round( coefficient / step_size )
```
Reconstruction (dequantization) multiplies back by the step size — but the rounding that occurred during quantization is **not recoverable**; the dequantized value is only an approximation of the original coefficient. Larger step sizes cause more coefficients (especially the already-small high-frequency ones from Chapter 13's energy compaction) to round to exactly **zero** — which is precisely why quantization and entropy coding work so well together: a stream with many zeros is extremely cheap for CABAC (Chapter 15) to represent.

### 14.2b Running example — quantizing Block X's coefficients

Recall Block X's transform output from Chapter 13 §13.3:
```
 42  -12   4   1   0   0   0   0
 -9    5  -2   0   0   0   0   0
  3   -1   1   0   0   0   0   0
  1    0   0   0   0   0   0   0
```
(remaining rows already zero from the transform stage). Apply a quantization step size of **8** (round to nearest integer, ties away from zero):
```
quantized[0][0] = round(42/8)  = round(5.25)  = 5
quantized[0][1] = round(-12/8) = round(-1.5)  = -2
quantized[0][2] = round(4/8)   = round(0.5)   = 1
quantized[0][3] = round(1/8)   = round(0.125) = 0
quantized[1][0] = round(-9/8)  = round(-1.125)= -1
quantized[1][1] = round(5/8)   = round(0.625) = 1
quantized[1][2] = round(-2/8)  = round(-0.25) = 0
quantized[2][0] = round(3/8)   = round(0.375) = 0
quantized[2][1] = round(-1/8)  = round(-0.125)= 0
quantized[2][2] = round(1/8)   = round(0.125) = 0
quantized[3][0] = round(1/8)   = round(0.125) = 0
```
Result:
```
  5  -2   1   0   0   0   0   0
 -1   1   0   0   0   0   0   0
  0   0   0   0   0   0   0   0
  0   0   0   0   0   0   0   0
```
**Only 5 of the 64 coefficients survive quantization as nonzero** (5, -2, 1, -1, 1). This is the concrete, numeric version of "quantization creates zeros" — and it's precisely what makes Block X cheap for CABAC to entropy-code in Chapter 15's continuation of this same example. Notice, too, that this is a *lossy, one-way* operation: given only the quantized value `5`, you cannot recover whether the original coefficient was exactly 42, or anywhere from 36 to 43 (the range that also rounds to 5 at step size 8) — the specific original value is genuinely, irrecoverably gone. This is the concrete meaning of "quantization is where information is discarded," stated in Chapter 14 §14.1 and now visible in real numbers.

### 14.3 QP — the rate-quality dial

The **Quantization Parameter (QP)** controls the step size on a logarithmic-ish scale (each increment of QP by roughly 6 in HEVC/H.264 approximately doubles the step size — a detail worth knowing precisely if pressed): higher QP → larger step size → more coefficients zeroed → fewer bits, more distortion. Lower QP → smaller step size → more coefficients survive → more bits, less distortion.

In practical encoders (e.g., x265, [[S21]](SOURCES.md#s21)), users more often set **CRF (Constant Rate Factor)** rather than a fixed QP directly — CRF targets a consistent *perceptual* quality level by letting QP vary adaptively per region/frame (spending more bits where the eye would notice loss, per Chapter 4's perceptual-redundancy framing, and fewer where it wouldn't), rather than a single global QP applied everywhere uniformly.

### 14.4 Perceptual masking — why quantization can be *more* aggressive in some regions "for free"

Human perception is less sensitive to quantization error in **busy/textured regions** (the error is masked by the surrounding high-frequency detail) than in **smooth regions** (where any quantization error stands out as visible banding or blockiness). This connects directly back to Chapter 4's perceptual redundancy: well-designed encoders exploit this by applying relatively coarser effective quantization in high-detail regions and finer quantization in smooth regions, for the *same* overall bitrate — genuinely getting "more compression, same perceived quality" rather than a strict lossless tradeoff.

### 14.5 Connection to Prof. Sen's research (previewed here; full detail in Chapter 20)

Quantization's core arithmetic — divide by a step size, round — is deliberately simple, but computing it exactly (true division) is relatively expensive in low-power hardware. Prof. Sen's group's work [[S10]](SOURCES.md#s10) approximates this division using **bit-shift operations** (much cheaper in hardware than true division) combined with **loop perforation** (skipping some computation entirely for parts of the datapath judged less impactful) and **precision scaling**, achieving roughly **36% energy savings for about 2% quality loss** in their reported results, specifically targeting this exact block of the pipeline. This is the single clearest, most concrete link between the standard HEVC pipeline you're learning in this chapter and the actual hardware-research contribution you'll be discussing with Prof. Sen.

### Chapter 14 Quiz
1. Precisely state which stage of the HEVC pipeline is lossy, and why the other stages are not (or only negligibly so).
2. Define QP and explain, quantitatively (the "~6 = 2×" rule), how it relates to step size.
3. What is CRF, and how does it differ from setting a single fixed QP for an entire video?
4. Explain "perceptual masking" in your own words, and why it lets an encoder be "more aggressive for free" in textured regions.
5. Describe Prof. Sen's approximate-quantization technique (§14.5) in your own words: what's being approximated, how, and at what reported cost/benefit.

**Further reading/watching:** [[S38]](SOURCES.md#s38) vcodex — Transform and Quantization · [[S10]](SOURCES.md#s10) Prof. Sen's paper — read this one in full now, not just the abstract; you have all the pipeline context needed to understand it deeply at this point in the book.

---

## Chapter 15 — CABAC: Entropy Coding

### 15.1 What's left to compress after quantization

After quantization, the encoder has: mostly-zero coefficient blocks, split flags (from Chapter 12's quadtrees), prediction mode indices, motion vectors, and various other syntax elements. None of these are uniformly distributed — recall Chapter 4's "statistical redundancy." CABAC (**Context-Adaptive Binary Arithmetic Coding**) is the final, lossless stage that exploits this non-uniformity to pack everything into the smallest possible number of bits.

### 15.2 The three stages, in detail

**1. Binarization** — every syntax element (a split flag, a motion vector component, a transform coefficient level) is converted into a string of binary decisions called **bins**, using element-specific binarization schemes (e.g., unary, truncated unary, exp-Golomb-like codes depending on the element) chosen because they match the *expected distribution* of that particular syntax element well (e.g., small motion vector differences are far more common than large ones, so the binarization is denser for small values).

**2. Context modeling** — each bin is assigned a **context** (based on already-coded neighboring information — e.g., whether the left/above blocks had a similar split decision) which determines an estimated probability for that bin being 0 or 1 (e.g., P(0)=0.86, P(1)=0.14 for a bin that's usually 0 in this context). Crucially, **these probabilities are adaptive**: they update continuously based on actual observed bin values as coding proceeds, so the model tracks the real, local statistics of *this specific video*, not a fixed global assumption.

**3. Arithmetic coding** — the estimated probabilities feed an arithmetic coder, which packs the sequence of bins into a compact bitstream where a bin predicted with high confidence (say P(0)=0.99) costs a small fraction of a bit, while a genuinely uncertain bin (P≈0.5) costs close to a full bit — this is the mechanism that lets CABAC approach the true entropy of the symbol stream far more closely than a fixed-length or simple variable-length (e.g., Huffman-style) code could.

### 15.3 Why arithmetic coding beats fixed/Huffman coding here

Huffman coding assigns a whole number of bits to each symbol — the best it can do for a highly probable symbol is 1 bit, even if the symbol's true information content is much less than 1 bit (e.g., if a symbol occurs with probability 0.95, its ideal code length by Shannon's formula is `-log2(0.95) ≈ 0.074` bits — nowhere close to achievable with any whole-bit code). Arithmetic coding can represent **fractional bits per symbol**, letting it get arbitrarily close to the true entropy limit for skewed distributions — exactly the kind of distribution CABAC's contexts are designed to produce (many bins strongly biased toward 0, post-quantization).

### 15.3b Bypass mode — the escape hatch from full context modeling

Not every bin gets the full three-stage treatment. For bins whose probability is already known to be close to 50/50 (e.g., the sign bit of a coefficient, or lower-order bits of large coefficient magnitudes, which carry little exploitable statistical skew), CABAC uses a **bypass mode**: the bin skips context modeling and probability estimation entirely and is coded directly at an assumed fixed 0.5 probability. This matters for two reasons worth holding onto: (1) it's a direct efficiency admission — modeling a genuinely unpredictable bin would waste computation for no compression benefit, so the design simply doesn't bother; (2) it matters enormously for **hardware and parallelism** (Chapter 19), because bypass bins have no context-update dependency chain — they can be processed at a much higher throughput than regular (context-coded) bins, and encoder/decoder hardware designs specifically exploit this by batching bypass bins separately from the serial regular-mode bin stream.

### 15.4 The cost: CABAC is inherently serial

Each bin's probability model depends on **previously coded bins and contexts** — meaning bin `k` genuinely cannot be decoded until bin `k-1` (and the context state it updated) is known. This makes CABAC **fundamentally sequential**, unlike, say, independent per-block operations that parallelize trivially. This single property is *the* reason parallel decoding tools — **Tiles** (dividing the picture into independently-decodable rectangular regions) and **Wavefront Parallel Processing (WPP)** (allowing rows to start decoding slightly staggered, once enough of the row above is done) — exist at all in HEVC: they're a direct engineering response to CABAC's serial bottleneck, letting a multi-core decoder/encoder exploit parallelism *despite* CABAC's row-to-row/bin-to-bin dependency. This connects forward to Chapter 19's "parallel processing" research direction.

### 15.4b Running example — entropy-coding Block X

Recall Block X's quantized coefficients from Chapter 14 §14.2b: `5, -2, 1, -1, 1`, all other 59 positions zero. Qualitatively, CABAC's job here is cheap by design: a `coded_block_flag` (one bin, likely context-coded, signaling "this 8×8 TU has at least one nonzero coefficient") followed by a short run of `significant_coeff_flag` bins concentrated near the top-left (matching the energy-compaction pattern from Chapter 13), then the five nonzero magnitudes and their signs — magnitudes via context-coded bins (since small-magnitude coefficients are common and skewed), signs via **bypass mode** (§15.3b — a coefficient's sign carries no useful statistical skew to model). Contrast this with a hypothetical lower-QP encoding of the same block that survived quantization with, say, 40 nonzero coefficients instead of 5: CABAC would need vastly more significant-coefficient and magnitude bins — this is the concrete link between Chapter 14's QP choice and Chapter 17's rate term `R`, made numeric: **fewer surviving coefficients doesn't just mean "less data" abstractly, it means measurably fewer CABAC bins to code, which is exactly what R counts.**

### Chapter 15 Quiz
1. Name and describe the three stages of CABAC in order.
2. Why is context "adaptive," and what specifically does it adapt based on?
3. Using Shannon's formula, compute the ideal code length (in bits) for a symbol with probability 0.98. Explain why a Huffman code cannot achieve this.
4. Precisely explain why CABAC is inherently serial — what specific dependency causes this?
5. What are Tiles and WPP, and what specific problem (named in this chapter) do they exist to solve?
6. What is bypass mode, why is it used for certain bins (like coefficient signs), and why does it matter specifically for hardware throughput?
7. Using Block X's running example, explain concretely why a lower QP (more surviving coefficients) increases the rate term `R` in Chapter 17's RDO formula — not just abstractly, but in terms of what CABAC actually has to code more of.

**Further reading/watching:** [[S36]](SOURCES.md#s36) vcodex — CABAC deep dive · [[S37]](SOURCES.md#s37) Wikipedia — CABAC (good for the binarization-scheme details) · [[S3]](SOURCES.md#s3) HEVC overview §"Entropy coding."

---

## Chapter 16 — In-Loop Filters: Deblocking and SAO

### 16.1 Why filtering is needed at all

Block-based coding (Chapter 12) combined with lossy quantization (Chapter 14) introduces visible artifacts specifically **at block boundaries** — because each block is quantized somewhat independently, adjacent blocks can end up with slightly different reconstructed values along their shared edge even where the true underlying image is smooth, producing visible "blockiness." Two filters, applied **in order**, address this and related systematic errors.

### 16.2 Deblocking filter — first in order

The deblocking filter selectively smooths pixels near block edges, with filter *strength* adapted based on: how strong the underlying block boundary discontinuity actually is (avoiding over-smoothing genuine image edges that happen to fall on a block boundary), the prediction mode used (intra edges are typically filtered more aggressively, since intra blocks tend to produce more visible blocking), and the QP used (higher QP → generally stronger filtering, since higher QP produces more pronounced blocking artifacts to correct). This targets specifically the block-edge discontinuity artifact.

### 16.3 SAO — Sample Adaptive Offset (new in HEVC, not present in H.264)

SAO runs **after** deblocking and adds small, explicitly signaled **offset values** to reconstructed samples, classified into one of two modes:
- **Band Offset (BO)** — samples are grouped into bands by intensity value (e.g., dividing the 0-255 range into 32 bands), and a signaled offset is added per band — corrects systematic intensity-range errors (e.g., a whole tonal range consistently reconstructed slightly too dark).
- **Edge Offset (EO)** — samples are classified by their local edge pattern relative to neighbors (e.g., "local peak," "local valley," "on a rising/falling edge") in one of four directions, and a signaled offset corrects systematic errors specific to that edge shape (e.g., ringing artifacts that consistently appear near sharp edges after transform/quantization).

**Why SAO is a genuinely new HEVC contribution**, not just "more deblocking": deblocking specifically targets *edge discontinuities introduced by block boundaries*; SAO targets *systematic reconstruction error patterns* that deblocking doesn't address at all (ringing near strong edges, banding within smooth-but-not-boundary-adjacent regions) — the two filters are complementary, not redundant, which is precisely why HEVC keeps deblocking and adds SAO on top, rather than replacing one with the other.

### 16.4 Why "in-loop" is the key word (recall Chapter 11 §11.6)

Both filters run **before** the frame is stored as a reference for future prediction, not merely before display. A cleaner reference frame produces smaller residuals for every subsequent frame predicted from it — meaning these filters have a genuine, compounding **compression benefit** across the whole GOP, not just a one-time subjective quality improvement on the current frame. This is worth restating because it's the detail that most clearly separates "in-loop filter" from "post-processing filter" (the latter, applied only at display, would have zero effect on bitrate/future coding efficiency, only on how the final displayed picture looks).

### 16.4b Running example — filtering Block X

Block X (Chapter 12's texture-region 8×8 CU) sits inside a busy, high-detail area of the CTU — exactly where deblocking is applied conservatively (§16.2: strong genuine texture edges shouldn't be smoothed away as if they were block-boundary artifacts) and where SAO's **Edge Offset** mode is more likely to fire than Band Offset, since fine texture produces exactly the kind of local peak/valley/edge patterns EO is designed to correct, rather than the broad intensity-band errors BO targets. Contrast this with the sky-region CU from Ch. 12 §12.7 (large, flat, smoothly predicted) — there, SAO's **Band Offset** would be the more relevant mode, since a large flat area is more prone to a uniform tonal-range error than to edge-shaped ringing. This is the concrete version of "SAO adapts to local content," not just an abstract description of the two modes.

### Chapter 16 Quiz
1. Why does block-based lossy coding specifically produce visible artifacts at block boundaries?
2. What factors determine deblocking filter strength at a given edge?
3. Describe Band Offset and Edge Offset, and give an example artifact each is designed to correct.
4. Why is SAO described as "complementary to," not "a replacement for," deblocking?
5. Explain precisely why "in-loop" (as opposed to "post-processing") filtering has a genuine compression effect, referencing Chapter 11 if needed.

**Further reading/watching:** [[S39]](SOURCES.md#s39) vcodex — H.264/AVC Loop Filter (deblocking concept, pre-SAO) · [[S40]](SOURCES.md#s40) Wikipedia — Deblocking filter (video) · [[S3]](SOURCES.md#s3) HEVC overview §"In-loop filters" (the primary and best source specifically for SAO, since it's HEVC-specific and not covered by the H.264-era sources above).

---

## Chapter 17 — Rate–Distortion Optimization

### 17.1 The decision engine behind every choice in Chapters 12–16

Every "should this CU split," "which PU shape," "which intra mode," "which TU size," "what QP for this region" decision from the preceding chapters is made by the **same underlying framework**: Rate-Distortion Optimization (RDO). This chapter is the "how the encoder actually decides" chapter that ties Part III together.

### 17.2 The Lagrangian cost function

```
J = D + λR
```
where:
- `D` = **distortion** — how different the reconstructed block is from the original (commonly measured as sum of squared error, SSE, or sum of absolute differences, SAD, or increasingly perceptually-weighted metrics)
- `R` = **rate** — the number of bits required to code this choice (split flags + mode signaling + residual bits, all of it)
- `λ` (lambda) = a **Lagrange multiplier** that sets the exchange rate between distortion and rate — tied to QP (higher QP, i.e., a lower target quality/bitrate operating point, generally uses a larger λ, weighting rate more heavily relative to distortion) [[S44]](SOURCES.md#s44)

The encoder evaluates `J` for every candidate choice at a given decision point and **picks the option with the lowest J** — this is the actual mechanism, repeated at every partitioning/mode/size decision in the encoder, that makes HEVC's huge combinatorial option space (Chapter 12's nested CTU/CU/PU/TU choices, Chapter 7's 35 intra modes, Chapter 8's motion search candidates) tractable to search at all.

### 17.3 Why a Lagrangian formulation, specifically (the theory, not just the formula)

This connects to formal rate-distortion theory (Shannon): for a given source and a given fidelity criterion, there exists a well-defined rate-distortion function `R(D)` describing the minimum achievable rate for a given maximum distortion. Directly minimizing "rate subject to a distortion constraint" (or vice versa) is a **constrained optimization problem**; the Lagrangian `J = D + λR` converts it into an **unconstrained** one, where sweeping `λ` traces out different points along the rate-distortion tradeoff curve. This is the standard technique (Sullivan & Wiegand's tutorial [[S44]](SOURCES.md#s44) is the canonical reference) for making rate-distortion-constrained decisions tractable at the granularity of "should this individual 16×16 block split," rather than only at the level of a whole-frame or whole-video budget.

### 17.4 The worked example (memorize this arithmetic — it appears directly in Presentation 2)

Three candidate encodings of the same region, with λ=2 — and, continuing the running example, **Option B below is Block X**: the 8×8 CU with the specific quantized coefficients from Chapter 14 §14.2b and the specific CABAC bin cost sketched in Chapter 15 §15.4b. Options A and C represent what the encoder *would have gotten* had it chosen a larger, less-split CU (A) or split even further (C) for that same image region instead:

| Option | Description | D | R | J = D + 2R |
|---|---|---|---|---|
| A | Large CU, simple mode | 18 | 6 | 18 + 12 = **30** |
| B | Smaller CUs, better prediction | 8 | 10 | 8 + 20 = **28** ← lowest, chosen |
| C | Deep split, many flags | 5 | 16 | 5 + 32 = **37** |

**The point of this example**: Option B wins even though **C has strictly lower distortion (5 < 8)** and **A has strictly fewer bits (6 < 10)** — RDO doesn't pick the least-distorted option or the cheapest option individually; it picks the best **combined** cost. This is worth being able to say out loud, unprompted, because it's exactly the kind of nuance that shows real understanding versus memorized slide content: *naive intuition says "more splitting = better quality, so C should win, or fewer bits = better efficiency, so A should win" — but RDO's whole point is that neither intuition alone determines the right answer; only the weighted combination does.*

### 17.5 Practical computational cost, and the segue to Part IV

Evaluating `J` for every candidate at every decision point, across every CTU in every frame, is enormously expensive if done exhaustively — this cost is *precisely* why fast mode-decision heuristics, early termination, and hardware acceleration (Chapter 19) exist: they are all, at their core, techniques for **approximating the RDO search** without exhaustively computing `J` for every possible combination of partition/mode/size choices.

### Chapter 17 Quiz
1. Write the Lagrangian RDO cost function from memory and define every term.
2. Explain why converting a constrained rate-distortion problem into an unconstrained Lagrangian is useful in practice.
3. Redo the worked example in §17.4 with λ=3 instead of 2. Which option wins now, and why does the answer change?
4. Explain, in your own words, why RDO is described as picking the best *combined* cost rather than the best individual metric.
5. Why is exhaustive RDO search computationally prohibitive, and what does this motivate (name the connection to Part IV)?

**Further reading/watching:** [[S44]](SOURCES.md#s44) Sullivan & Wiegand — Rate-Distortion Optimization for Video Compression (the canonical tutorial; read this one closely, it's short and directly authoritative) · [[S41]](SOURCES.md#s41) Wikipedia — Rate-distortion optimization (quick reference).

---

## Chapter 18 — One Block's Complete Journey

### 18.1 The synthesis chapter

This chapter adds no new concepts — it exists purely to **narrate the entire Part III pipeline as one continuous story**, the way you should be able to explain it in a hallway conversation or a defense, start to finish, without notes.

### 18.2 The full narration

> *"The encoder picks a CTU — its current 64×64 workspace [Ch. 12]. It recursively decides how to split that CTU into CUs, weighing whether more detail is worth the extra split-flag bits [Ch. 12, 17]. For each CU, it chooses a PU shape and a prediction — either intra, using one of 35 spatial modes and already-decoded neighbors [Ch. 7], or inter, using a motion vector found by searching previous (and possibly future, for B-frames) decoded frames [Ch. 6, 8]. It subtracts that prediction from the original to get a residual [Ch. 5]. It transforms the residual, independently of the PU shape, using a TU size chosen to match how the error is spatially distributed, concentrating most of the residual's energy into a few low-frequency coefficients [Ch. 12, 13]. It quantizes those coefficients — the one genuinely lossy step in the whole pipeline — rounding many of them to zero, with the aggressiveness controlled by QP [Ch. 14]. It entropy-codes everything — split flags, modes, motion vectors, and the mostly-zero coefficient stream — using CABAC's three-stage binarize/context-model/arithmetic-code process, exploiting exactly how non-uniform that stream now is [Ch. 15]. And in parallel, it reconstructs its own output — inverse-quantizing, inverse-transforming, adding the prediction back, then running deblocking and SAO — so that the reference it stores for future prediction is bit-identical to what the real decoder will eventually have, preventing drift [Ch. 11, 16]. Every single one of these decisions — CU split, PU shape, intra mode, motion vector, TU size, QP — was made by evaluating `J = D + λR` and picking the lowest-cost option [Ch. 17]."*

### 18.2b Block X's complete numeric journey

The narration in §18.2 is the conceptual version; here is the same journey with the actual running-example numbers collected in one place:

| Stage | Chapter | Block X's actual result |
|---|---|---|
| Partitioning | 12 | CTU splits down to an 8×8 CU in the texture region; `2N×2N` PU, inter-predicted |
| Transform | 13 | Coefficients: 42, -12, 4, 1 / -9, 5, -2, 0 / 3, -1, 1, 0 / 1, 0, 0, 0 (rest zero) |
| Quantization | 14 | Step size 8 → surviving coefficients: 5, -2, 1, -1, 1 (only 5 of 64 nonzero) |
| Entropy coding | 15 | `coded_block_flag` + a short run of significant-coefficient/magnitude bins (context-coded) + 5 sign bits (bypass-coded) — cheap, because quantization left so few nonzero coefficients |
| Filtering | 16 | Conservative deblocking (genuine texture, not a boundary artifact); SAO Edge Offset likely selected, matching the local peak/valley pattern typical of texture |
| RDO | 17 | This is **Option B** in the worked table: D=8, R=10, J=8+2(10)=28 — the lowest cost among the three candidate encodings of this region, which is *why* the encoder committed to this exact 8×8 split/PU/quantization combination in the first place |

**The point of building this table:** every number in it is causally connected to the one before it — the CU split size determined the transform size, the transform's energy compaction determined how few coefficients survived quantization, the surviving coefficient count determined the entropy-coding cost (the `R` in RDO), and the *combination* of that `R` with the resulting distortion `D` is *why* this particular partition/prediction/quantization choice was selected over the alternatives in the first place. This is what "one block's journey" means with the abstraction stripped away.

### 18.3 Why this narration is the actual test of understanding

If you can say the paragraph above fluently, in your own words, without it sounding memorized, you have genuinely internalized Part III — not because you've memorized 8 chapters of facts, but because the paragraph only makes sense if you understand *why* each stage exists and *how* it depends on the stage before it. This is deliberately the bar Presentation 2's slide 17 sets, and it's the right bar for a PhD-level command of the material.

### Chapter 18 Quiz (no new content — a comprehensive checkpoint)
1. Reproduce the full narration in §18.2 from memory, in your own words, in under 2 minutes spoken aloud.
2. For each stage in the narration, name which specific redundancy type (Chapter 4) it exploits.
3. Identify every point in the narration where a rate-distortion decision (Chapter 17) is being made, even implicitly.
4. Identify the exact point in the narration where drift would occur if the closed-loop principle (Chapter 11) were violated.
5. Reproduce the Block X table in §18.2b from memory, without looking — including the actual numbers, not just the stage names. Then explain, in one sentence per row, *why* that stage's result follows causally from the row above it.

**Further reading/watching:** re-read [[S3]](SOURCES.md#s3) HEVC overview in full at this point — with all of Part III behind you, the entire paper should now read as familiar material rather than new information; this is a good self-check in itself.

---

*(End of Part III.)*

---

# Part IV — The Research Frontier

*(Where the standard ends and your PhD topic begins.)*

## Chapter 19 — Searching HEVC's Decision Space Efficiently

### 19.1 Restating the problem precisely

Chapter 17 established that every encoder decision is chosen by minimizing `J = D + λR` over a candidate set. Chapters 12, 7, and 8 established just how large that candidate set is: recursive CU splits down to 8×8, up to 35 intra modes per PU, a wide motion-vector search space per inter PU, independent TU sizes per residual region — multiplied across every CTU in every frame of a video. **Exhaustively evaluating `J` for every combination is computationally intractable at real-time speeds**, and the four research directions below are, without exception, different strategies for approximating that search well enough, fast enough, and (increasingly) cheaply enough in energy.

### 19.2 Direction 1 — Fast mode and partition search

Instead of evaluating every CU/PU/TU/mode combination, encoders use **early termination** (e.g., if a large CU's RDO cost is already very low, skip evaluating its smaller-split children — a strong empirical heuristic, since a good match rarely needs further splitting) and increasingly **learned heuristics** — machine-learning models trained to predict, from cheap-to-compute features (block variance, gradient statistics, neighboring blocks' chosen modes), which candidates are worth fully evaluating with the expensive true RDO cost function, and which can be skipped with high confidence. This shrinks the search space *algorithmically*, without touching hardware.

### 19.3 Direction 2 — Parallel processing

Recall Chapter 15 §15.4: CABAC is inherently serial. **Tiles** partition a picture into independently decodable rectangular regions (each with its own CABAC state), enabling genuine multi-core parallelism at the cost of a small compression efficiency loss (context statistics can't be shared across tile boundaries, and tile boundaries themselves may need filtering treatment). **Wavefront Parallel Processing (WPP)** takes a gentler approach: rows can start decoding once the row above has progressed far enough (typically two CTUs ahead), preserving most of CABAC's context-sharing efficiency while still enabling a diagonal-wavefront pattern of parallel execution across multiple cores. Both are *engineering responses to a specific bottleneck identified in Chapter 15*, not independent inventions.

### 19.4 Direction 3 — Hardware acceleration

Dedicated silicon (ASICs, or accelerator blocks within an SoC) for the most computationally dominant stages — motion estimation (Chapter 8 §8.5's search cost), the transform (Chapter 13), and entropy coding (Chapter 15) — trades general-purpose flexibility for large speed and energy efficiency gains, since a fixed-function or semi-programmable hardware datapath can be far more efficient than a general CPU/GPU executing the equivalent software. This is the traditional, longest-standing research direction of the four, and the direct predecessor context for approximate computing (below).

### 19.5 Direction 4 — Approximate computing (the direction most relevant to your PhD)

Rather than computing each pipeline stage *exactly* and then optimizing how fast/parallel that exact computation runs, approximate computing asks: **does this stage need to be computed exactly at all, given how much error the rest of the system (and human perception) can already tolerate?** Concretely, for the transform and quantization datapath specifically (Chapters 13–14):
- **Bit-shift approximation of division** — quantization's divide-by-step-size operation is replaced by cheaper shift operations, since shifts are dramatically cheaper in hardware than true division circuits.
- **Loop perforation** — deliberately skipping some fraction of computation (e.g., some coefficient computations in a less-impactful part of the transform) when the resulting error is judged acceptable.
- **Precision scaling** — using fewer bits of precision than the "exact" specification would use, wherever the extra precision doesn't meaningfully change the final visual/coding outcome.

Recall from Chapter 11 §11.5 the crucial standardization fact that makes this entire research direction *legal and practical*: **the standard does not require the encoder's internal computation to be exact or to match any reference implementation** — only the final bitstream needs to be legally decodable. This is what allows Prof. Sen's group to report real measured results (≈36% energy savings for ≈2% quality loss, [[S10]](SOURCES.md#s10)) on real, standard-conformant hardware, without violating or needing any special-case exception to the HEVC standard.

Approximate computing is not unique to video coding — it's an established, broader systems/architecture research area (bit-shift approximation, loop perforation, precision scaling, and voltage overscaling all appear across signal processing, machine learning inference, and general computing, not just codecs) [[S49]](SOURCES.md#s49). Situating Prof. Sen's work within that broader literature, rather than treating it as a codec-specific trick, is worth doing explicitly in a research conversation — it signals you understand the technique as a general principle applied to this specific domain, not a one-off.

### 19.6 How the four directions relate to each other

They are not competing — they attack different parts of the same underlying problem, and real systems often combine several:

| Direction | Attacks | Typical form |
|---|---|---|
| Fast mode search | *Algorithmic* search-space size | Software heuristics / ML |
| Parallel processing | *Serial dependency* bottleneck (specifically CABAC) | Software/architecture (Tiles, WPP) |
| Hardware acceleration | *General-purpose compute overhead* | Fixed-function/ASIC silicon |
| Approximate computing | *Exactness requirement itself* | Circuit-level approximation |

### Chapter 19 Quiz
1. Restate precisely why exhaustive RDO search is intractable, using Chapters 12 and 17's search-space description.
2. Explain the difference between Tiles and WPP, and why WPP generally preserves more compression efficiency.
3. Why is hardware acceleration described as "trading flexibility for efficiency"?
4. Name the three specific techniques Prof. Sen's group uses for approximate quantization (§19.5), and briefly describe each.
5. Why does the "decoders are specified, encoders are not" fact from Chapter 11 §11.5 make approximate computing on the encoder side standard-legal?
6. Explain, using the table in §19.6, why these four directions are complementary rather than mutually exclusive.

**Further reading/watching:** [[S10]](SOURCES.md#s10) Prof. Sen's approximate DCT/quantization paper (if you haven't yet read it in full, this is the moment — every concept it references is now familiar) · search YouTube/IEEE Xplore for "HEVC fast mode decision CTU" and "wavefront parallel processing HEVC" for implementation-level detail.

---

## Chapter 20 — Where This Connects to Prof. Sen's SPARC Lab

### 20.1 The SPARC Lab's actual research center of gravity

It's worth being precise and honest about this, since overstating the connection would be a mistake in a real research conversation: Prof. Shreyas Sen's primary research areas are **mixed-signal circuit design, the Internet of Bodies (body-area sensing/communication), and hardware security** [[S17]](SOURCES.md#s17). Video/image compression research appears within the lab specifically through the lens of **energy-efficient, approximate hardware for resource-constrained sensing** — it's one application domain the lab's broader circuit-design expertise gets applied to, not the lab's central identity. Framing it any other way risks sounding like you haven't actually read the lab's work carefully.

### 20.2 The specific papers, and how each maps to this book

- **[[S10]](SOURCES.md#s10) — "Approximate DCT and Quantization Techniques for Energy-Constrained Image Sensors"** (IEEE TCAD, 2024). This is the direct, load-bearing bridge paper: it targets exactly the transform+quantization block of Chapters 13–14, using exactly the three techniques described in Chapter 19 §19.5, achieving ~36% energy savings for ~2% quality loss, at roughly 15 µW for the DCT+quantization stages. **Read this paper closely, more than once — it is, in effect, "Chapter 13 + Chapter 14 + Chapter 19, but as a real measured hardware result."**
- **[[S11]](SOURCES.md#s11) — "dAJC" (analog-to-MJPEG converter)** (IEEE JSSC 2025 / CICC 2023). A different, more radical strategy: compress **in the analog domain**, before the signal is even fully digitized — a philosophically different approach from HEVC's "digitize everything, then compress digitally" pipeline (all of Chapters 2–18). Worth contrasting explicitly: this paper asks "can we skip most of the digital pipeline altogether for constrained sensor use cases," rather than "can we make the digital pipeline cheaper," which is a genuinely different research question from S10's.
- **[[S12]](SOURCES.md#s12), [[S13]](SOURCES.md#s13), [[S14]](SOURCES.md#s14)** — related in-sensor, ultra-low-power imaging/compute work, useful for understanding the lab's broader hardware philosophy (do as much processing as early/cheaply as possible, close to the sensor) even where they don't map onto a specific HEVC pipeline stage the way S10 does.
- **For broader field context beyond this one lab**, [[S50]](SOURCES.md#s50) (Correa et al., complexity-controlled HEVC encoding for power-constrained devices) shows the same "make HEVC encoding cheaper for constrained hardware" problem being worked from a different angle (algorithmic complexity control rather than circuit-level approximation) by a different group — useful for demonstrating in a research conversation that you know this is an active, multi-group research question, not an area only one lab works in.

### 20.3 Two candidate research framings, and the open question to raise with Prof. Sen directly

Given everything above, there are at least two distinct, legitimate ways your PhD topic could sit relative to HEVC and the lab's existing work:

1. **Extend the approximate-computing angle (S10) to more of the HEVC pipeline** — beyond transform/quantization, could motion estimation (Chapter 8, the most compute-heavy encoder stage) or CABAC's context modeling (Chapter 15) similarly tolerate approximation for energy savings, given the same "encoder-side computation is unconstrained by the standard" argument from Chapter 11 §11.5?
2. **Bring the "compress at/near the sensor" philosophy (S11–S14) into a standardized-codec context** — rather than a bespoke analog-to-MJPEG pipeline, could similar early/analog/in-sensor techniques feed into (or precondition) a standard-conformant HEVC/VVC bitstream, combining the lab's existing sensor-side efficiency work with the interoperability benefits of a real, widely-decodable standard?

**The honest, well-prepared question to actually ask Prof. Sen** (an upgraded version of the one drafted in [papers/README.md](papers/README.md)): *"I've read S10 closely — the transform+quantization approximation is a clean fit because the standard only constrains the decoder, not the encoder's internal computation. Is the lab's interest in extending that same approximate-encoder philosophy to other unconstrained stages of a standardized codec like HEVC/VVC (motion estimation, mode search), or is the more active direction closer to S11–S14's philosophy of doing more work before/outside the standardized pipeline entirely?"* This single question demonstrates you've read the actual papers, understand the standard's structural constraint that makes the research legal in the first place, and can see that these are two different, both-legitimate directions rather than one obvious path.

### Chapter 20 Quiz
1. State precisely what the SPARC Lab's primary research focus is, and where video compression fits relative to that focus.
2. For S10, name the specific pipeline stage it targets, the three techniques used, and the reported quantitative result.
3. Contrast S10's approach with S11's, in terms of *where in the pipeline* each intervenes (digital vs. analog, which chapter's material each maps onto).
4. Articulate, in your own words, the two candidate research framings from §20.3, and explain why each is a legitimate but distinct direction.
5. Explain why the "decoder is specified, encoder is not" fact (Chapter 11 §11.5) is the structural reason approximate-encoder research is viable at all — connect this explicitly back to Chapter 11.

**Further reading/watching:** [[S15]](SOURCES.md#s15) SPARC Lab publications (check for anything more recent than what's cataloged in this book) · [[S18]](SOURCES.md#s18) Google Scholar profile · re-read [papers/README.md](papers/README.md)'s "how to talk about these in your first meeting" section now that you have the full technical grounding to back it up.

---

*(End of Part IV.)*

---

# Part V — Completing the Toolkit

*(Three chapters that fill the gaps a rigorous self-review found: how coding-efficiency claims are actually measured, the bitstream-structure vocabulary assumed everywhere else in this book, and how to read the hardware/approximate-computing literature you'll be citing critically rather than at face value.)*

## Chapter 21 — Quality Metrics and BD-Rate: How "50%" Is Actually Measured

### 21.1 The question this chapter answers

Chapter 9 stated "HEVC achieves ~50% bitrate reduction versus H.264 at equal quality" without explaining what "equal quality" means numerically, or how a single percentage summarizes what is actually a *curve*, not a point. This chapter fills that gap — and it's one of the most commonly asked "wait, how do you actually know that" questions in any coding-efficiency conversation.

### 21.2 PSNR — the default, imperfect baseline

**Peak Signal-to-Noise Ratio** measures reconstruction error directly in the pixel domain:
```
MSE = (1/N) Σ (original[i] − reconstructed[i])²
PSNR = 10 · log10( MAX² / MSE )      (MAX = 255 for 8-bit)
```
Higher PSNR = less error = (usually) better quality. PSNR is universally reported because it's simple, deterministic, and comparable across labs — but it's well known to correlate imperfectly with *perceived* quality: it penalizes every pixel error equally, regardless of whether that error falls in a smooth region (highly visible) or a busy textured region (masked, per Chapter 4's perceptual redundancy and Chapter 14 §14.4's masking discussion). Two reconstructions with identical PSNR can look noticeably different to a human viewer if the error is distributed differently.

### 21.3 SSIM — modeling structural similarity instead of raw error

**Structural Similarity (SSIM)** [[S47]](SOURCES.md#s47) compares local **luminance, contrast, and structure** between original and reconstructed image patches, rather than raw per-pixel difference, on the premise that human perception is highly sensitive to *structural* distortion (an edge that's shifted, blurred, or has ringing) but more tolerant of uniform luminance/contrast shifts. SSIM scores range 0 to 1 (1 = identical). It's not a strict replacement for PSNR — most serious evaluations (including HEVC's own JCT-VC standardization process) report **both**, precisely because they fail in different, complementary ways.

### 21.4 VMAF — a learned, multi-feature perceptual metric

**VMAF (Video Multi-method Assessment Fusion)** [[S48]](SOURCES.md#s48), developed at Netflix and now widely used in industry, combines several elementary quality features (including SSIM-family measures and detail-loss/motion measures) via a machine-learning model trained to predict actual human subjective quality scores, rather than hand-designing a single formula. It correlates with human judgment substantially better than PSNR alone across a broad range of content, which is why streaming-industry coding-efficiency claims increasingly report VMAF rather than (or alongside) PSNR. The open-source implementation is [[S25]](SOURCES.md#s25).

### 21.5 BD-Rate — the actual methodology behind "~50%"

Here is the part that resolves Chapter 9's unanswered question. A single (bitrate, quality) pair is meaningless on its own — quality vs. bitrate is a *curve* (encode the same content at several QP/CRF values, plot rate on one axis and PSNR/VMAF on the other). Comparing two codecs fairly means comparing their **whole curves**, not one operating point each.

**Bjøntegaard Delta (BD-rate)** [[S46]](SOURCES.md#s46) is the standard method:
1. Encode the same test content with **both codecs**, each at (typically) 4 different QP/CRF values, producing two rate-distortion curves.
2. Fit a polynomial (typically cubic, in log-rate vs. quality space) to each curve.
3. Numerically integrate the **horizontal (rate) difference between the two curves** over the quality range where both curves overlap.
4. Report the result as a single percentage: **"Codec B needs X% less bitrate than Codec A to achieve the same quality, averaged across this quality range."**

This is precisely how the "~50% HEVC vs. H.264" and "~40–50% VVC vs. HEVC" figures (Chapter 9) are actually computed — averaged BD-rate results across a standard set of JCT-VC test sequences (varied content: faces, motion, texture, screen content), not a single video at a single setting. **The honest caveat worth stating in any presentation:** BD-rate is itself computed on top of *some* quality metric (usually PSNR, increasingly VMAF) — so "HEVC saves 50%" implicitly means "50% by BD-rate using PSNR as the quality axis," and a VMAF-based BD-rate calculation on the same content can (and sometimes does) produce a somewhat different percentage. Precision matters here: state which metric a reported percentage is based on if asked.

### 21.6 Why this connects back to Chapter 17's RDO

Recall Chapter 17: `J = D + λR`, where `D` is a distortion metric. **Which distortion metric an encoder actually optimizes for during RDO (commonly SSE/SATD internally, for computational speed, rather than true PSNR or VMAF at every candidate evaluation) can differ from which metric is used to *report* the final coding-efficiency result (often PSNR or VMAF via BD-rate).** This is a subtlety worth having ready: an encoder optimizing internally for a cheap proxy metric, then being *evaluated* externally on a different, more perceptually accurate metric, is standard practice — and is itself a legitimate research question (does optimizing RDO directly for a perceptual metric, rather than a cheap proxy, change the achievable BD-rate? — a real, open-ended question in the field).

### 21.7 Hands-on exercise — measure a real BD-rate-style comparison yourself

Reading about BD-rate is not the same as having computed one. This exercise uses [[S19]](SOURCES.md#s19) (FFmpeg, with libx265) and takes under 15 minutes on any short test clip (`input.mp4` below — use any short, few-second clip you have):

```bash
# Encode the same clip at four CRF values (lower CRF = higher quality/bitrate)
for crf in 18 23 28 33; do
  ffmpeg -i input.mp4 -c:v libx265 -crf $crf -preset medium out_crf$crf.mp4
done

# For each output, get its bitrate and PSNR vs. the original
for crf in 18 23 28 33; do
  size=$(stat -c%s out_crf$crf.mp4 2>/dev/null || stat -f%z out_crf$crf.mp4)
  echo "CRF $crf: size=$size bytes"
  ffmpeg -i out_crf$crf.mp4 -i input.mp4 -lavfi psnr -f null - 2>&1 | grep "average"
done
```

You now have four (bitrate, PSNR) pairs — a real rate-distortion curve for one piece of content, one codec. **What this exercise deliberately leaves for you to do next, as a genuine extension:** repeat the same four-CRF sweep with a second codec (e.g., `-c:v libx264` for H.264, using FFmpeg's own CRF scale for that codec) on the *same* clip, and you'll have the two curves that a real BD-rate calculation (§21.5) compares — at that point, either fit the curves by hand (a cubic fit in log-rate vs. PSNR, per Bjøntegaard's method [[S46]](SOURCES.md#s46)) or use one of the several open-source BD-rate calculator scripts available on GitHub to get an actual percentage. Doing this once, on real content you chose, is worth more than re-reading §21.5 twice.

### Chapter 21 Quiz
1. Write the PSNR formula from memory and explain what MSE measures.
2. Name one specific weakness of PSNR as a perceptual quality measure, and explain why SSIM addresses it differently.
3. What does VMAF do differently from both PSNR and SSIM (in terms of *how* it's constructed)?
4. Describe the four steps of the BD-rate methodology, in order.
5. Precisely state what "~50% bitrate reduction" actually means as a BD-rate claim (i.e., what exactly is being averaged, over what).
6. Why might a PSNR-based BD-rate result and a VMAF-based BD-rate result differ for the same two codecs on the same content?
7. Explain the subtlety in §21.6: why might the metric an encoder optimizes internally (during RDO) differ from the metric used to report its final coding efficiency?

**Further reading/watching:** [[S46]](SOURCES.md#s46) Bjøntegaard's original BD-rate methodology document · [[S47]](SOURCES.md#s47) Wang et al., the SSIM paper · [[S48]](SOURCES.md#s48) Netflix's VMAF blog post · [[S25]](SOURCES.md#s25) VMAF codebase (run it on a real encode to see the numbers yourself).

---

## Chapter 22 — Bitstream Structure: NAL Units, Slices, Profiles, and Levels

### 22.1 Why this vocabulary matters even though it never appeared in Presentations 1–2

Both presentations focus on *what happens to one block of pixels*. This chapter covers the **container structure around that content** — how a real HEVC file is organized so that it can be transmitted over unreliable networks, decoded by devices with wildly different capabilities, and parsed at all. This is baseline vocabulary that any HEVC conversation will assume you already have.

### 22.2 NAL units — the basic packaging

Every piece of an HEVC bitstream — a parameter set, a slice of coded picture data, supplemental metadata — is wrapped in a **NAL (Network Abstraction Layer) unit**: a small header (identifying the unit's type) plus a payload. This exists specifically so the bitstream can be transported over different underlying networks (a file, an RTP stream, an MPEG-2 transport stream) using a consistent, self-describing packaging, independent of the specific transport's own framing. **Parameter sets** — VPS (Video Parameter Set), SPS (Sequence Parameter Set), PPS (Picture Parameter Set) — are themselves NAL units, carrying the resolution, profile/level, and other coding-configuration information the rest of the bitstream depends on; they're typically sent once (or repeated periodically for error resilience) rather than with every frame.

### 22.3 Slices — the real error-resilience and independent-decoding unit (distinct from Tiles)

A **slice** is a spatially contiguous sequence of CTUs within a picture that can be decoded **independently of other slices in the same picture** — critically, a slice **resets CABAC's context state and prediction dependencies at its boundary** (with some configurable exceptions), meaning a corrupted or lost slice doesn't necessarily corrupt the rest of the picture the way a corrupted CTU *within* a slice would.

**This is easy to confuse with Tiles (Chapter 19 §19.3) — be precise about the distinction:**
- **Slices** exist primarily for **error resilience and legacy compatibility** (they predate HEVC, inherited from H.264 and earlier), and are the unit at which a decoder can begin decoding after a transmission error, without necessarily being about parallelism.
- **Tiles** exist primarily for **parallel encoding/decoding performance**, dividing a picture into independent rectangular regions specifically to give separate hardware cores independent work, and are an HEVC-era addition.
- A picture can use **slices, tiles, both, or neither** — they are independent, orthogonal partitioning mechanisms serving different primary purposes, even though both happen to also provide *some* independence/parallelism benefit as a side effect of their respective primary goals.

### 22.4 Profiles — which tools a decoder must support

A **profile** defines a *subset* of the full standard's coding tools that a conformant decoder for that profile must support — because requiring every decoder to implement every possible HEVC tool (including tools aimed at radically different use cases, like lossless screen-content coding) would be impractical for simple/cheap devices. The three main HEVC profiles:
- **Main** — 8-bit, 4:2:0 — the baseline for most consumer video (streaming, broadcast).
- **Main 10** — adds 10-bit support — relevant for HDR content and, as noted in Chapter 2, for sensor/research applications wanting finer amplitude resolution.
- **Main Still Picture** — a constrained profile for single-image coding (HEVC's image-coding tools are competitive enough that HEIF, the image format, is built on them).

### 22.5 Levels — the resource-limit dial

Independent of *which tools* are supported (profile), a **level** caps *how much* of them — maximum picture resolution, maximum decoding/output frame rate, maximum bitrate, and maximum decoded-picture-buffer size. A decoder conformant to "Main profile, Level 4.1" is guaranteed to handle content up to that level's resolution/bitrate ceiling, but not necessarily beyond it. This is precisely how a phone, a TV, and a data-center transcoding server can all claim "HEVC support" while having wildly different actual capability ceilings — profile says *what tools*, level says *how much*.

### 22.6 Why this all matters for research framing

If your research targets a **specific, constrained device class** (a battery-powered sensor node, exactly Prof. Sen's lab's context), profile/level constraints are not academic — they define the actual envelope your hardware needs to satisfy to produce a genuinely standard-conformant, interoperable bitstream, as opposed to a research prototype that only talks to its own custom decoder. Being able to say precisely which profile/level your target scenario needs to conform to is a concrete, practical detail that separates "I understand the standard's structure" from "I only understand the compression algorithm."

### Chapter 22 Quiz
1. What is a NAL unit, and why does this packaging exist independent of any specific transport mechanism?
2. Name the three parameter set types and, in one sentence each, what each one carries.
3. Precisely distinguish slices from tiles — same mechanism with two names, or two different mechanisms with two different primary purposes? Justify.
4. What does a profile constrain, and what does a level constrain? Give the one-sentence distinction.
5. Name HEVC's three main profiles and one practical use case for each.
6. Why does profile/level conformance matter specifically for sensor-node hardware research, beyond pure compression-algorithm design?

**Further reading/watching:** [[S3]](SOURCES.md#s3) HEVC overview — covers NAL units, slices, and profiles/levels directly (§III–IV) · [[S1]](SOURCES.md#s1) the H.265 standard text itself, for the authoritative profile/level tables when you need exact numbers.

---

## Chapter 23 — Reading Hardware and Approximate-Computing Papers Critically

### 23.1 Why this chapter exists

Chapters 19–20 gave you the *content* of the research frontier. This chapter gives you the **critical-reading skill** — because a PhD student's job is not to accept a paper's reported numbers at face value, but to know what to check. This is the difference between summarizing a paper and being ready to discuss its limitations in a lab meeting.

### 23.2 A vocabulary bridge — translating circuit metrics into terms you already understand

Prof. Sen's papers (Chapter 20) report results in circuit-design units that a video-coding background doesn't automatically parse. Build the translation explicitly:
- **µW (microwatt) / mW (milliwatt)** — instantaneous power draw. To connect this to something concrete: a typical coin-cell battery (≈225 mAh at ≈3V ≈ 675 mWh) running a circuit that draws 15 µW continuously would last `675 mWh / 0.015 mW ≈ 45,000 hours ≈ 5+ years` — this is the actual scale of "why microwatts matter" for a battery-powered sensor node, and it's the kind of back-of-envelope conversion worth being able to produce on the spot.
- **pJ/pixel (picojoules per pixel)** — energy cost *per unit of work* (one pixel processed), rather than a raw power draw — useful because it's independent of frame rate/resolution, letting you compare efficiency across designs that process different amounts of data. Convert to a power figure by multiplying by the actual pixel throughput: `power = (pJ/pixel) × (pixels/second)`.
- **Throughput (Mpixels/s or fps at a given resolution)** — how fast the hardware actually processes data — always check this *alongside* an energy-efficiency number, because a design can look impressively low-power partly by being slow (processing fewer pixels per second), which may or may not be acceptable depending on the target application's real-time requirements.

### 23.3 A checklist for reading an energy/approximate-computing result skeptically

When you read a claim like "36% energy savings for 2% quality loss" (Chapter 14 §14.5, Chapter 20 §20.2), check:
1. **Quality loss measured how?** PSNR at a single QP, or a proper BD-rate comparison (Chapter 21) across multiple operating points? A single-point PSNR comparison can understate or overstate the real quality cost relative to a full curve comparison.
2. **Energy measured how?** Post-layout circuit simulation, an FPGA prototype, or fabricated silicon measurement? Each is progressively more credible and progressively harder/more expensive to produce — know which one a given paper is reporting.
3. **Baseline fairness** — is the "exact" baseline being compared against a genuinely optimized exact implementation, or a deliberately unoptimized one that makes the approximate version look better by comparison?
4. **Content diversity** — was the result measured on one test sequence, or a diverse standard test set (recall Chapter 21 §21.5's JCT-VC sequence sets)? A technique that works well on smooth, low-motion content might behave very differently on high-detail, high-motion content.
5. **Which pipeline stage, exactly, and how much of the total system does it represent?** An impressive percentage savings on one block (e.g., the transform+quantization datapath) may be a much smaller percentage of the *whole encoder's* energy budget if other stages (motion estimation, CABAC) dominate total consumption — a claim should be read in the context of *what fraction of the total system* that stage represents.

### 23.4 Situating a technique in the broader literature, not just one lab

Recall Chapter 19 §19.5 and Chapter 20 §20.2's pointer to [[S49]](SOURCES.md#s49) (a general approximate-computing survey) and [[S50]](SOURCES.md#s50) (a non-Sen-lab example of power-constrained HEVC complexity research). When discussing any specific paper, be ready to place it: is this the *first* work to try this approach, an *improvement* on a known technique, or a *new application* of an established technique (e.g., applying known approximate-computing principles specifically to the HEVC transform/quantization stage, which is itself the kind of contribution framing worth being precise about — "this is a novel application of an established principle to a specific, well-motivated pipeline stage," which is exactly how [[S10]](SOURCES.md#s10) should be described, rather than overselling it as an entirely new invention of approximate computing itself).

### 23.5 Seed research questions — candidate directions, phrased as testable hypotheses

Building directly on Chapters 19–20's two candidate framings, here are concrete, testable starting questions — not answers, but the kind of well-posed question a research proposal actually needs:

1. **Extending approximation beyond transform/quantization**: *Does approximating CABAC's context-modeling stage (Chapter 15) — e.g., using a coarser or static probability model for a subset of syntax elements — produce a measurably better energy/BD-rate tradeoff than approximating transform/quantization alone, given that CABAC's serial bottleneck (§15.4) is a distinct problem from computation cost?*
2. **Motion estimation approximation**: *Given that motion estimation (Chapter 8 §8.5) is the most compute-heavy encoder stage, does applying the same class of techniques (bit-shift approximation, precision scaling) to the block-matching cost function (e.g., approximating SAD/SATD computation) yield energy savings comparable to, worse than, or better than the transform/quantization results in [[S10]](SOURCES.md#s10), and does it interact with RDO's mode-decision accuracy (Chapter 17) more or less than a transform/quantization approximation does?*
3. **Sensor-side vs. standard-conformant tradeoff**: *Can [[S11]](SOURCES.md#s11)'s analog-domain compression philosophy be combined with a standard-conformant HEVC/VVC encode stage (rather than a bespoke MJPEG-like output), and if so, at what point in the analog-to-digital pipeline does inserting standard conformance stop being "free" and start costing back some of the analog-domain energy savings?*
4. **End-to-end system framing**: *For a specific target sensor-node power budget, what is the Pareto-optimal allocation of approximation "budget" across pipeline stages (transform/quantization, motion estimation, entropy coding) — i.e., is uniformly approximating every stage a little better or worse than concentrating aggressive approximation on the single most expensive stage?*

These are deliberately posed as **testable, falsifiable comparisons** rather than vague directions — that's the actual bar for a research question, and a good exercise before meeting Prof. Sen is trying to sharpen your own version of one of these (or a new one) to this level of specificity.

### Chapter 23 Quiz
1. Convert a reported "15 µW" figure into an estimated coin-cell battery lifetime, showing your work (use the worked conversion in §23.2 as a template, not the literal answer).
2. List the five items on the skeptical-reading checklist (§23.3) from memory.
3. Why does "energy measured on fabricated silicon" carry more weight than "energy measured via simulation," and why might a paper still legitimately report simulation-only results?
4. Explain why a percentage energy savings on one pipeline stage needs to be contextualized against that stage's share of the total system energy budget.
5. Pick one seed research question from §23.5 and identify, specifically, which two (or more) chapters of this book it directly builds on.

**Further reading/watching:** [[S49]](SOURCES.md#s49) Mittal — approximate computing survey · [[S50]](SOURCES.md#s50) Correa et al. — complexity-controlled HEVC encoding · re-read [[S10]](SOURCES.md#s10) one final time with this chapter's checklist in hand, and try to answer each of the five checklist items for it directly.

---

*(End of Part V. End of main text.)*

---

# Appendix A — Glossary

- **AMP (Asymmetric Motion Partitioning)** — inter-only PU splits at 1/4 or 3/4 of a CU's height/width (2N×nU, 2N×nD, nL×2N, nR×2N), letting a PU boundary track an off-center object boundary.
- **BD-Rate** — the Bjøntegaard-Delta methodology for summarizing the bitrate difference between two full rate-distortion curves as a single percentage; the actual basis for claims like "HEVC saves ~50% vs. H.264."
- **Bin** — a single binary decision within CABAC's binarization of a syntax element.
- **Bypass mode** — a CABAC mode that skips context modeling/adaptive probability estimation for bins assumed near-50/50 (e.g., coefficient signs), coding them directly at fixed probability — cheaper and more parallel-friendly than regular (context-coded) bins.
- **Bit depth** — number of bits used to represent each color channel's intensity per pixel.
- **CABAC** — Context-Adaptive Binary Arithmetic Coding; HEVC's sole entropy coding method.
- **Chroma subsampling** — reducing color-plane resolution relative to luma, exploiting perceptual redundancy (e.g., 4:2:0).
- **CRA** — Clean Random Access; a random-access I-frame type that permits some following pictures to reference earlier frames (open-GOP-friendly).
- **CRF** — Constant Rate Factor; a practical encoder setting targeting consistent perceptual quality via adaptive QP.
- **CTU** — Coding Tree Unit; the largest HEVC block unit (up to 64×64), the encoder's per-region workspace.
- **CU** — Coding Unit; a (possibly recursively split) region within a CTU where intra/inter mode is decided.
- **Deblocking filter** — an in-loop filter smoothing visible discontinuities at block boundaries.
- **Distortion (D)** — the RDO cost term measuring reconstruction error vs. the original.
- **DPCM** — Differential Pulse-Code Modulation; predictive coding of the difference from a prediction.
- **Drift** — the compounding reconstruction error that results from an unclosed (open-loop) prediction structure.
- **Entropy coding** — lossless coding exploiting the non-uniform statistics of a symbol stream (see CABAC).
- **GOP** — Group of Pictures; the span between one I-frame and the next.
- **Hybrid coding** — the combination of predictive (temporal/spatial) coding and transform coding within a closed feedback loop; the architecture underlying every standard from H.261 to VVC.
- **IDR** — Instantaneous Decoder Refresh; an I-frame that clears the entire reference buffer, the strongest random-access point.
- **Inter prediction** — prediction from a different (previously decoded) frame, via motion compensation.
- **Intra prediction** — prediction from already-decoded neighboring samples within the same frame.
- **Lagrange multiplier (λ)** — the rate-distortion exchange-rate parameter in `J = D + λR`, tied to QP.
- **Motion compensation** — applying a motion vector to fetch a predicted block from a reference frame (required on both encoder and decoder).
- **Motion estimation** — the encoder-only search process for finding the best motion vector.
- **Motion vector** — a 2D spatial displacement describing where, in a reference frame, a predicted block is found.
- **NAL unit (Network Abstraction Layer unit)** — the basic self-describing packaging for every piece of an HEVC bitstream (parameter sets, slice data), independent of the underlying transport.
- **Level** — caps *how much* of a profile's tools a decoder must support (max resolution, frame rate, bitrate, buffer size).
- **Profile** — defines *which subset* of HEVC's coding tools a conformant decoder must support (e.g., Main, Main 10, Main Still Picture).
- **PSNR (Peak Signal-to-Noise Ratio)** — a pixel-domain error metric (10·log10(MAX²/MSE)); the default but perceptually imperfect quality measure.
- **PU** — Prediction Unit; the geometry a CU is divided into for prediction purposes.
- **QP** — Quantization Parameter; controls the quantization step size (and thus rate/distortion tradeoff).
- **Quantization** — the sole deliberately lossy pipeline stage; rounds transform coefficients to a coarser step size.
- **Rate (R)** — the RDO cost term measuring the bit cost of a coding choice.
- **RDO** — Rate-Distortion Optimization; the `J = D + λR` framework governing all encoder decisions.
- **Residual** — the difference between the original signal and its prediction; what actually gets transform/quantize/entropy coded.
- **RQT** — Residual Quad-Tree; the (PU-independent) tree structure dividing a CU's residual into TUs.
- **SAO** — Sample Adaptive Offset; an HEVC-new in-loop filter correcting systematic reconstruction error via signaled per-class offsets.
- **Slice** — a spatially contiguous, independently-decodable sequence of CTUs within a picture, resetting CABAC/prediction state at its boundary; primarily for error resilience (distinct from Tiles, which are primarily for parallelism).
- **SSIM (Structural Similarity)** — a perceptual quality metric comparing local luminance/contrast/structure rather than raw pixel error.
- **Transform (DCT/DST)** — converts spatial residual values into frequency-domain coefficients, concentrating energy for efficient quantization.
- **TU** — Transform Unit; the geometry a CU's residual is divided into for transform purposes, independent of PU.
- **VMAF (Video Multi-method Assessment Fusion)** — a learned, multi-feature perceptual video quality metric developed at Netflix.
- **YCbCr** — the luma/chroma color representation used by video codecs, separating brightness from color to exploit perceptual redundancy.

---

# Appendix B — Master Reading & Video List by Chapter

| Chapter | Primary source(s) | Video/visual resource |
|---|---|---|
| 1 | [[S27]](SOURCES.md#s27), [[S42]](SOURCES.md#s42), [[S43]](SOURCES.md#s43) | "Reducible — How Video Compression Works" (YouTube) |
| 2 | [[S27]](SOURCES.md#s27) | "4:2:0 chroma subsampling explained" (YouTube) |
| 3 | [[S27]](SOURCES.md#s27) | — |
| 4 | [[S27]](SOURCES.md#s27), [[S30]](SOURCES.md#s30) | Reducible — JPEG/DCT videos |
| 5 | [[S43]](SOURCES.md#s43), [[S33]](SOURCES.md#s33), [[S3]](SOURCES.md#s3) | Yao Wang NYU slides (visual loop diagram) |
| 6 | [[S28]](SOURCES.md#s28), [[S27]](SOURCES.md#s27) | — |
| 7 | [[S31]](SOURCES.md#s31), [[S32]](SOURCES.md#s32) | Elecard 35-mode fan diagram (essential — visual) |
| 8 | [[S3]](SOURCES.md#s3) | "motion estimation motion compensation" search (YouTube) |
| 9 | [[S5]](SOURCES.md#s5), [[S6]](SOURCES.md#s6), [[S29]](SOURCES.md#s29), [[S34]](SOURCES.md#s34) | — |
| 10 | [[S16]](SOURCES.md#s16), [[S10]](SOURCES.md#s10), [[S26]](SOURCES.md#s26) | — |
| 11 | [[S42]](SOURCES.md#s42), [[S43]](SOURCES.md#s43), [[S33]](SOURCES.md#s33), [[S3]](SOURCES.md#s3) | Yao Wang NYU slides |
| 12 | [[S35]](SOURCES.md#s35), [[S3]](SOURCES.md#s3) | — |
| 13 | [[S38]](SOURCES.md#s38), [[S3]](SOURCES.md#s3) | Reducible — DCT visual intuition (essential) |
| 14 | [[S38]](SOURCES.md#s38), [[S10]](SOURCES.md#s10) | — |
| 15 | [[S36]](SOURCES.md#s36), [[S37]](SOURCES.md#s37), [[S3]](SOURCES.md#s3) | — |
| 16 | [[S39]](SOURCES.md#s39), [[S40]](SOURCES.md#s40), [[S3]](SOURCES.md#s3) | — |
| 17 | [[S44]](SOURCES.md#s44), [[S41]](SOURCES.md#s41) | — |
| 18 | [[S3]](SOURCES.md#s3) (full re-read) | — |
| 19 | [[S10]](SOURCES.md#s10), [[S49]](SOURCES.md#s49) | Search "HEVC fast mode decision," "wavefront parallel processing HEVC" |
| 20 | [[S10]](SOURCES.md#s10)–[[S14]](SOURCES.md#s14), [[S15]](SOURCES.md#s15), [[S17]](SOURCES.md#s17), [[S18]](SOURCES.md#s18), [[S50]](SOURCES.md#s50) | — |
| 21 | [[S46]](SOURCES.md#s46), [[S47]](SOURCES.md#s47), [[S48]](SOURCES.md#s48), [[S25]](SOURCES.md#s25) | — |
| 22 | [[S3]](SOURCES.md#s3), [[S1]](SOURCES.md#s1) | — |
| 23 | [[S49]](SOURCES.md#s49), [[S50]](SOURCES.md#s50), [[S10]](SOURCES.md#s10) (re-read with the Ch. 23 checklist) | — |

**Full source registry with links:** [SOURCES.md](SOURCES.md).

---

# Appendix C — Quiz Answer Key

Answers are intentionally terse — they confirm you're on the right track, not substitute for writing the full answer yourself first.

**Ch.1** 1) ≈53.2 MB/s (1280×720×3×8 bits × 24fps / 8 / 1e6). 2) Cutler's DPCM → Habibi transform coding → Netravali & Robbins motion compensation → Musmann/Pirsch/Grallert hybrid coding → H.261. 3) Motion-compensated (displaced-block) temporal prediction, plus rigorous analysis of quantization-noise feedback stability. 4) Entropy is a lower bound on *lossless* average code length for a given source model; video's *effective* redundancy under a good model is far below the raw bit count, so large practical compression ratios are consistent with, not violations of, Shannon's bound. 5) Because no realistic network/memory bandwidth exists at raw rates — compression is required for the medium to function at all, not merely to save cost.

**Ch.2** 1) Number of bits per channel per pixel; too few bits causes visible "banding" in smooth gradients. 2) Human vision is far more sensitive to luma than chroma; YCbCr separates them so chroma can be subsampled with minimal perceived loss. 3) 4:4:4 = 3N samples; 4:2:0 = N + N/4 + N/4 = 1.5N; ratio = 0.5. 4) Lossy — real color information is discarded, even if usually imperceptible. 5) 3840×2160×1.5×10 bits × 60fps ≈ 74.7 Gbit/s.

**Ch.3** 1) A field = only the odd or only the even lines of a frame, captured/shown at a distinct instant; two fields interleave to form one interlaced frame. 2) Half the vertical data per instant, doubled perceived temporal rate, exploiting CRT persistence/visual fusion — a genuine analog-broadcast bandwidth saving. 3) Sensor nodes are digital, progressive-native systems with no legacy broadcast infrastructure constraint.

**Ch.4** 1–2) Spatial=neighboring-pixel similarity (intra prediction/transform); temporal=frame-to-frame similarity (inter prediction); statistical=non-uniform symbol distribution (CABAC); perceptual=imperceptible information (subsampling/quantization). 3) Quantization and chroma subsampling are lossy; prediction, transform, entropy coding are (near-)lossless — quantization must be lossy because that's the only stage that actually reduces bits at a controllable rate/quality tradeoff. 4) Masking: busy detail hides quantization error the eye would otherwise notice in a smooth region.

**Ch.5** 1) Predict→Subtract→Transform→Quantize→Entropy-code, +feedback; only Quantize is lossy. 2) Raw pixels are only weakly locally correlated at the sample level and close to uniform in distribution — entropy coding alone can't exploit block/frame-level redundancy. 3) Combining predictive coding (DPCM) and transform coding within a closed loop; Musmann, Pirsch & Grallert, 1985. 4) e.g. 64×64 CTUs (Ch.12/partitioning), 35 intra modes (Ch.7/prediction), SAO (Ch.16/filtering). 5) False — same five-stage hybrid architecture since 1985; HEVC changes parameters/tools, not the architecture.

**Ch.6** 1) I=self only; P=prior decoded frame(s); B=prior and future (display-order) decoded frames. 2) Wastes temporal redundancy, usually the largest single redundancy source. 3) Longer GOP → better compression, fewer/coarser random-access points; shorter GOP → worse compression, finer seeking/more error resilience. 4) IDR clears the whole reference buffer (nothing after references before); CRA allows some leading pictures to still reference earlier frames — broadcasters favor CRA for better open-GOP compression when strict IDR-only resilience isn't required. 5) Because B1 needs P3 as a "future" (in display order) reference, so P3 must be decoded first.

**Ch.7** 1) Because prediction must be reproducible identically at the decoder, which has only already-decoded (above/left, in raster order) neighbors available. 2) Planar=smooth gradients; DC=flat/uniform regions. 3) More angular resolution → smaller average prediction error across real edge orientations, at the cost of more mode-signaling overhead. 4) Cheaply signaling the likely mode by exploiting that neighboring blocks often share dominant edge directions. 5) A newly-uncovered background region with no good match in any reference frame.

**Ch.8** 1) ME=search (encoder-only); MC=apply (both sides). 2) Real-world motion rarely aligns to integer pixel offsets; sub-pixel interpolation matches true motion more closely. 3) An object partially occluded in one reference but visible in the other. 4) False — same transform/quantize/entropy-code pipeline handles both. 5) Combinatorially large search window; replaced by heuristic search patterns (diamond/hexagon, predictive starting points).

**Ch.9** 1) ≈50% (HEVC vs H.264); ≈40–50% (VVC vs HEVC). 2) Fragmented, costly, overlapping patent pools vs. H.264's clearer single pool, deterring adoption. 3) Sibling — shares the hybrid-coding lineage but is an independently designed tool set, not a direct descendant. 4) e.g. 128×128 CTUs (extends Ch.12), 67 intra modes (extends Ch.7). 5) Each generation's efficiency gain comes from a larger/more flexible tool space, which mechanically increases the RDO search space and thus encoder complexity.

**Ch.10** 1) "More compression" = new coding tools; "faster search" = better exploiting the existing (already very large) tool space — the second dominates now because the tool space has become the bottleneck, not tool availability. 2) Fast search=algorithmic/software; parallel processing=software/architecture; hardware acceleration=hardware; approximate computing=hardware/circuit-level. 3) Inside the transform/quantization datapath specifically. 4) Both face a fundamental efficiency-vs-cost tension between coding/representational power and computational expense.

**Ch.11** 1–2) See §11.3–11.4 derivation. 3) A feedback loop without correction for injected disturbance (quantization noise) accumulates error indefinitely, analogous to an uncompensated control system. 4) Floating-point rounding differs subtly across implementations/hardware, which would itself reintroduce drift — exact integer arithmetic guarantees bit-identical results everywhere. 5) The decoder (inverse operations, filters) is normatively specified; the encoder's search/mode-decision process is not — this split exists because only decoder bit-exactness is required for zero drift. 6) ŝ[n]=53, q[n]=2. 7) A cleaner stored reference reduces future residuals, a real bitrate/efficiency effect, not just a display-time cosmetic one.

**Ch.12** 1) CTU=where; CU=split?; PU=how predicted; TU=how transformed. 2) 64×64 max; 16× the area of H.264's 16×16 macroblock. 3) Prediction geometry and transform-error geometry are different problems; e.g. a uniformly-predicted CU can still have spatially uneven residual needing finer TUs locally. 4) It weighs added signaling bits against distortion reduction — exactly RDO's J=D+λR framework. 5) The sharp edge concentrates residual energy locally; a smaller TU there captures it more efficiently than one large TU spanning the whole region. 6) AMP lets a PU boundary sit at 1/4 or 3/4 of a CU's dimension rather than only the midline, tracking off-center object boundaries; whether it's used is decided by RDO weighing its signaling cost against prediction improvement.

**Ch.13** 1) Close approximation to the KLT, the theoretically optimal decorrelating transform for spatially-correlated data. 2) Most energy concentrated in few (low-frequency) coefficients — shown by the example's rapid magnitude falloff away from the top-left. 3) (Near-)lossless — reorganizes energy distribution, doesn't discard information (aside from small integer-rounding error). 4) Larger transforms capture wider correlation efficiently but cost more compute and can't localize fine detail as well; hence adaptive TU sizing. 5) DST better fits the statistical properties of intra residuals near a prediction boundary. 6) X[k] = c(k)·Σx[n]·cos(π/N·(n+1/2)·k); k=0 (constant cosine term, no oscillation) is the DC coefficient. Integer approximation guarantees bit-exact, drift-free reproducibility across all decoder implementations (Ch.11 §11.5) and avoids requiring floating-point hardware.

**Ch.14** 1) Quantization; all other stages are (near-)lossless. 2) QP controls step size; +6 QP ≈ doubles step size. 3) CRF targets consistent perceptual quality via adaptively varying QP, rather than one fixed QP everywhere. 4) Masking: detail hides error the eye would notice in smoothness; lets the encoder quantize busy regions more coarsely "for free." 5) Approximates division via bit-shifts + loop perforation + precision scaling; ≈36% energy savings for ≈2% quality loss.

**Ch.15** 1) Binarize → context-model → arithmetic-code. 2) Adapts based on previously coded neighboring bins/context (e.g., neighbors' split decisions), tracking real local statistics. 3) −log2(0.98) ≈ 0.029 bits; Huffman can't assign less than 1 whole bit to any symbol. 4) Each bin's probability model depends on prior bins/context state, making decode order-dependent and non-parallelizable at the bin level. 5) Tiles=independent regions; WPP=staggered rows; both address CABAC's serial bottleneck for multi-core parallelism. 6) Bypass mode skips context modeling for near-50/50 bins (e.g. signs), coding them at fixed probability; it matters for hardware because bypass bins have no context-update dependency chain and can be processed at much higher throughput than regular bins. 7) Lower QP → fewer coefficients round to zero → more significant-coefficient/magnitude bins CABAC must code → directly larger R, exactly the term Chapter 17's J=D+λR counts.

**Ch.16** 1) Independent per-block quantization can leave adjacent blocks slightly mismatched at shared edges even where the true image is smooth. 2) Boundary discontinuity strength, prediction mode, and QP. 3) BO=intensity-band systematic error (e.g., a tonal range reconstructed too dark); EO=edge-shape systematic error (e.g., ringing near sharp edges). 4) They target different, non-overlapping artifact types (boundary discontinuity vs. systematic error patterns). 5) In-loop filtering changes the stored reference used by future predictions, reducing future residuals — a real bitrate effect, unlike display-only post-processing (Ch.11 §11.6).

**Ch.17** 1) J = D + λR; D=distortion, R=rate, λ=Lagrange multiplier tied to QP. 2) Converts a constrained problem into an unconstrained one, tractable to evaluate at every fine-grained decision point. 3) At λ=3: A=18+18=36, B=8+30=38, C=5+48=53 → A now wins — because a larger λ weights rate more heavily, favoring the cheapest-bits option. 4) It doesn't optimize D or R alone; it finds the best weighted tradeoff, which can differ from either individual optimum. 5) Combinatorial explosion across CU/PU/TU/mode choices; motivates fast search, parallelism, hardware acceleration, and approximate computing (Ch.19).

**Ch.18** 1–4) self-assessed; no single correct wording — verify against §18.2's structure and Chapters 4/11/17 for the redundancy/drift/RDO cross-references. 5) Verify against the §18.2b table exactly: CU split→transform size follows from Ch.12's partition choice; quantization's surviving-coefficient count follows from the transform's energy compaction (Ch.13); entropy cost follows directly from how many nonzero coefficients survived (Ch.14→15); filtering choice follows from the region's content type (Ch.16); and the RDO score is the combination of the resulting D and R that made this exact combination of choices the lowest-cost option (Ch.17).

**Ch.19** 1) The CU/PU/TU/mode/motion-vector combinatorial space, multiplied across every CTU/frame, is too large to evaluate exhaustively at real-time speed. 2) Tiles fully partition CABAC state (parallel but loses cross-tile context sharing); WPP staggers rows while preserving most context sharing, hence usually better efficiency. 3) Fixed-function hardware trades general programmability for large speed/energy gains on specific, high-volume operations. 4) Bit-shift division approximation, loop perforation, precision scaling — see §19.5. 5) Because only the decoder need be bit-exact/standard-conformant; the encoder's internal computation (however approximate) only needs to produce a legal, decodable bitstream. 6) Each targets a different bottleneck (algorithmic size, serial dependency, general-purpose overhead, exactness requirement) — real systems often combine multiple simultaneously.

**Ch.20** 1) Mixed-signal circuits, Internet of Bodies, hardware security; compression is one applied domain via energy-efficient hardware, not the lab's core identity. 2) Transform+quantization (Ch.13–14); bit-shift/loop-perforation/precision-scaling; ≈36% energy savings for ≈2% quality loss. 3) S10 stays in the digital pipeline, approximating an existing stage; S11 intervenes earlier, in the analog domain, before/instead of much of the digital pipeline (Ch.2–18) entirely. 4) Framing 1 extends approximate-encoder computing to more standard pipeline stages; Framing 2 brings sensor-side/analog efficiency into a standardized-codec context — distinct because one stays inside HEVC's existing structure, the other partially bypasses it. 5) Because encoder-side computation is unconstrained by the standard (Ch.11 §11.5), any approximation there remains legal as long as the resulting bitstream is conformant.

**Ch.21** 1) PSNR=10·log10(MAX²/MSE), MSE=mean squared per-pixel error. 2) PSNR weights every pixel error equally regardless of visibility; SSIM instead compares local luminance/contrast/structure, better matching that structural distortion (e.g. blur, ringing) is more perceptually salient than uniform shifts. 3) VMAF fuses several elementary features via a model trained on actual human subjective scores, rather than a single hand-designed formula. 4) Encode both codecs at several QP/CRF points → fit a curve to each → integrate the horizontal (rate) gap between curves over their overlapping quality range → report as one percentage. 5) It's an average bitrate saving across a standard test-sequence set and quality range, computed via curve-fitting/integration, not a single-point measurement. 6) Because PSNR and VMAF weight/measure error differently, so the same rate-distortion behavior can integrate to different percentages under each metric. 7) Encoders commonly use a cheap proxy (SSE/SATD) internally for speed across many candidate evaluations, while the final reported efficiency is often measured with a more perceptually accurate metric (PSNR/VMAF) — the two serve different purposes (fast internal search vs. accurate final reporting).

**Ch.22** 1) A NAL unit is the standard self-describing packaging (header + payload) for any bitstream component; it exists so the same bitstream content can be carried over different transports (file, RTP, transport stream) consistently. 2) VPS=overall video-sequence-level parameters; SPS=sequence-level coding configuration (resolution, profile/level, etc.); PPS=picture-level configuration. 3) Two different mechanisms with different primary purposes: slices target error resilience/independent decoding (legacy, pre-HEVC), tiles target parallel performance (HEVC-era) — a picture can use either, both, or neither. 4) Profile constrains which tools must be supported; level constrains how much (resolution/bitrate/frame-rate/buffer ceilings). 5) Main=8-bit 4:2:0 consumer video; Main 10=10-bit, HDR/sensor use; Main Still Picture=single-image coding (basis of HEIF). 6) Because a real, interoperable deployment (not just a research prototype talking to its own decoder) must conform to specific profile/level ceilings that define the actual envelope the hardware needs to satisfy.

**Ch.23** 1) Using the §23.2 method: battery capacity (mWh) ÷ power draw (mW) = lifetime in hours — e.g. a 675 mWh coin cell ÷ 0.015 mW ≈ 45,000 hours. 2) Quality-loss measurement method, energy-measurement method (sim/FPGA/silicon), baseline fairness, content diversity, and what fraction of the total system the improved stage represents. 3) Fabricated silicon reflects real, manufactured behavior including effects simulation can miss; simulation-only results are still legitimately reported when silicon fabrication is costly/premature for an early-stage research result, as long as this is stated plainly. 4) Because a large percentage savings on a small-contribution stage may be a small percentage of total system savings, while a smaller percentage on a dominant-energy stage may matter more — the raw percentage alone doesn't convey system-level impact. 5) Self-assessed — e.g. question 2 (motion estimation approximation) builds directly on Ch.8 §8.5 (search cost) and Ch.17 (RDO's mode-decision sensitivity).

---

# Appendix D — Formula Reference Sheet

```
Raw bitrate (bits/s) = width × height × bits_per_channel × channels × fps

4:2:0 total samples   = 1.5N   (N = luma sample count)
4:4:4 total samples   = 3N

Closed-loop model:
  e[n]   = s[n] − p[n]
  Ê[n]   = Q(T(e[n]))
  ê[n]   = T⁻¹(Q⁻¹(Ê[n]))
  ŝ[n]   = p[n] + ê[n]
  ŝ_f[n] = SAO(Deblock(ŝ[n]))          ← used as p[n+1]'s basis, never s[n]

Quantization:
  quantized_coefficient = round(coefficient / step_size)
  step_size doubles approximately every +6 in QP

Shannon ideal code length:
  L(symbol) = −log2(P(symbol))   bits

Rate–Distortion Optimization:
  J = D + λR         (choose the candidate minimizing J)

Quality metrics (Ch. 21):
  MSE  = (1/N) Σ (original[i] − reconstructed[i])²
  PSNR = 10 · log10( MAX² / MSE )        (MAX = 255 for 8-bit)
  SSIM ∈ [0,1]  (1 = identical; compares local luminance/contrast/structure)
  BD-rate = % bitrate difference between two fitted RD curves, integrated
            over their overlapping quality range (Bjøntegaard's method)

Circuit-metric conversions (Ch. 23):
  power (W) = energy-per-unit (J/unit) × throughput (units/second)
  battery life (hours) ≈ battery capacity (mWh) / power draw (mW)
```

---

*End of H.265/HEVC: The Complete Primer.*
