# How does inter prediction work?

> Chapter 2 · Day 5

## One-line answer
Inter prediction builds a block from other (reference) frames using motion compensation; instead of sending full motion vectors it predicts them — via AMVP (candidate MV + small difference) or Merge mode (inherit a neighbor's motion).

## Key points
- **AMVP:** signal which predicted-MV candidate + a motion-vector difference (MVD).
- **Merge:** inherit a neighbor's MV + reference index by index (very cheap).
- **Skip:** merge with zero residual.
- Quarter-pel luma precision using 8-tap interpolation filters; chroma to 1/8-pel.

## Diagram to draw
- Current block → search window in reference frame → best match → MV → residual.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
