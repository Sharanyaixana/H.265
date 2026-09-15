# What is the hybrid video-codec pipeline?

> Learning sequence: Foundations · Sources: [[S3]](../SOURCES.md#s3) [[S33]](../SOURCES.md#s33) [[S43]](../SOURCES.md#s43)

## Short answer

A hybrid video codec combines predictive coding with transform coding. For a typical coded block, the encoder predicts the block, subtracts the prediction to obtain a residual, transforms and quantizes that residual, and entropy-codes the resulting syntax. A local decoder inside the encoder reconstructs the same reference pictures that the real decoder will use.

## Main bitstream path

```text
original block
    ↓
prediction → subtraction → residual → transform → quantization → entropy coding → bitstream
```

The encoded syntax can include partition choices, prediction modes, reference indices, motion-vector differences, transform information, quantized coefficient levels, and filter parameters—not only residual coefficients.

## Reconstruction path

```text
quantized coefficients
    ↓
inverse scaling and transform
    ↓
reconstructed residual + prediction
    ↓
deblocking and Sample Adaptive Offset
    ↓
decoded-picture buffer for future prediction
```

## Why the local decoder is necessary

Quantization changes the residual permanently. The external decoder therefore reconstructs a slightly different picture from the original input. If the encoder predicted future pictures from the pristine original while the decoder predicted from its reconstructed picture, their predictions would differ and error would drift. Both sides must use matching reconstructed references.

## Why it is called hybrid

It combines two major coding ideas:

- **Prediction** removes spatial or temporal correlation.
- **Transform coding** represents the prediction error in a form that can be quantized and entropy-coded efficiently.

## What to remember

The encoder has two related outputs after quantization: syntax is entropy-coded into the bitstream, while locally reconstructed samples are filtered and stored for future prediction.
