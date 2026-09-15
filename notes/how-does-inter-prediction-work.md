# How does inter prediction work?

> Learning sequence: Prediction · Sources: [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7)

## Short answer

Inter prediction constructs a block from one or two previously decoded reference pictures. Motion vectors identify locations in reference-picture lists, interpolation supports fractional-sample positions, and the coded residual corrects prediction error.

## Encoder and decoder roles

The encoder searches candidate references, block partitions, and motion parameters. It signals or implies the selected information in the bitstream. The decoder does not repeat the search; it follows the decoded syntax and the normative interpolation process.

## Reference lists

- **List 0** and **List 1** contain decoded pictures available for prediction.
- A prediction can use one list or combine predictions from both lists.
- “List 0” does not universally mean past and “List 1” future. Reference-picture order and display-time direction depend on the coded structure.

## Motion information

- **Advanced Motion Vector Prediction (AMVP):** predict a motion vector from candidates and code a motion-vector difference.
- **Merge mode:** select a candidate whose motion information is derived from spatial or temporal neighbors.
- **Skip mode:** a merge-mode case with no coded residual data for the Coding Unit.

For the usual 4:2:0 case, HEVC supports quarter-luma-sample motion-vector precision; the associated chroma displacement has one-eighth-chroma-sample precision because chroma is subsampled [[S3]](../SOURCES.md#s3).

## What to remember

Inter prediction sends enough information to reproduce a prediction—not the encoder's search process—and then codes whatever residual remains.

## Related open question

See [Q3: PU partition and prediction-mode selection](../open-questions.md#q3-how-does-an-encoder-select-the-pu-partition-and-prediction-mode).
