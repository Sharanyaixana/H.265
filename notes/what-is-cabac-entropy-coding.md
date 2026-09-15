# What is CABAC entropy coding?

> Learning sequence: Coding loop · Sources: [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7)

## Short answer

**Context-based Adaptive Binary Arithmetic Coding (CABAC)** is HEVC's entropy-coding method for slice data. It converts syntax-element values into binary decisions called bins, assigns probability models where appropriate, and arithmetic-codes the bins into the bitstream without changing their decoded meaning.

## The three ideas

1. **Binarization:** represent a non-binary syntax value using the bin string specified for that syntax element.
2. **Context modeling:** for context-coded bins, select a probability state based on the syntax element and previously decoded information.
3. **Binary arithmetic coding:** update a numerical range according to the bin and its probability state, with renormalization producing output bits.

HEVC also uses **bypass-coded bins**, which use an equiprobable path without a context-state update, and a termination process at defined boundaries.

## How the decoder knows what a bin means

The decoder follows the normative HEVC syntax in a fixed order. The active syntax element determines which binarization and context rule to use and how many bins to request. CABAC does not insert textual separators, and the decoder does not identify fields by visually recognizing patterns in the compressed bits.

This is why an invented unary “number of positions” field must not be presented as actual HEVC residual syntax. Real residual coding uses specified elements such as the last-significant-coefficient position, significance flags, coefficient-level flags or remainders, and sign flags [[S1]](../SOURCES.md#s1).

## Why it compresses

If one outcome is much more likely than another, arithmetic coding can represent a long sequence at an average cost below one bit per bin. CABAC also adapts many probability states as decoding proceeds.

## Serial dependency and parallelism

Context-coded bins within one CABAC substream have sequential state dependencies. Bypass bins follow a simpler path. HEVC's tiles, slices, and Wavefront Parallel Processing create additional boundaries or substreams so more work can proceed in parallel, with corresponding coding-efficiency and implementation tradeoffs.

## What to remember

CABAC is lossless packing of already chosen syntax. It does not perform transform, quantization, or mode selection.

## Related open question

See [Q6: CABAC syntax-field boundaries](../open-questions.md#q6-how-does-cabac-know-which-bin-belongs-to-which-syntax-field).
