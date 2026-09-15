# How does H.265 compare with H.264, AV1, and VVC?

> Learning sequence: Codec landscape · Sources: [[S3]](../SOURCES.md#s3) [[S4]](../SOURCES.md#s4) [[S5]](../SOURCES.md#s5) [[S6]](../SOURCES.md#s6) [[S51]](../SOURCES.md#s51)

## Short answer

H.265/HEVC succeeds H.264/AVC and was designed for about 50% bitrate reduction at comparable quality under the standardization test conditions. H.266/Versatile Video Coding (VVC) is HEVC's formal successor and again targets roughly 50% savings over HEVC. AV1 is a separate AOMedia codec developed under a royalty-free patent policy; its relative efficiency depends strongly on encoder, content, quality metric, speed, and operating point.

## Comparison

| Codec | Published | Relationship | Main practical distinction |
|---|---:|---|---|
| H.264/AVC | 2003 | Predecessor to HEVC | Very broad hardware and software support; lower complexity than newer codecs |
| H.265/HEVC | 2013 | Successor to AVC | Better compression, especially valuable for high-resolution video, with higher complexity and licensing friction |
| AV1 | 2018 | Independent competitor | Open specification and reference code under AOMedia's royalty-free patent policy; designed particularly for internet delivery |
| H.266/VVC | 2020 | Formal successor to HEVC | Higher compression efficiency and a broader tool set, with substantially higher implementation complexity |

## How to interpret percentage claims

A statement such as “50% better” should mean approximately half the bitrate at comparable measured quality—not half the file size under every setting. A valid comparison must state:

- the encoders and versions;
- encoding speed or configuration;
- test sequences and resolutions;
- quality metric, such as PSNR, SSIM, or VMAF;
- whether the result is objective or subjective;
- the bitrate range or BD-rate methodology.

Do not say that AV1 is universally “30–50% better than HEVC.” Published results vary and can reverse when encoder effort or metric changes.

## Current landscape

AOMedia now also lists **AV2** as its next-generation video-coding specification [[S51]](../SOURCES.md#s51). Treat it as an evolving standard and verify maturity, implementations, and measured performance before comparing it with deployed HEVC, AV1, or VVC systems.

## What to remember

Newer coding tools usually buy bitrate savings with more search, computation, memory traffic, implementation work, and sometimes licensing cost. Research comparisons should examine rate, distortion, complexity, energy, latency, and ecosystem constraints together.
