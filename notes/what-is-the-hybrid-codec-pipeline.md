# What is the hybrid codec pipeline?

> Chapter 1 · Day 2 (revisit Day 7 & 13) · Source: [[S27]](../SOURCES.md#s27)

## One-line answer
Every modern codec (MPEG-2 → H.264 → HEVC → VVC) runs the same per-block loop: **Predict → subtract to get a residual → Transform → Quantize → Entropy-code**, plus a reconstruction feedback path so the encoder predicts from the exact frames the decoder will see.

## The pipeline in order
```
PREDICT → SUBTRACT → TRANSFORM → QUANTIZE → ENTROPY-CODE → bits
 (guess)   (residual = (concentrate  (discard the   (pack tightly,
            original −   energy into   small, unseen   short codes for
            prediction)  few coeffs)   coefficients)   common symbols)
```
The order is deliberate — each stage sets up the next:
1. **Predict** removes spatial/temporal redundancy.
2. **Transform** concentrates the leftover energy into a few coefficients.
3. **Quantize** discards the perceptually-minor part (the lossy step).
4. **Entropy-code** removes statistical redundancy (lossless).

## The residual is everything
Prediction is never perfect. `residual = original − prediction`. We code the **residual**, not the pixels. **Good prediction → tiny residual → few bits.** Almost every H.265 tool exists to shrink the residual.

## The insight I must be able to explain
- **The encoder contains a decoder inside it** (inverse quantize → inverse transform → reconstruct → in-loop filter → reference buffer).
- **Why?** The encoder must predict from the **reconstructed** (slightly degraded) frames — exactly what the decoder will have. If it predicted from pristine originals instead, encoder and decoder would **drift apart** and the picture would fall apart. That feedback path is what makes it a *loop*.

## Diagram to draw (THE diagram — practice until it's from memory)
- The full encoder block diagram (predict → transform → quantize → entropy, plus the inverse path that reconstructs reference frames), including the feedback loop and the point where the residual appears. Redraw it until you can do it from memory.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
