# Why is raw video so large?

> Chapter 1 · Day 1 · Source: [[S27]](../SOURCES.md#s27)

## One-line answer
Uncompressed video stores every pixel of every frame, so the data rate is enormous (~0.75 Gbit/s for 1080p30, ~3 Gbit/s for 4K30) — far too big to store or stream, which is why compression is mandatory.

## The exercise (worked)
Raw bitrate = pixels/frame × bits/pixel × frames/sec. (8-bit, 4:2:0 → 12 bits/pixel.)

**1080p30:**
- 1920 × 1080 = 2,073,600 pixels/frame
- × 12 bits ≈ 24.9 Mbit/frame
- × 30 fps ≈ **746 Mbit/s ≈ 0.75 Gbit/s**

**4K30 (3840×2160):**
- 3840 × 2160 = 8,294,400 pixels/frame
- × 12 bits ≈ 99.5 Mbit/frame
- × 30 fps ≈ **2.99 Gbit/s ≈ 3 Gbit/s**

**Compression ratio needed:** a real 4K stream is ~15–25 Mbit/s. So from ~3,000 Mbit/s down to ~20 Mbit/s ≈ **~150× compression**. That's the mountain H.265 climbs.

## Key points
- 4:2:0 already halves color data before "real" compression starts (see [what-is-ycbcr-and-chroma-subsampling.md](what-is-ycbcr-and-chroma-subsampling.md)).
- HEVC targets ~50% of H.264's bitrate for the same quality.
- The raw number is the whole motivation for the field — feel it once, never forget it.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
