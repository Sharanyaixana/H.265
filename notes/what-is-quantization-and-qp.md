# What are quantization and QP?

> Learning sequence: Coding loop · Sources: [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7) [[S21]](../SOURCES.md#s21)

## Short answer

Quantization maps transform coefficients to coarser integer levels. Small values often become zero, which reduces bitrate but prevents exact recovery. The **Quantization Parameter (QP)** controls the effective quantization step: increasing QP by 6 approximately doubles the step size.

## Teaching model

A useful simplified model is:

```text
quantized level = round(transform coefficient ÷ step size)
reconstructed coefficient ≈ quantized level × step size
```

HEVC implements this relationship with specified integer scaling, shifts, QP-dependent factors, and optional scaling lists; it is not literally one floating-point division in the decoder.

## Rate-quality effect

- Lower QP → finer step → more nonzero levels → more bits and usually less distortion.
- Higher QP → coarser step → more zero levels → fewer bits and usually more distortion.

For 8-bit luma in the basic range, QP values run from 0 through 51. Higher bit depths extend the internal range through a bit-depth offset [[S1]](../SOURCES.md#s1).

## Who selects QP?

The standard tells the decoder how to derive and apply signaled QP information; it does not mandate one encoder policy. An encoder may begin from:

- a fixed QP requested by an application;
- a target bitrate and buffer model handled by rate control;
- an implementation-specific quality mode such as x265's Constant Rate Factor (CRF).

It may then vary QP between pictures or regions within the legal syntax. Rate-distortion analysis can help evaluate local choices, but “RDO selects QP” is not a complete description of sequence-level rate control.

## What to remember

QP is signaled control information; the quantization step is derived from it. Quantization is the central intentionally lossy stage.

## Related open question

See [Q5: QP selection](../open-questions.md#q5-how-is-qp-selected).
