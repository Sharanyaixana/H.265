# What is CABAC entropy coding?

> Chapter 3 · Day 7

## One-line answer
CABAC (Context-Adaptive Binary Arithmetic Coding) is HEVC's final, lossless step that packs quantized coefficients and metadata into as few bits as possible via three stages: binarization → context modeling (adaptive probabilities) → arithmetic coding.

## Key points
- Frequent symbols can cost **less than one bit** each.
- CABAC is inherently **serial** (each symbol updates the context) → a parallelization bottleneck → a reason the parallel tools (Tiles/WPP) exist.
- HEVC uses CABAC only (H.264 also allowed simpler CAVLC).

## Questions this raised
- (move unresolved ones to ../open-questions.md)
