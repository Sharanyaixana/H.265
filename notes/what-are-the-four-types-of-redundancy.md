# What are the four types of redundancy?

> Chapter 1 · Day 2 · Source: [[S27]](../SOURCES.md#s27)

## One-line answer
Compression works by removing four kinds of redundancy: **spatial** (nearby pixels alike), **temporal** (nearby frames alike), **statistical** (some symbols frequent), and **perceptual** (the eye won't notice some detail). Everything in H.265 is machinery to exploit these four.

## The four, with examples

| Redundancy | What repeats / is wasted | Everyday example | H.265 tool that removes it |
|---|---|---|---|
| **Spatial** | neighboring *pixels* in one frame | a clear blue sky — big patches of near-identical pixels | intra prediction + transform |
| **Temporal** | consecutive *frames* | a news anchor: background frozen, only lips move | inter prediction (motion) |
| **Statistical** | some *symbols* far more common than others | value "0" appears constantly after quantization | entropy coding (CABAC) |
| **Perceptual** | detail the eye can't see | fine color detail (already exploited by 4:2:0) | chroma subsampling + quantization |

## The mental hook
- Spatial + temporal = **prediction** (guess a pixel/block from what's already known).
- Statistical = **smart labeling** (short codes for common things).
- Perceptual = **throw away what nobody sees.**

## Lossy vs. lossless
- **Lossless** (like ZIP): perfect reconstruction, removes only *statistical* redundancy → modest ratios (~2×).
- **Lossy**: permanently discards detail (perceptual) for far bigger savings → how we reach the ~150× video needs. The knob controlling how much we discard is **quantization**.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
