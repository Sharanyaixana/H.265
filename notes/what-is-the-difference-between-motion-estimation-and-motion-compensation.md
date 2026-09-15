# What is the difference between motion estimation and motion compensation?

> Learning sequence: Prediction · Sources: [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7)

## Short answer

**Motion estimation** is the encoder's search for useful reference blocks and motion parameters. **Motion compensation** is the standardized process that uses decoded motion information and reference pictures to construct the prediction.

## Motion estimation

- Performed by the encoder.
- Not prescribed by the HEVC standard.
- May use full search, fast search, hierarchical search, early termination, or learned heuristics.
- Common screening measures include Sum of Absolute Differences, but final mode decisions may include both distortion and signaling cost.
- Often one of the dominant sources of encoder computation.

## Motion compensation

- Performed by both encoder and decoder.
- Uses reference indices, motion vectors, and interpolation filters.
- Must be reproduced consistently so both sides obtain matching prediction samples.

## Example

The encoder may test many candidate locations for a moving car. That search is motion estimation. Once one vector is selected, generating the predicted car block from the selected reference position is motion compensation.

## What to remember

Estimation chooses motion; compensation applies it.
