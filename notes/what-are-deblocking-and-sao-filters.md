# What are the deblocking and SAO filters?

> Learning sequence: Coding loop · Sources: [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7)

## Short answer

HEVC applies two normative in-loop filters to reconstructed samples: the **deblocking filter** first reduces selected discontinuities across block boundaries, and **Sample Adaptive Offset (SAO)** then applies signaled class-dependent offsets to reduce systematic reconstruction error.

## Deblocking filter

Block prediction, transform partitioning, and quantization can create artificial jumps at block edges. HEVC derives boundary strength and checks local sample conditions before filtering. It does not simply blur every boundary; filtering strength and whether filtering is applied depend on the coded modes, residual information, motion information, and neighboring samples.

## Sample Adaptive Offset

SAO classifies reconstructed samples and adds an offset selected by the encoder and signaled to the decoder.

- **Band Offset:** selects intensity bands and applies offsets to samples in those bands.
- **Edge Offset:** classifies a sample by comparing it with two neighbors in a chosen direction, then applies an offset for the resulting edge category.

SAO is not primarily a smoothing operation. It corrects statistical bias introduced by reconstruction and quantization and can preserve or restore edge contrast depending on the selected offsets.

## Why “in-loop” matters

The filtered picture is placed in the Decoded Picture Buffer and may be used for future inter prediction. Encoder and decoder must therefore apply matching filters; otherwise their future reference pictures would diverge.

## What to remember

Deblocking acts on selected block boundaries. SAO acts on classified reconstructed samples. Both affect future reference pictures as well as the displayed reconstruction.
