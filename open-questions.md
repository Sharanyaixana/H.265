# Open Questions

A running list of things I couldn't fully answer yet. **Don't let these block daily progress** — jot them here and return each week. Turning these into answers (or into "here's how I'd find out") is exactly how I prep for Q&A and how a researcher thinks.

Format: question → status (🔴 open / 🟡 partial / 🟢 answered) → my current best understanding.

---

### Q1. Is there an H.266, or something better than H.265?
**Status:** 🟡 partial — *(refine in your own words as you study Day 13)*

**Current understanding:**
- **Yes — H.266 exists.** It's called **VVC (Versatile Video Coding)**, finalized in **2020** by the same ITU/MPEG group (JVET) that made HEVC. Target: roughly **~50% lower bitrate than H.265** at equal quality, using the same hybrid pipeline with more/finer tools (finer partitioning, more prediction modes, better tools for screen and 360° content) — at the cost of **much higher encoder complexity**.
- **AV1** (AOMedia, ~2018) is a **royalty-free** competitor, roughly HEVC-class or better in efficiency; its adoption was helped by HEVC's messy patent-licensing situation.
- There's also earlier context: HEVC (2013) succeeded **H.264/AVC** (2003).
- **The pattern:** each new generation ≈ halves the bitrate but demands a big jump in compute/energy. Whether that trade stays worth it — and how to pay for it in **hardware/energy** — is an open research question (and a good fit for Prof. Sen's lab).

**To dig deeper:** read Bross et al., "Overview of the VVC Standard," IEEE TCSVT 2021 [[S5]](SOURCES.md#s5); and an AV1 overview paper [[S6]](SOURCES.md#s6). See [notes/how-does-h265-compare-to-h264-av1-vvc.md](notes/how-does-h265-compare-to-h264-av1-vvc.md).

---

### Q2. _(your next question here)_
**Status:** 🔴 open
**Current understanding:**

---

<!-- Add new questions above this line as they come up during study. Review weekly. -->
