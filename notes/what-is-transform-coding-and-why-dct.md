# What is transform coding and why the DCT?

> Chapter 3 · Day 6

## One-line answer
Transform coding converts a residual block into frequency coefficients (using integer DCT approximations, 4×4–32×32) so that most of the signal's energy compacts into a few low-frequency coefficients — which the next step (quantization) can then cheaply discard.

## Key points
- The transform itself is (nearly) lossless — it just *reorganizes* information.
- Integer transforms are exactly invertible and hardware-friendly (no float drift).
- Intra luma 4×4 uses DST instead of DCT.
- **This DCT/quantization datapath is exactly what Prof. Sen's "approximate DCT" work optimizes for energy [[S10]](../SOURCES.md#s10).**

## Diagram to draw / see
- Use an interactive DCT demo: watch high-frequency coefficients vanish.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
