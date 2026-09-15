# What is Rate Distortion Optimization?

> Learning sequence: Optimization · Sources: [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7) [[S44]](../SOURCES.md#s44)

## Short answer

Rate Distortion Optimization (RDO) is an encoder design method for comparing coding candidates with a combined cost, commonly written:

```text
J = D + lambda R
```

Here, `D` measures reconstruction distortion, `R` represents estimated or exactly coded bits, and `lambda` sets the exchange rate between distortion and bitrate.

## Why both terms are needed

- Choosing only the smallest distortion can spend an unreasonable number of bits.
- Choosing only the fewest bits can produce unacceptable reconstruction quality.
- The combined cost lets the encoder rank candidates that make different compromises.

## What can be compared

An encoder may apply rate-distortion costs to decisions such as:

- Coding Unit split versus no split;
- intra versus inter prediction;
- intra mode, reference picture, motion vector, or merge candidate;
- Prediction Unit and Transform Unit partition choices;
- coefficient coding and quantization-related alternatives.

## Important limitation

HEVC does not mandate RDO or one distortion metric, rate estimator, lambda formula, search order, or pruning strategy. Full evaluation of every candidate is expensive, so real encoders combine RDO with fast screening, approximations, and early termination.

`lambda` is commonly related to QP, but the exact relationship is encoder-dependent. Sequence-level QP selection and buffer-based rate control are broader control problems and should not be reduced to this formula alone.

## What to remember

RDO is an encoder strategy for choosing among compliant alternatives, not a decoding tool defined by the HEVC bitstream.
