# Presentations

This folder contains the final presentation series and a concise discussion handoff. Keep only presentation-ready `.pptx` files and durable documentation here; store temporary renders and working drafts outside the repository.

| Presentation | Slides | Scope | Supporting material |
|---|---:|---|---|
| [Presentation 1 - Introduction to HEVC](Presentation%201%20-%20Introduction%20to%20HEVC.pptx) | 21 | Why video compression is necessary, what HEVC standardizes, redundancy, prediction, frame types, GOP structure, and the codec landscape | [Textbook Parts I and II](../TEXTBOOK.md#part-i-foundations) |
| [Presentation 2 - Inside the HEVC Coding Loop](Presentation%202%20-%20Inside%20the%20HEVC%20Coding%20Loop.pptx) | 49 | CTU/CU/PU/TU structure, transform, quantization, CABAC, reconstruction, in-loop filters, and rate-distortion optimization | [Textbook Part III](../TEXTBOOK.md#part-iii-inside-the-coding-loop) |

## Discussion context

[Presentation Discussion Summary](PRESENTATION_DISCUSSION_SUMMARY.md) records the progression across the July 25, August 17, and September 14 discussions, the feedback received, unresolved questions, confidentiality guidance, and the direction for the next presentation. Read it before editing either deck.

## Naming convention

Use `Presentation N - Descriptive Topic.pptx` for final decks. Avoid version numbers in this folder; keep draft versions in a separate working directory.

## Presentation sequence

1. **Introduction to HEVC:** motivation and conceptual foundation.
2. **Inside the HEVC Coding Loop:** block-level processing and encoder decisions.
3. **Immediate revision goal:** revisit Presentation 2 and explain why each structure and operation is needed.
4. **Planned later topic:** HEVC bitstream structure, including Network Abstraction Layer units, slices, parameter sets, profiles, tiers, and levels.
