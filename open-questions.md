# Open Questions

This file records questions that require a source-backed answer or an implementation trace. A partial answer is a working model, not a settled result.

**Status:** open · partial · resolved

**Presentation readiness:** not assessed · needs practice · ready

Presentation readiness and research status are independent. A concise, source-backed explanation may be ready for a talk while the research question remains partial until its standards locator or implementation trace is recorded.

## Q1 Is there something beyond H.265

**Status:** partial

**Presentation readiness:** not assessed

H.266/Versatile Video Coding (VVC) is the formal successor to HEVC and targets substantially better coding efficiency with higher complexity [[S5]](SOURCES.md#s5). AV1 is a separate AOMedia codec developed under a royalty-free patent policy [[S6]](SOURCES.md#s6). AOMedia also lists AV2 as a developing next-generation specification [[S51]](SOURCES.md#s51).

**Still needed:** compare mature implementations under matched speed, content, quality metric, and hardware conditions. A single percentage is insufficient.

## Q2 How does an HEVC encoder select the CU partition

**Status:** partial

**Presentation readiness:** not assessed

The standard defines legal Coding Unit structures but not a universal encoder search. An encoder can compare split and no-split candidates using rate-distortion cost, then prune the search for speed.

**Next step:** trace one CTU in HM and x265, recording the candidates tested, pruning conditions, and final partition.

## Q3 How does an encoder select the PU partition and prediction mode

**Status:** partial

**Presentation readiness:** not assessed

The encoder compares legal Prediction Unit structures and intra or inter candidates using distortion, signaling cost, and implementation-specific screening. The decoder reconstructs the selected syntax; it does not repeat the search.

**Next step:** map legal PU structures to H.265 syntax and inspect one encoder trace.

## Q4 Why does HEVC separate TU from PU

**Status:** partial

**Presentation readiness:** not assessed

The Prediction Unit describes prediction geometry. The Transform Unit describes how the resulting residual is partitioned and transformed. A useful prediction partition does not imply that residual energy has the same spatial structure.

**Next step:** encode one fixed PU with alternative legal transform trees and compare rate, distortion, and signaling overhead.

## Q5 How is QP selected

**Status:** partial

**Presentation readiness:** not assessed

The standard defines how signaled Quantization Parameter information is derived and applied, not one encoder policy. Fixed QP, target-bitrate rate control, and implementation-specific quality modes solve different control problems. Local QP decisions can use rate-distortion analysis within that policy.

**Next step:** document x265 and HM separately: sequence or picture target, rate-control feedback, local offsets, lambda derivation, and signaled delta QP.

## Q6 How does CABAC know which bin belongs to which syntax field

**Status:** partial

**Presentation readiness:** not assessed

The decoder follows the normative H.265 syntax and parsing process. The active syntax element determines the binarization, context, and stopping rule; the arithmetic bitstream contains no textual separators. The earlier unary “number of positions” explanation was not HEVC residual syntax and has been removed.

**Next step:** trace one transform block through H.265 `residual_coding` or the HM decoder and annotate the last-significant position, significance map, level, remainder, and sign syntax.

## Resolution rule

Mark a question resolved only when the repository contains either:

- a clause-level standards explanation with a stable source; or
- a reproducible trace from a named encoder or decoder version.
