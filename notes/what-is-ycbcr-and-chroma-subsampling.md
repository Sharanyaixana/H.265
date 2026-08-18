# What is YCbCr and chroma subsampling?

> Chapter 1 · Day 1 · Source: [[S27]](../SOURCES.md#s27)

## One-line answer
YCbCr splits an image into brightness (luma, Y) and two color channels (Cb, Cr); because the eye is far more sensitive to brightness than color, we can store the color channels at lower resolution (chroma subsampling, e.g. 4:2:0) with little visible loss — cutting raw size in half.

---

## How 4:2:0 cuts the size in half (the proof)

A color pixel needs **three numbers**: Y (brightness), Cb, Cr (the two color-difference channels), each 8 bits. Subsampling keeps Y for every pixel but **shares Cb/Cr across several pixels.**

Count samples on the smallest repeating unit — a **2×2 block (4 pixels):**

**4:4:4 (no subsampling — same cost as RGB):**
```
4 Y  +  4 Cb  +  4 Cr  = 12 samples for 4 pixels  →  3 samples/pixel  →  24 bits/pixel
```

**4:2:0 (one Cb + one Cr shared by the whole 2×2 block):**
```
4 Y  +  1 Cb  +  1 Cr  =  6 samples for 4 pixels  →  1.5 samples/pixel  →  12 bits/pixel
```

| | Samples / 4 px | Bits/pixel | Relative size |
|---|---|---|---|
| RGB / YCbCr 4:4:4 | 12 | 24 | 100% |
| **YCbCr 4:2:0** | **6** | **12** | **50%** |

**12 → 6 samples. That's the half.**

Why exactly half:
- **Luma (Y) is untouched** — full resolution, so the image still looks sharp.
- **Each chroma channel is cut to ¼** (halved horizontally *and* vertically): 4 samples → 1.
- Chroma total drops 8 → 2, so overall 12 → 6 = **50% gone before real compression starts.**

## Key points
- 4:4:4 = full color · 4:2:2 = half horizontal color · **4:2:0 = quarter color** (most common).
- The name `J:a:b`: over a 4-wide, 2-tall region — 4 luma wide, **2** chroma in the top row, **0** *new* chroma in the bottom row (reuses the top).
- **RGB → YCbCr alone saves nothing** (both 24 bpp). It only *enables* subsampling by separating brightness from color.
- This is a *perceptual* redundancy trick, applied before the codec's main tools.

## Diagram to draw
- A 2×2 pixel grid: 4 Y samples, but only 1 Cb and 1 Cr shared across the block.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
