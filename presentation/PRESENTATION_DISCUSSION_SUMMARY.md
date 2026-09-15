# Presentation Discussion Summary

## Purpose

This is a handoff for anyone helping with the HEVC presentation series. The meeting transcripts are historical context, not technical sources. Tentative statements from a discussion must be checked against `TEXTBOOK.md`, `SOURCES.md`, and the H.265 specification before entering a slide.

## Presentation 0 July 25 2026

The discussion established the research motivation:

- Begin with image compression and the transform-quantization idea.
- Move from frame-by-frame compression to temporal prediction in video.
- Connect codec efficiency to data movement, hardware energy, and sensor constraints.
- Build knowledge gradually and maintain regular technical discussions.

The discussion used approximate language for compression ratios and HEVC gains. Those numbers should not be repeated without a cited test setup.

## Presentation 1 August 17 2026

The presentation introduced intra and inter prediction, residuals, motion vectors, I/P/B pictures, and Group of Pictures structure.

Standing feedback:

- Number every slide correctly.
- Expand an abbreviation when it first appears.
- Introduce an example before relying on abstract terminology.
- Separate display order, decoding order, reference structure, and random access.
- State what is deferred rather than improvising an uncertain answer.

Questions raised included block-size selection, intra-mode choice, and GOP latency or random-access tradeoffs.

## Presentation 2 September 14 2026

The presentation surveyed closed-loop reconstruction, CTU/CU/PU/TU structure, transforms, quantization, CABAC, filters, and Rate Distortion Optimization.

The main feedback was that the talk described process steps without establishing why each structure or operation is required. The next revision should put the problem before the mechanism:

1. What failure or inefficiency exists without this tool?
2. What alternatives can the encoder choose?
3. Which behavior is normative and which is encoder-specific?
4. What is gained in rate or quality?
5. What complexity, memory, energy, or latency is paid?

The earlier CABAC walkthrough included hypothetical fields and arbitrary arithmetic-coding probabilities. It must be presented only as an arithmetic-coding illustration or replaced with a trace of actual H.265 residual syntax.

## Open technical questions

- How is a Coding Unit split selected in a named encoder?
- How are Prediction Unit structure and prediction mode selected?
- Why is Transform Unit partitioning independent of Prediction Unit partitioning?
- How do rate control, QP signaling, local QP changes, and RDO interact?
- How does H.265 residual syntax delimit and interpret CABAC bins?

The maintained versions of these questions are in [open-questions.md](../open-questions.md).

## Presentation requirements

- Use one consistent image or block across a multi-slide example.
- Keep selected regions in the same location and maintain consistent labels.
- Use thin, aligned connectors and arrowheads.
- Avoid dense slides and redundant restatements.
- Label every numerical example as measured, derived, or illustrative.
- Cite measured claims and standards facts.
- Do not present encoder heuristics as requirements of H.265.
- Keep detailed calculations in sequential slides or backup material.

## Current direction

First revise Presentation 2 to explain the motivation and tradeoff behind each coding-loop decision. After that foundation is stable, proceed to bitstream structure: NAL units, parameter sets, slices, profiles, tiers, levels, random access, and the distinction between an HEVC elementary stream and a media container.

## Confidentiality

The September discussion identified possible future collaborative intellectual property. Keep project-specific material within authorized Ixana or lab systems. Do not publish or individually attribute collaborative research ideas without approval.

## Instructions for future AI assistance

Inspect the current deck before editing it. Preserve manual changes and do not restore intentionally deleted slides. Verify slide count and numbering after structural edits. Use repository source IDs, expand abbreviations on first use, and distinguish normative H.265 behavior, encoder implementation choices, and teaching illustrations.
