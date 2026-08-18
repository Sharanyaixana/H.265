# Where is H.265 research heading?

> Chapter 6 · Day 13

## One-line answer
Active directions: reducing encoder complexity (fast/ML-guided RDO and partitioning), hardware acceleration, approximate & low-power computing for the transform/quantization path, learned/neural video codecs, and perceptual/content-adaptive coding.

## Pick ONE direction for the talk
- Recommended: **complexity/energy-efficient encoding** (fast RDO, approximate transforms, HW–algorithm co-design) — best fit for Prof. Sen's SPARC Lab [[S10]](../SOURCES.md#s10)[[S16]](../SOURCES.md#s16). See [../papers/README.md](../papers/README.md).

## The three-way tradeoff to mention
- Not just rate ↔ distortion, but **rate ↔ distortion ↔ complexity/energy**.

## Two flavors of ML in compression (keep distinct)
- ML *inside* traditional codecs (predict the split, better mode/MV, learned filters).
- End-to-end learned/neural codecs (replace the pipeline; heavy compute → loops back to hardware).

## Questions this raised
- (move unresolved ones to ../open-questions.md)
