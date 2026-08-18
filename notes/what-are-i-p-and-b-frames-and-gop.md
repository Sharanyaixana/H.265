# What are I, P, and B frames (and GOP)?

> Chapter 2 · Day 3 · Sources: [[S27]](../SOURCES.md#s27) [[S28]](../SOURCES.md#s28)

## One-line answer
A **GOP (Group of Pictures)** is the video segment between consecutive keyframes — the smallest independently playable chunk. Inside it: **I-frames** are self-contained (random-access points), **P-frames** predict from past frames, and **B-frames** predict from both past and future frames (smallest, but need out-of-order decoding).

## The three frame types

| Type | Predicted from | Typical size (1080p) | Role |
|---|---|---|---|
| **I** (Intra) | itself only (spatial) | 80–250 kbit; largest | GOP anchor / random-access point |
| **P** (Predicted) | past reference(s) | ~30–60% of an I-frame | motion + residual vs. earlier frames |
| **B** (Bi-directional) | past **and** future | ~15–30% of an I-frame; smallest | most compressible; interpolates between neighbors |

- **Size order:** I > P > B. **Efficiency order:** B > P > I.
- Concrete cost: **removing B-frames raises bitrate ~48%** at equal quality — that's the price of low-latency live streaming. [[S28]](../SOURCES.md#s28)

## GOP structure
- A repeating pattern, e.g. `I B B P B B P B B P …`, restarting at each I-frame.
- **GOP length is an engineering knob:** short GOP → easy seeking + error recovery, bigger files; long GOP → better compression, harder to seek, more fragile. Typical VOD ≈ 2-second GOP (48 frames @ 24 fps).

## Display order ≠ decode order (the "aha")
- A B-frame needs a *future* frame as reference, so the decoder must decode that future frame **first**. Frames are stored in **decode order**, reordered to **display order** for viewing.
- The display-position index is **POC (Picture Order Count)**.
- Cost: each B-frame between P-frames adds buffering latency (~42 ms at 24 fps) — why video calls (WebRTC) often drop B-frames.

## Keyframe / GOP subtleties (good for Q&A)
- **IDR** (Instantaneous Decoder Refresh): a keyframe that also clears the reference buffer → a true random-access point.
- **CRA** (Clean Random Access, HEVC): an entry point that still lets leading pictures reference across the boundary — useful for splicing.
- **Closed GOP:** fully self-contained; required for adaptive-bitrate (ABR) switching. **Open GOP:** first B-frames may reference the previous GOP (saves ~1–3%) but breaks clean segment boundaries.
- **Hierarchical B-pyramid:** B-frames reference other B-frames in temporal layers → ~10–15% better compression in HEVC/AV1, at the cost of deeper buffering.
- **AV1 note:** display order = coding order; uses filtered non-displayable references (ALTREF/GOLDEN) instead of a classic GOP.

## Diagram to draw
- `I B B P B B P` with prediction arrows (P → back; B → back **and** forward). Below it, write decode order vs. display order and confirm they differ.

## Verify it yourself
- See the real I/P/B pattern: `ffprobe -select_streams v -show_frames -show_entries frame=pict_type -of csv input.mp4`. Change the GOP with x265 params (`keyint`, `bframes`) and re-run to watch it shift.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
