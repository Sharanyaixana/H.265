# What is H.265, and why was it made?

> Learning sequence: Foundations · Sources: [[S1]](../SOURCES.md#s1) [[S2]](../SOURCES.md#s2) [[S3]](../SOURCES.md#s3) [[S4]](../SOURCES.md#s4)

## Short answer

**H.265**, also called **High Efficiency Video Coding (HEVC)**, is a video-compression standard whose first edition was approved in 2013. It was designed to provide substantially better compression than H.264/Advanced Video Coding (AVC), with roughly 50% bitrate savings at comparable visual quality in the test conditions reported during standardization. That percentage is an average experimental result, not a guarantee for every video or encoder.

## What the standard actually defines

HEVC defines a compressed-bitstream syntax and the decoding process needed to turn a conforming bitstream into reconstructed pictures. It also constrains what a conforming encoder may place in that bitstream. It does **not** prescribe one search algorithm for choosing partitions, prediction modes, motion vectors, or rate control.

This distinction explains why two HEVC encoders can both be compliant yet produce different quality, bitrate, speed, and power consumption. Encoder decision-making is a major engineering and research space.

## Why it was developed

Video workloads were moving toward higher resolution, frame rate, and bit depth. Sending H.264-quality video at lower bitrate would reduce transmission and storage cost and make Ultra High Definition delivery more practical. HEVC kept the hybrid prediction-transform architecture but expanded its block sizes, prediction choices, transforms, filtering, and parallel-processing support [[S3]](../SOURCES.md#s3).

## Names and organizations

- **ITU-T H.265** is the ITU-T designation [[S1]](../SOURCES.md#s1).
- **ISO/IEC 23008-2** is the technically aligned ISO/IEC designation [[S2]](../SOURCES.md#s2).
- The standard was developed jointly by the ITU-T Video Coding Experts Group and ISO/IEC Moving Picture Experts Group through the Joint Collaborative Team on Video Coding.

## What to remember

HEVC is primarily a common language between an encoder and decoder. The decoder behavior is standardized; the encoder's strategy for finding a good compliant representation is largely open.

## Related question

See [How does H.265 compare with H.264, AV1, and VVC?](how-does-h265-compare-to-h264-av1-vvc.md).
