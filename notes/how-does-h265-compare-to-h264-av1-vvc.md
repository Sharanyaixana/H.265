# How does H.265 compare to H.264, AV1, and VVC?

> Chapter 6 · Day 13

## One-line answer
H.265 roughly halves H.264's bitrate at equal quality [[S3]](../SOURCES.md#s3)[[S4]](../SOURCES.md#s4); its successors continue the pattern — AV1 (AOMedia, royalty-free, ~2018, HEVC-class or better) [[S6]](../SOURCES.md#s6) and H.266/VVC (2020, ~50% over HEVC) [[S5]](../SOURCES.md#s5) — each adding coding gain at the cost of much higher encoder complexity.

## The pattern to articulate
- Each generation ≈ halves bitrate for a large jump in encoder complexity/energy.
- **Open question:** is that trade still worth it, and how do we pay for it in hardware/energy? (Good thesis-shaped question — ties to Prof. Sen's lab.)

## Key points
- All four share the **same hybrid pipeline** — successors add more tools, not a new paradigm.
- HEVC's fragmented patent-pool licensing helped drive royalty-free AV1's adoption.

## Questions this raised
- See [open-questions.md → "Is there an H.266 / something better than H.265?"](../open-questions.md)
