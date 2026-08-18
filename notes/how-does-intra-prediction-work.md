# How does intra prediction work?

> Chapter 2 · Day 4

## One-line answer
Intra prediction guesses a block's pixels from already-decoded neighbors (the row above and column to the left) in the *same* frame; HEVC offers 35 luma modes (Planar, DC, and 33 angular directions), and the encoder picks the best by RDO.

## Key points
- Planar = smooth gradients; DC = flat average; angular = directional edges/textures.
- 35 modes vs. H.264's 9 → a better fit → **smaller residual** before the transform.
- Intra luma residuals use a 4×4 DST instead of DCT (better statistical match).

## Diagram to draw
- The angular "fan" of directions predicting a block from top/left reference samples.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
