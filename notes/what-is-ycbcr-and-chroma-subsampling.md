# What are YCbCr and chroma subsampling?

> Learning sequence: Foundations · Sources: [[S3]](../SOURCES.md#s3) [[S27]](../SOURCES.md#s27)

## Short answer

YCbCr represents a picture using one luma component, **Y**, and two chroma-difference components, **Cb** and **Cr**. Chroma subsampling stores fewer Cb and Cr samples than Y samples. At the same bit depth, 4:2:0 uses half as many samples as 4:4:4.

YCbCr conversion by itself does not reduce the sample count. The saving comes from subsampling the chroma components.

## How 4:2:0 halves the sample count

Consider a 2 × 2 area containing four luma positions.

```text
4:4:4: 4 Y + 4 Cb + 4 Cr = 12 samples
4:2:0: 4 Y + 1 Cb + 1 Cr =  6 samples
```

At 8 bits per sample:

| Format | Samples in a 2 × 2 area | Average bits per pixel | Size relative to 4:4:4 |
|---|---:|---:|---:|
| 4:4:4 | 12 | 24 | 100% |
| 4:2:0 | 6 | 12 | 50% |

Each chroma plane in 4:2:0 has half the luma resolution horizontally and half vertically, so it contains one quarter as many samples. The luma plane remains at full resolution.

## Why quality can remain acceptable

Human vision generally resolves fine brightness structure better than fine color structure. Reducing chroma resolution can therefore save data with less visible damage than reducing luma resolution by the same amount. The result is still lossy: sharp colored edges, text, and computer graphics can reveal chroma-subsampling artifacts.

## Interpreting the notation

- **4:4:4:** no chroma subsampling.
- **4:2:2:** each chroma plane has half the horizontal luma sampling rate and the same vertical rate.
- **4:2:0:** each chroma plane has half the horizontal and half the vertical luma sampling rate.

The exact chroma sample positions, called chroma siting, depend on the format and should not be inferred from the digits alone.

## What to remember

For 8-bit data, 4:2:0 averages 12 bits per pixel before codec compression; it is half the sample storage of 8-bit 4:4:4 or RGB.
