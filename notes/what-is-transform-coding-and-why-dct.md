# What is transform coding, and why does HEVC use DCT-like transforms?

> Learning sequence: Coding loop · Sources: [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7)

## Short answer

Transform coding converts a spatial residual block into coefficients describing basis patterns of different spatial frequencies. Correlated residuals often concentrate most of their energy into relatively few coefficients, giving quantization and entropy coding a favorable input distribution.

## What the transform does

The transform changes representation; it does not by itself mean “delete high frequencies.” A smooth or gradually varying residual usually produces a strong low-frequency coefficient and smaller high-frequency coefficients. A sharp or irregular residual can retain significant high-frequency energy.

HEVC specifies integer transforms closely related to the Discrete Cosine Transform (DCT) for square transform blocks of 4 × 4, 8 × 8, 16 × 16, and 32 × 32. It also defines an alternative integer transform derived from the Discrete Sine Transform for 4 × 4 intra-predicted luma residual blocks [[S3]](../SOURCES.md#s3).

## Why integer transforms

The decoder needs deterministic fixed-point operations across hardware and software implementations. Integer transform matrices, defined shifts, and clipping rules provide reproducible reconstruction and efficient implementation. The transform stage uses finite-precision arithmetic, so it is safer to say that quantization is the principal intentional loss—not that every transform operation is mathematically lossless.

## Transform-size tradeoff

- A larger Transform Unit can compact energy across a broad smooth residual.
- A smaller Transform Unit can localize sharp changes and avoid spreading their energy across a large block.
- Larger transforms require more computation and may not fit localized detail well.

The encoder therefore chooses among legal transform partitions rather than applying one size everywhere.

## What to remember

Transform coding does not remove the residual. It reorganizes residual energy so later stages can represent it efficiently.
