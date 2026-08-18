# Interlaced vs. progressive scan?

> Chapter 1 · Day 1 (background context — don't over-invest) · Source: [[S27]](../SOURCES.md#s27)

## One-line answer
Progressive scan stores every line of a frame as one complete snapshot in time (the "p" in 1080p); interlaced splits each frame into two time-offset half-frames (fields) of odd/even lines (the "i" in 1080i) — a legacy bandwidth trick for old CRT TVs. Modern displays and H.265 are built around progressive.

## The contrast

| | Interlaced (`1080i`) | Progressive (`1080p`) |
|---|---|---|
| One "frame" holds | **half** the lines (a *field*: odd **or** even) | **all** the lines |
| Time captured | two fields from **two different moments** | a single moment |
| Example | 1080i60 = 60 fields/s = 30 full frames/s | 1080p30 = 30 whole frames/s |
| Origin | bandwidth trick for CRT TVs | how LCD/OLED natively work |

Mental model: interlaced = tearing each photo into odd/even strips sent at slightly different times to fake smooth motion cheaply. Progressive = just sending whole photos.

## Why progressive matters (esp. for H.265)
1. **Displays are natively progressive** — LCD/OLED address every pixel; interlaced content must be *deinterlaced* first (lossy; can cause combing/flicker).
2. **Codecs assume one frame = one moment.** Motion estimation and intra/inter prediction rely on that. Interlaced fields are time-offset, breaking the clean spatial/temporal relationships → worse compression.
3. **H.265 fact:** H.264 had dedicated interlaced tools (PAFF/MBAFF). **HEVC dropped them** — it codes fields as separate pictures and just signals "interlaced" via metadata (SEI). The standards world treats interlacing as legacy; HEVC is progressive-first. In practice, essentially all HEVC content (streaming, 4K, mobile) is progressive.

## Bottom line
Recognize the `p` vs `i` distinction, but you'll almost always work with `p`. This is context, not a mastery item.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
