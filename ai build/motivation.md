# Project Motivation

## What this project is about

This project builds the technical foundation needed to study energy-efficient video compression, beginning with H.265 or High Efficiency Video Coding. The immediate output is a sequence of presentations, but the larger purpose is to develop research-level understanding of how compression algorithms interact with hardware cost, data movement, power, latency, and image quality.

The project begins from first principles because a useful hardware contribution requires more than recognizing codec block names. It requires understanding what information each stage removes or represents, why the stage exists, which decisions are fixed by the standard, and which decisions remain open to encoder designers.

## The starting observation

A camera produces a large stream of sample values. Sending or storing every sample directly is expensive in bandwidth, memory, and energy. Compression reduces that burden by exploiting structure in the signal and by accepting controlled reconstruction error where appropriate.

The initial discussion used still-image compression as the entry point. A block transform represents spatial variation using coefficients. Quantization reduces coefficient precision and often produces many zeros. Scanning and entropy coding then represent the remaining information efficiently. This establishes the spatial side of the problem.

Video adds temporal structure. Consecutive pictures often share most of their content: a background may remain still while only one object moves. Instead of coding every picture independently, a video codec can predict a current block from reconstructed reference pictures and code the remaining error. This transition from image compression to temporal prediction motivates the study of HEVC.

## Why HEVC

HEVC is a mature standardized video codec with a rich set of block partitions, prediction modes, transforms, quantization controls, entropy coding, filters, and parallel-processing structures. It is complex enough to expose realistic research problems but established enough to provide a normative specification, reference software, production encoders, and published comparisons.

Studying HEVC provides a foundation for understanding H.264 or Advanced Video Coding, AV1, H.266 or Versatile Video Coding, and emerging learned-compression systems. The value is therefore broader than learning one codec.

## Research motivation

Better coding efficiency is not automatically better system design. A lower bitrate may require more motion search, more mode evaluation, additional memory traffic, or more expensive hardware. For an energy-constrained sensor or wearable system, the relevant question is not only:

> How many bits are saved?

It is also:

> What computation, memory movement, latency, silicon area, and energy were required to save them?

This leads to a multi-objective research problem:

```text
rate ↔ distortion ↔ complexity ↔ energy ↔ latency
```

Professor Shreyas Sen's related work on approximate transform and quantization hardware, analog-to-MJPEG conversion, and energy-constrained sensing provides a methodological connection. These publications are not automatically HEVC implementations. The research task is to determine which ideas transfer to a standards-conforming video encoder, what must be redesigned, and whether the full system still benefits after temporal prediction, memory, control, and bitstream generation are included.

## What the repository must achieve

The repository should support four levels of work:

1. **Learning:** explain video coding from raw samples through the HEVC bitstream.
2. **Presentation:** turn that knowledge into a clear sequence of talks with accurate diagrams and sourced claims.
3. **Verification:** keep standards facts, encoder choices, and teaching illustrations separate.
4. **Research formation:** turn unresolved implementation questions into measurable hypotheses and experiments.

The presentations are milestones, not the final objective. A successful presentation should expose gaps in understanding and produce better questions for the next study cycle.

## Current research questions

The current questions focus on how an encoder selects Coding Unit partitions, Prediction Unit structures, Transform Unit partitions, Quantization Parameters, and CABAC syntax. These questions matter because they sit at the boundary between normative decoding and encoder design, where complexity and energy optimizations are possible.

## Confidentiality boundary

General explanations of published standards and papers can be maintained as study material. Collaborative lab or company ideas may create intellectual property and must remain within authorized Ixana or laboratory systems. Do not publish, upload, or individually attribute project-specific research ideas without approval.
