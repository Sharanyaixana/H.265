# What are HEVC parallel-processing tools, profiles, tiers, and levels?

> Learning sequence: Conformance and implementation · Sources: [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7)

## Short answer

Slices, tiles, and Wavefront Parallel Processing divide coded work or CABAC substreams to support parallelism, random access within a picture, packetization, or resilience. Profiles, tiers, and levels describe decoder conformance: **which coding features**, **which bitrate class**, and **which processing and buffer limits** must be supported.

## Parallel-processing structures

- **Slice or independent slice segment:** contains a sequence of Coding Tree Units with its own slice header and entropy-coding start. It limits selected in-picture dependencies but still belongs to a picture and may depend on reference pictures.
- **Tile:** divides a picture into rectangular groups of Coding Tree Units. Prediction and entropy-coding dependencies are constrained across tile boundaries, making rectangular parallel work allocation possible.
- **Wavefront Parallel Processing (WPP):** creates row-based entropy-coding substreams. A lower Coding Tree Unit row can start after sufficient progress in the row above and initializes its context from a defined state derived from that row.

More boundaries and substreams can improve parallel execution or resilience, but they can reduce coding efficiency by preventing useful context or prediction dependencies.

## Conformance vocabulary

- **Profile:** the set of coding tools, chroma formats, and bit depths a decoder supports. Main targets 8-bit 4:2:0; Main 10 adds up-to-10-bit support.
- **Tier:** a bitrate-capability class within a level. HEVC defines Main and High tiers, with High tier unavailable at lower levels.
- **Level:** upper bounds on workload and resources, including luma sample rate, picture size, bitrate, and buffering. A level is not just a resolution label; frame rate and other limits must also fit.

## What to remember

Parallel tools organize decoding work. Profile, tier, and level describe the capabilities and limits of a conforming decoder or bitstream.
