# What are the deblocking and SAO filters?

> Chapter 4 · Day 9

## One-line answer
Two in-loop filters clean up reconstructed frames before they become references: the deblocking filter smooths block-boundary discontinuities, then SAO (Sample Adaptive Offset, new in HEVC) adds small signaled offsets to reduce ringing and banding.

## Key points
- Order: **deblocking → SAO**.
- "In-loop" = applied inside the reconstruction loop, so better references also shrink *future* residuals (not just prettier output).
- SAO modes: **Band Offset** (by intensity band) and **Edge Offset** (by local edge shape).

## Questions this raised
- (move unresolved ones to ../open-questions.md)
