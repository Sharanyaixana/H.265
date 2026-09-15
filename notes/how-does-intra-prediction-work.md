# How does intra prediction work?

> Learning sequence: Prediction · Sources: [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3) [[S32]](../SOURCES.md#s32)

## Short answer

Intra prediction constructs a prediction block from already reconstructed samples in the same picture, usually along the top and left boundaries. The encoder tests legal modes and signals the selected mode; the decoder reproduces that prediction and adds the decoded residual.

## HEVC luma modes

HEVC defines 35 intra prediction modes for luma:

- **Planar:** predicts a smooth two-dimensional surface.
- **DC:** predicts a nearly constant block from an average of reference samples.
- **33 angular modes:** extend boundary samples into the block along different directions.

The reference samples must already be available to both encoder and decoder. When required neighbors are unavailable, the standard defines substitution behavior. Reference-sample filtering and boundary smoothing may also apply depending on block size and mode [[S1]](../SOURCES.md#s1).

## How the encoder chooses a mode

The HEVC standard defines the legal modes and how to decode them, but not one mandatory encoder search. A practical encoder commonly performs a fast screening of modes and then compares promising candidates using rate-distortion cost. A good mode reduces residual energy, but its signaling bits also count.

## Small example

If a diagonal edge continues from the reconstructed top and left neighbors, a matching angular mode can predict that edge. Horizontal or vertical modes would leave a larger residual. The encoder does not know the answer from the picture label; it evaluates candidates.

## What to remember

Intra prediction does not copy the original block. It creates a reproducible guess from already decoded spatial neighbors and codes the remaining error.
