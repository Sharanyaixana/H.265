# What are the four types of redundancy in video?

> Learning sequence: Foundations · Sources: [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7) [[S27]](../SOURCES.md#s27)

## Short answer

Video compression exploits four useful forms of predictability or irrelevance: **spatial**, **temporal**, **statistical**, and **perceptual** redundancy.

## The four types

| Type | Meaning | Example | Typical tool |
|---|---|---|---|
| Spatial | Nearby samples within one picture are correlated | A smooth sky or wall | Intra prediction and transform coding |
| Temporal | Pictures close in time contain related content | A static background behind a moving person | Inter prediction and motion compensation |
| Statistical | Some symbols or events are more probable than others | Zero coefficients occur frequently after quantization | Context modeling and arithmetic coding |
| Perceptual | Some changes matter less to human observers | Reduced fine chroma detail | Chroma subsampling and quantization choices |

## Lossless and lossy use

Lossless coding can exploit spatial, temporal, and statistical structure as long as every original sample remains recoverable. Perceptual irrelevance is exploited by lossy coding because information is intentionally discarded or represented less precisely.

Prediction and transforms do not automatically reduce the number of bits. They reshape the data into a form with a more compressible distribution. Quantization and entropy coding then turn that structure into bitrate reduction.

## What to remember

- Prediction attacks spatial and temporal correlation.
- Quantization makes a controlled rate-quality tradeoff.
- Entropy coding assigns shorter representations to more probable syntax outcomes.
