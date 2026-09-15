# Study Log: September 30 Presentation

## Goal

Prepare a clear 20-25 minute presentation for **September 30, 2026** titled:

> **From Coding Decisions to the HEVC Bitstream**

The presentation should connect the coding loop from Presentation 2 to the structure that lets a decoder interpret the result. It should answer the main unresolved questions from the previous discussion before introducing new bitstream terminology.

**Available time:** about 2 hours per day  
**Preparation window:** September 15-29  
**Presentation day:** September 30  
**Total planned preparation:** about 30 hours, plus a short review on presentation day

## How to use the online material

The internet readings below are part of each day's two-hour allocation; they are not additional homework. Read with a question in mind and stop when you can produce the day's output. Do not attempt to read a standards document or research paper from beginning to end.

If a DOI page does not provide full-text access, record **access blocked** and use the local HEVC book or another indexed primary source. Do not silently replace a technical paper with an uncited blog post.

Use sources in this order:

1. **Orientation:** the HEVC overview paper [[S3]](../SOURCES.md#s3).
2. **One focused paper:** the source assigned to that day's question.
3. **Normative verification:** the H.265 specification [[S1]](../SOURCES.md#s1), only when exact syntax or decoder behavior matters.
4. **Implementation evidence:** HM, x265, or FFmpeg documentation [[S22]](../SOURCES.md#s22) [[S21]](../SOURCES.md#s21) [[S63]](../SOURCES.md#s63).

### Core online library for this presentation

| Question | Online resource | What to extract |
|---|---|---|
| What does HEVC standardize? | [Overview of the HEVC Standard](https://doi.org/10.1109/TCSVT.2012.2221191) [[S3]](../SOURCES.md#s3) | Decoder contract, coding architecture, and major tools |
| Why are CU, PU, and TU separate? | [Block Partitioning Structure in the HEVC Standard](https://doi.org/10.1109/TCSVT.2012.2223011) [[S53]](../SOURCES.md#s53) | The different jobs of coding, prediction, and transform partitions |
| How is prediction represented? | [Intra Coding of the HEVC Standard](https://doi.org/10.1109/TCSVT.2012.2221525) [[S54]](../SOURCES.md#s54) and [Block Merging for Quadtree-Based Partitioning](https://doi.org/10.1109/TCSVT.2012.2223051) [[S65]](../SOURCES.md#s65) | Signaled modes, merge candidates, and the distinction between encoder search and decoder reconstruction |
| How are residual coefficients written? | [Transform Coefficient Coding in HEVC](https://doi.org/10.1109/TCSVT.2012.2223055) [[S55]](../SOURCES.md#s55) | Last-significant position, significance information, levels, signs, and scan order |
| Why is CABAC difficult to parallelize? | [High Throughput CABAC Entropy Coding in HEVC](https://www.mit.edu/~sze/papers/sze_tcsvt_2012.pdf) [[S56]](../SOURCES.md#s56) | Context dependencies, bypass bins, and throughput constraints |
| How are bitstream layers organized? | [HEVC High-Level Syntax and Reference Picture Management](https://doi.org/10.1109/TCSVT.2012.2223052) [[S52]](../SOURCES.md#s52) | NAL units, parameter sets, random access, DPB, and RPS |
| How does HEVC enter containers and networks? | [System Layer Integration of HEVC](https://doi.org/10.1109/TCSVT.2012.2223054) [[S62]](../SOURCES.md#s62) | Elementary stream versus RTP, MPEG-2 TS, MP4/ISOBMFF, and DASH |
| How can I inspect a real stream? | [FFprobe documentation](https://ffmpeg.org/ffprobe.html) and [`trace_headers`](https://ffmpeg.org/ffprobe-all.html#trace_005fheaders) [[S63]](../SOURCES.md#s63) | Frames, packets, metadata, and readable syntax traces |
| What is the authoritative implementation reference? | [HEVC Test Model repository](https://vcgit.hhi.fraunhofer.de/jvet/HM) and [HM documentation](https://hevc.hhi.fraunhofer.de/HM-doc/) [[S22]](../SOURCES.md#s22) | A named reference-software version for experiments and code tracing |

The complete catalogue, including filters, complexity, VVC, AV1, and learned compression, remains in [SOURCES.md](../SOURCES.md). Those topics are future-reading material unless they directly support a slide or audience question.

## Two different completion levels

- **Presentation-ready:** I can explain the answer accurately, distinguish normative HEVC from encoder choices, and cite a suitable source.
- **Research-resolved:** I have either located the exact standards procedure or produced a reproducible trace from a named software version, as required by [open-questions.md](../open-questions.md#resolution-rule).

A question may be presentation-ready while remaining only **partial** in the research log. Do not change an open question to **resolved** merely because its verbal explanation sounds convincing.

## What has already been covered

- Presentation 1: compression motivation, spatial and temporal prediction, residuals, motion vectors, I/P/B pictures, and Group of Pictures structure.
- Presentation 2: the closed coding loop, Coding Tree Unit (CTU), Coding Unit (CU), Prediction Unit (PU), Transform Unit (TU), transform coding, quantization, Context-based Adaptive Binary Arithmetic Coding (CABAC), reconstruction, deblocking, Sample Adaptive Offset (SAO), and Rate Distortion Optimization (RDO).
- The repository now has question-based notes, a 23-chapter primer, an HEVC book memory, a source catalogue, and a record of presentation feedback.

The main weakness is no longer lack of terminology. The next step is to explain **why each structure exists, how encoder choices are made, and how the selected choices become an unambiguous bitstream**.

## Target understanding by September 29

I should be able to answer these questions without reading from notes:

- [ ] Why are CU, PU, and TU separate structures?
- [ ] How can an encoder select CU splits, PU modes, and TU partitions?
- [ ] What does RDO compare, and which parts are encoder-specific?
- [ ] How do rate control, QP, lambda, and local coding decisions differ?
- [ ] How does the decoder know which CABAC bins belong to which syntax element?
- [ ] What is an HEVC elementary bitstream?
- [ ] What is a Network Abstraction Layer (NAL) unit?
- [ ] What is the difference between Video Coding Layer (VCL) and non-VCL NAL units?
- [ ] Why are Video Parameter Set (VPS), Sequence Parameter Set (SPS), and Picture Parameter Set (PPS) separate?
- [ ] What do slices and slice segments organize?
- [ ] What is the difference between Instantaneous Decoding Refresh (IDR) and Clean Random Access (CRA)?
- [ ] What roles do the Decoded Picture Buffer (DPB) and Reference Picture Sets (RPS) play?
- [ ] What is the difference between profile, tier, and level?
- [ ] Why is an HEVC elementary stream different from an MP4 or another container?

## Daily plan

Mark a day complete only after producing the stated output. Reading alone does not count.

### September 15: Establish the target

- [ ] 20 min: Read [presentation feedback](../ai%20build/feedback.md), especially the September 14 section.
- [ ] 20 min: Read the [presentation discussion summary](../presentation/PRESENTATION_DISCUSSION_SUMMARY.md).
- [ ] 20 min: Read only the abstract, introduction, and high-level architecture portion of the [HEVC overview paper](https://doi.org/10.1109/TCSVT.2012.2221191) [[S3]](../SOURCES.md#s3). **Stop when:** I can state what the standard defines and name the encoder decisions it leaves open.
- [ ] 45 min: Review the titles and speaking flow of [Presentation 2](../presentation/Presentation%202%20-%20Inside%20the%20HEVC%20Coding%20Loop.pptx).
- [ ] 15 min: Write five points that were unclear in the previous talk.

**Output:** a five-item weakness list.  
**Mastery check:** explain why a third presentation should build on Presentation 2 rather than repeat it.

### September 16: CU selection

- [ ] 35 min: Read [What are CTU, CU, PU, and TU?](../notes/what-are-ctu-cu-pu-tu.md).
- [ ] 35 min: Read only the abstract, introduction, and portions defining the coding tree and CU hierarchy in [Block Partitioning Structure in the HEVC Standard](https://doi.org/10.1109/TCSVT.2012.2223011) [[S53]](../SOURCES.md#s53). **Stop when:** I can separate legal quadtree syntax from the encoder's split search.
- [ ] 35 min: Compare split and no-split CU candidates using the RDO explanation in [the RDO note](../notes/what-is-rate-distortion-optimization.md).
- [ ] 15 min: Explain the decision aloud without using the phrase “HEVC decides.” Name the encoder as the decision-maker.

**Output:** one page answering “How can an encoder select a CU split?”  
**Mastery check:** distinguish legal CU syntax from an encoder's search strategy.

### September 17: PU selection and prediction modes

- [ ] 30 min: Review the [intra-prediction note](../notes/how-does-intra-prediction-work.md) and [inter-prediction note](../notes/how-does-inter-prediction-work.md).
- [ ] 20 min: Read only the abstract, overview, and portions defining reference samples and luma prediction modes in [Intra Coding of the HEVC Standard](https://doi.org/10.1109/TCSVT.2012.2221525) [[S54]](../SOURCES.md#s54). **Stop when:** I can explain what the encoder selects and what the decoder reconstructs.
- [ ] 20 min: Read only the abstract, motivation, and merge-candidate derivation in [Block Merging for Quadtree-Based Partitioning](https://doi.org/10.1109/TCSVT.2012.2223051) [[S65]](../SOURCES.md#s65). **Stop when:** I can explain why inheriting motion data can reduce signaling.
- [ ] 35 min: Draw one CU with two legal prediction choices and list the syntax cost and prediction error each could produce.
- [ ] 15 min: Give a two-minute spoken explanation.

**Output:** a CU-to-PU decision sketch.  
**Mastery check:** explain why the decoder does not perform motion estimation or mode search.

### September 18: Why TU is independent of PU

- [ ] 30 min: Read [the transform note](../notes/what-is-transform-coding-and-why-dct.md).
- [ ] 15 min: Read only the transform-tree and PU/TU independence portions of the [block-partitioning paper](https://doi.org/10.1109/TCSVT.2012.2223011) [[S53]](../SOURCES.md#s53). **Stop when:** I can state the different questions PU and TU answer.
- [ ] 15 min: Read only the introduction and transform-block syntax overview in [Transform Coefficient Coding in HEVC](https://doi.org/10.1109/TCSVT.2012.2223055) [[S55]](../SOURCES.md#s55). **Stop when:** I can explain why changing TU partitioning changes the coded coefficient structure.
- [ ] 40 min: Take one fixed PU and sketch two different residual patterns that need different TU partitions.
- [ ] 20 min: Explain what would be lost if TU boundaries had to match PU boundaries.

**Output:** one fixed-PU, two-residual comparison.  
**Mastery check:** state the separate questions answered by PU and TU.

### September 19: Quantization and QP control

- [ ] 30 min: Read [What are quantization and QP?](../notes/what-is-quantization-and-qp.md).
- [ ] 35 min: Study only the QP, CRF, ABR, and VBV entries in the [x265 command-line documentation](https://x265.readthedocs.io/en/stable/cli.html) [[S21]](../SOURCES.md#s21). **Stop when:** I can classify each control as user policy, encoder rate control, or signaled decoder information.
- [ ] 35 min: Make a four-level map: user target, rate control, signaled QP information, and local encoder decisions.
- [ ] 20 min: Explain why “RDO chooses QP” is incomplete.

**Output:** a QP control map.  
**Mastery check:** explain what approximately doubles when QP increases by six, without claiming that bitrate automatically halves.

### September 20: RDO as the encoder's comparison method

- [ ] 30 min: Read [the RDO note](../notes/what-is-rate-distortion-optimization.md).
- [ ] 35 min: Read only the introduction, Lagrangian-cost formulation, and conclusion of [Rate-Distortion Optimization for Video Compression](https://doi.org/10.1109/79.733497) [[S44]](../SOURCES.md#s44). Skip derivations not needed for `J = D + lambda R`. **Stop when:** I can explain why the minimum-distortion and minimum-rate candidates need not win.
- [ ] 35 min: Build one labeled teaching example for `J = D + lambda R`.
- [ ] 20 min: Recalculate the winner after increasing lambda and explain the change.

**Output:** one correctly labeled illustrative RDO table.  
**Mastery check:** distinguish distortion, estimated or coded rate, lambda, and implementation-specific pruning.

### September 21: Accurate CABAC syntax

- [ ] 30 min: Read [What is CABAC entropy coding?](../notes/what-is-cabac-entropy-coding.md).
- [ ] 25 min: Read Chapter 8 of [HEVC Book Memory](../ai%20build/memory.md#8-cabac-memory-correct-syntax-story).
- [ ] 15 min: Read only the introduction and coefficient-syntax sequence in [Transform Coefficient Coding in HEVC](https://doi.org/10.1109/TCSVT.2012.2223055) [[S55]](../SOURCES.md#s55). Use the paper's syntax names rather than the old invented count field. **Stop when:** I can order the syntax elements without inventing separators.
- [ ] 10 min: Read only the motivation, dependency discussion, and conclusion in [High Throughput CABAC Entropy Coding in HEVC](https://www.mit.edu/~sze/papers/sze_tcsvt_2012.pdf) [[S56]](../SOURCES.md#s56). **Stop when:** I can explain why context-coded bins constrain throughput.
- [ ] 25 min: Write the transform-block syntax order: last significant position, coefficient-group data, significance flags, levels, signs, and remainders.
- [ ] 15 min: List every part of the old example that must remain labeled as an illustration.

**Output:** an accurate residual-syntax flow.  
**Mastery check:** explain why HEVC does not need an invented “number of positions to check” field.

### September 22: Close the Presentation 2 loop

- [ ] 25 min: Read [the hybrid pipeline note](../notes/what-is-the-hybrid-codec-pipeline.md).
- [ ] 10 min: Review [the filter note](../notes/what-are-deblocking-and-sao-filters.md).
- [ ] 15 min: Read only the abstracts, process overviews, and conclusions of the [HEVC deblocking filter](https://doi.org/10.1109/TCSVT.2012.2223053) [[S57]](../SOURCES.md#s57) and [Sample Adaptive Offset](https://doi.org/10.1109/TCSVT.2012.2221529) [[S58]](../SOURCES.md#s58). **Stop when:** I can name each filter's input, correction, output, and effect on future references.
- [ ] 40 min: Narrate one block from prediction through storage as a future reference.
- [ ] 30 min: Review all six maintained questions in [open-questions.md](../open-questions.md). For each one, mark the explanation **presentation-ready** or **not ready**, while preserving its independent research status of open, partial, or resolved.

**Output:** a five-minute coding-loop explanation.  
**Checkpoint:** do not move to bitstream structure until CU, PU, TU, QP, and CABAC can be explained without contradiction.

### September 23: Elementary stream and NAL units

- [ ] 30 min: Read [Chapter 22 of the primer](../TEXTBOOK.md#chapter-22--bitstream-structure).
- [ ] 20 min: Read Chapter 2 of [HEVC Book Memory](../ai%20build/memory.md#2-high-level-syntax-and-bitstream-memory), through the NAL-unit section.
- [ ] 25 min: Read only the introduction and portions covering NAL units and parameter sets in [HEVC High-Level Syntax and Reference Picture Management](https://doi.org/10.1109/TCSVT.2012.2223052) [[S52]](../SOURCES.md#s52). **Stop when:** I can draw the hierarchy from elementary stream to coded slice data.
- [ ] 20 min: Draw the stack: container or transport, HEVC elementary stream, NAL units, slice syntax, CTUs, and coded data.
- [ ] 15 min: Begin the exact [reproducible bitstream experiment](artifacts/README.md) using [FFprobe](https://ffmpeg.org/ffprobe.html) and [`trace_headers`](https://ffmpeg.org/ffprobe-all.html#trace_005fheaders) [[S63]](../SOURCES.md#s63). If FFmpeg is still unavailable, complete the documented prerequisite check and record the block honestly.
- [ ] 10 min: Explain VCL versus non-VCL NAL units aloud.

**Output:** one end-to-end bitstream hierarchy.  
**Mastery check:** explain why an MP4 file and an HEVC elementary stream are not the same object.

### September 24: VPS, SPS, PPS, and slices

- [ ] 20 min: Continue Chapter 2 of [HEVC Book Memory](../ai%20build/memory.md#2-high-level-syntax-and-bitstream-memory).
- [ ] 25 min: Read only the portions on parameter-set activation and slice-segment headers in the [high-level-syntax paper](https://doi.org/10.1109/TCSVT.2012.2223052) [[S52]](../SOURCES.md#s52). Use the [H.265 recommendation](https://www.itu.int/rec/T-REC-H.265) [[S1]](../SOURCES.md#s1) only to verify an exact field or hierarchy. **Stop when:** I can explain scope and references among VPS, SPS, PPS, and slice headers.
- [ ] 25 min: Make a table for VPS, SPS, PPS, and slice-segment header: scope, examples of information, and why the information is not repeated everywhere.
- [ ] 25 min: Explain how a slice refers to parameter sets.
- [ ] 10 min: Write three questions an audience may ask.
- [ ] 15 min: Complete the [reproducible bitstream experiment](artifacts/README.md), or record the precise installation/tooling blocker. Save observations rather than relying on memory.

**Output:** a parameter-set comparison table.  
**Mastery check:** give one accurate example of information associated with each parameter set.

### September 25: References, ordering, and random access

- [ ] 15 min: Study the IRAP, IDR, CRA, DPB, and RPS sections in [HEVC Book Memory](../ai%20build/memory.md#2-high-level-syntax-and-bitstream-memory).
- [ ] 20 min: Read only the random-access and reference-picture-management portions of the [high-level-syntax paper](https://doi.org/10.1109/TCSVT.2012.2223052) [[S52]](../SOURCES.md#s52). **Stop when:** I can distinguish IDR, CRA, DPB state, RPS, decoding order, and display order.
- [ ] 30 min: Review [I, P, B, and GOP](../notes/what-are-i-p-and-b-frames-and-gop.md).
- [ ] 35 min: Draw decoding order, display order, and retained reference pictures as three separate ideas.
- [ ] 20 min: Explain IDR versus CRA without saying every intra picture is a random-access point.

**Output:** one random-access and reference diagram.  
**Mastery check:** explain why “List 0 is past and List 1 is future” is unreliable.

### September 26: Profiles, tiers, levels, slices, tiles, and WPP

- [ ] 35 min: Read [the profiles, levels, and parallel-tools note](../notes/what-are-parallel-tools-profiles-levels.md).
- [ ] 15 min: Review the corresponding sections in Chapters 2 and 3 of [HEVC Book Memory](../ai%20build/memory.md).
- [ ] 20 min: Read only the problem statement, tiles/WPP definitions, reported tradeoffs, and conclusion in [Parallel Scalability and Efficiency of HEVC Parallelization Approaches](https://doi.org/10.1109/TCSVT.2012.2223056) [[S61]](../SOURCES.md#s61). **Stop when:** I can explain what independence or dependency each method changes and what that may cost.
- [ ] 30 min: Make two comparisons: profile versus tier versus level, and slice versus tile versus Wavefront Parallel Processing (WPP).
- [ ] 20 min: Decide which details belong in the main talk and which belong in backup.

**Output:** two concise comparison tables.  
**Mastery check:** explain why these terms cannot be used interchangeably.

### September 27: Build the presentation story

- [ ] 25 min: Write the one-sentence purpose of the talk.
- [ ] 15 min: Read only the architecture figures, introduction, and container/transport summary in [System Layer Integration of HEVC](https://doi.org/10.1109/TCSVT.2012.2223054) [[S62]](../SOURCES.md#s62). **Stop when:** I can separate HEVC syntax from packaging and transport.
- [ ] 30 min: Draft the 12-slide main structure below using one main question per slide.
- [ ] 20 min: Assign one source to every technical slide.
- [ ] 15 min: Remove repeated definitions and calculations that interrupt the story.
- [ ] 15 min: Perform one untimed story-only rehearsal, noting every broken transition.

**Output:** complete slide titles, one sentence of intent, and one source for every slide; then perform one untimed story-only rehearsal.

### September 28: Complete and verify the deck

- [ ] 60 min: Complete the main slides and speaker notes.
- [ ] 25 min: Add backup slides for detailed CABAC, RDO, and parameter-set questions.
- [ ] 20 min: Check full forms, slide numbers, citations, arrows, and image consistency.
- [ ] 15 min: Check every claim against [SOURCES.md](../SOURCES.md) or the local HEVC book. Prioritize the specification [[S1]](../SOURCES.md#s1), high-level-syntax paper [[S52]](../SOURCES.md#s52), and system-integration paper [[S62]](../SOURCES.md#s62) for the main bitstream claims.

**Output:** presentation-ready draft.  
**Mastery check:** every number is marked as measured, derived, or illustrative.

### September 29: Rehearse and repair

- [ ] 30 min: First timed rehearsal without stopping.
- [ ] 25 min: Fix only the sections that caused confusion or exceeded time.
- [ ] 30 min: Second timed rehearsal with slide pointer practice.
- [ ] 25 min: Answer the anticipated questions below aloud.
- [ ] 10 min: Confirm the final file opens and fonts, equations, and images render correctly.

**Output:** final deck and a 20-25 minute delivery.  
**Stop rule:** do not add a new technical section after the second rehearsal.

### September 30: Presentation day

- [ ] 15 min: Review the opening, the transition into bitstream structure, and the conclusion.
- [ ] 15 min: Review abbreviations and the IDR/CRA, VPS/SPS/PPS, and CU/PU/TU distinctions.
- [ ] 10 min: Check the presentation computer, display, pointer, and backup copy.
- [ ] Present slowly and answer only what can be supported.

**After the talk:** record every question, correction, and requested next topic before the details are forgotten.

## Recommended presentation structure

Use **12 main slides** for a 20-25 minute talk. Aim for about 22 minutes so questions or a slow explanation do not force the ending to be rushed.

| Slide | Main question | Target time |
|---:|---|---:|
| 1 | What will this presentation explain? | 0.5 min |
| 2 | What did the previous presentation establish about the coding loop? | 2 min |
| 3 | How do encoder choices become syntax that every decoder can interpret? | 2 min |
| 4 | What is the hierarchy from an HEVC elementary stream down to coded CTU data? | 2 min |
| 5 | What is a NAL unit, and how do VCL and non-VCL units differ? | 2 min |
| 6 | Why are VPS, SPS, and PPS separate, and how are they referenced? | 2.5 min |
| 7 | How do slices and slice-segment headers organize coded picture data? | 2 min |
| 8 | How does syntax-controlled CABAC parsing tell the decoder what each bin means? | 2.5 min |
| 9 | How do the DPB and RPS determine which reconstructed pictures remain usable? | 2 min |
| 10 | How do IDR and CRA pictures provide different forms of random access? | 2 min |
| 11 | How is an HEVC elementary stream different from MP4, MPEG-2 TS, RTP, or DASH? | 1.5 min |
| 12 | What is the complete path from encoder decision to decoded picture, and what remains open? | 1.5 min |

### Backup slides

Keep these after the closing slide and show them only in response to a question:

- CU, PU, TU, QP, and RDO decision details;
- complete CABAC coefficient-syntax trace or arithmetic-coding calculation;
- profile versus tier versus level;
- slices versus tiles versus Wavefront Parallel Processing;
- complete NAL-unit-type table;
- FFprobe and `trace_headers` evidence from the reproducible experiment.

### Slide citation rule

- Put compact source IDs in the bottom corner of each technical slide, for example `Sources: [S52], [S1]`.
- Put the full source titles and links in the speaker notes or a final references slide.
- Place a source immediately beside a measured number, table, or borrowed diagram when attribution could otherwise be ambiguous.
- Label teaching arithmetic as **illustrative**; a source citation must not make invented numbers look measured.

## Questions to rehearse

- If HEVC mainly defines decoding, why are encoder decisions discussed?
- How does the decoder know whether the next CABAC bin describes significance, magnitude, or sign?
- Why can two conforming HEVC encoders produce different bitrates?
- Why are VPS, SPS, and PPS not one structure?
- Can a picture contain multiple slices or tiles?
- Does an I picture always provide random access?
- Does CABAC encode NAL-unit headers?
- Where does RDO end and normative bitstream syntax begin?
- What information belongs to the container rather than HEVC itself?

## Daily entry template

Copy this block below the plan when recording a session:

```text
Date:
Minutes studied:
Planned output completed: yes / partly / no
Source and exact sections read:
Evidence produced or file updated:
Claim verified:
Open question affected:
Presentation readiness: ready / needs practice / not ready
Research status: open / partial / resolved
Can explain without notes:
Still unclear:
Next action:
Confidence from 1 to 5:
```

## Progress summary

| Milestone | Due | Status |
|---|---|---|
| Presentation 2 gaps identified | Sep 15 | Not started |
| CU, PU, TU, QP, RDO, and CABAC explanations presentation-ready | Sep 22 | Not started |
| Reproducible bitstream experiment recorded | Sep 24 | Not started |
| Bitstream foundations understood | Sep 26 | Not started |
| Slide story, sources, and untimed rehearsal complete | Sep 27 | Not started |
| Final deck complete | Sep 28 | Not started |
| Two timed rehearsals complete | Sep 29 | Not started |
| Presentation delivered and feedback recorded | Sep 30 | Not started |

## Scope rule

The main presentation should explain the bitstream structure and its connection to earlier coding decisions. It should not attempt to teach every CABAC calculation, every NAL-unit type, profile/tier/level details, or parallel-processing implementation. Move those topics to backup slides and keep the spoken story focused on what problem each layer solves.
