# What is quantization and QP?

> Chapter 3 · Day 6

## One-line answer
Quantization is the deliberately lossy step: each transform coefficient is divided by a step size and rounded (zeroing many small ones), controlled by the Quantization Parameter (QP ~0–51) — higher QP means fewer bits and lower quality.

## Key points
- **This is where quality is traded for bits.** Prediction, transform, and entropy coding are essentially lossless; quantization is not.
- QP is the primary knob for the rate–distortion tradeoff and rate control.
- In x265 you'll meet **CRF**, a quality-target mode built on QP.

## Verify it yourself
- Encode the same clip at a few CRF values and compare file size:
  ```
  ffmpeg -i input.mp4 -c:v libx265 -crf 18 -preset medium out_crf18.mp4
  ffmpeg -i input.mp4 -c:v libx265 -crf 28 -preset medium out_crf28.mp4
  ffmpeg -i input.mp4 -c:v libx265 -crf 38 -preset medium out_crf38.mp4
  ```
  Lower CRF → finer quantization → bigger file, higher quality. Record the sizes and see the tradeoff for yourself.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
