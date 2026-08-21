# What is H.265, and why was it made?

> Chapter 1 · Sources: [[S1]](../SOURCES.md#s1) [[S2]](../SOURCES.md#s2) [[S3]](../SOURCES.md#s3) [[S34]](../SOURCES.md#s34)

## One-line answer
**H.265**, also called **HEVC (High Efficiency Video Coding)**, is a video compression standard finalized in **2013** with one blunt, quantified goal: **the same visual quality as the older H.264 standard, at roughly half the bitrate.**

## Who made it, and what "standard" means
- Formally published as **ITU-T H.265** [[S1]](../SOURCES.md#s1) and **ISO/IEC 23008-2 / MPEG-H Part 2** [[S2]](../SOURCES.md#s2) — identical text, two publishing bodies.
- Developed jointly by **ITU-T VCEG** and **ISO/IEC MPEG**, working together as the **JCT-VC (Joint Collaborative Team on Video Coding)**.
- A "standard" here means: a precise, agreed-upon recipe for **encoding video into a compressed bitstream and decoding it back** — so that any compliant encoder and any compliant decoder, made by different companies, interoperate correctly.

## The key clarification (a common beginner confusion — and a good talking point)
A standard like H.265 **only defines the bitstream syntax and how a decoder must interpret it.** It does **not** dictate *how* to encode. That freedom is why two H.265 encoders (say `x265` vs. a phone's hardware encoder) can produce very different quality at the same bitrate — they're both valid, but one made smarter choices. **This gap between "what the standard defines" and "how a smart encoder chooses" is where most encoding research and engineering happens.**

## Why it was developed
Video kept growing — more pixels (HD → 4K → 8K), higher frame rates, higher bit depth, far more streaming. The previous standard, **H.264/AVC (2003)**, was excellent but a decade old and increasingly strained by 4K/UHD content. HEVC's target was explicit: **~50% bitrate reduction at equal quality**, so that 4K/UHD streaming and storage become practical [[S3]](../SOURCES.md#s3). It hits this on average across many types of content (the gain varies by content and operating point).

## Where it fits in the family
`H.264/AVC (2003) → H.265/HEVC (2013) → H.266/VVC (2020) & AV1 (~2018)` — each generation roughly halves the bitrate of its predecessor at a cost of much higher encoder complexity. See [how-does-h265-compare-to-h264-av1-vvc.md](how-does-h265-compare-to-h264-av1-vvc.md).

## Questions this raised
- (move unresolved ones to ../open-questions.md)
