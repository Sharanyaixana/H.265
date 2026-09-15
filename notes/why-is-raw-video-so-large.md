# Why is raw video so large?

> Learning sequence: Foundations · Source: [[S27]](../SOURCES.md#s27)

## Short answer

Raw video records millions of sample values for every frame and repeats that process many times per second. Its data rate therefore grows as:

```text
width × height × bits per pixel × frames per second
```

## Worked example

Assume 8-bit YCbCr 4:2:0 video. It uses an average of 12 bits per pixel: 8 luma bits plus an average of 4 chroma bits.

### 1920 × 1080 at 30 frames per second

```text
1920 × 1080 × 12 × 30
= 746,496,000 bits/s
≈ 746.5 Mbit/s
```

### 3840 × 2160 at 30 frames per second

```text
3840 × 2160 × 12 × 30
= 2,985,984,000 bits/s
≈ 2.99 Gbit/s
```

These figures do not include audio, container overhead, or transmission overhead.

## The assumption matters

“Raw video” does not have one universal bitrate. For example, 8-bit RGB has 24 bits per pixel, so its bitrate is twice the 8-bit 4:2:0 examples above. Higher bit depth, frame rate, or resolution raises the result proportionally.

## Why compression is possible

Raw video describes every sample directly even though neighboring samples and neighboring frames are often strongly related. A codec predicts repeated structure, transforms and quantizes the remaining error, and entropy-codes the resulting symbols.

## What to remember

Always state the resolution, frame rate, bit depth, and chroma format when quoting a raw-video bitrate.
