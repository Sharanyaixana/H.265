# H.265/HEVC Research Primer

This primer supports the repository's notes and presentations. It is written for a reader who knows basic digital systems but is new to video coding.

Three labels are used throughout:

- **Normative HEVC:** behavior defined by the H.265 decoding specification.
- **Encoder choice:** a decision made by an implementation, such as x265 or HM.
- **Illustration:** a simplified example used to explain an idea; it is not a trace of the HEVC bitstream unless explicitly stated.

Use [[S1]](SOURCES.md#s1) for normative questions, [[S3]](SOURCES.md#s3) for a technical overview, and [[S7]](SOURCES.md#s7) for algorithms and architecture. Track unresolved details in [open-questions.md](open-questions.md).

## How to use this primer

Read each chapter in three passes:

1. Identify the problem the coding tool solves.
2. Separate decoder requirements from encoder decisions.
3. Explain the rate, quality, complexity, memory, or latency tradeoff aloud.

The mastery check is the minimum bar. A research-level answer should also name the evidence source, state whether a number is normative or measured, and describe the experiment needed to verify an encoder claim.

This primer and [the HEVC book memory](ai%20build/memory.md) serve different purposes. The primer teaches the subject in dependency order. The memory preserves detailed conclusions and locators from *High Efficiency Video Coding: Algorithms and Architectures* [[S7]](SOURCES.md#s7).

## Table of contents

### Part I Foundations

1. [Why compress video at all](#chapter-1--why-compress-video-at-all)
2. [Digital video representation](#chapter-2--digital-video-representation)
3. [Interlaced and progressive scan](#chapter-3--interlaced-and-progressive-scan)
4. [Redundancy in video](#chapter-4--redundancy-in-video)
5. [The hybrid codec pipeline](#chapter-5--the-hybrid-codec-pipeline)

### Part II Prediction and temporal structure

6. [I P and B pictures and GOP structure](#chapter-6--i-p-and-b-pictures-and-gop-structure)
7. [Intra prediction](#chapter-7--intra-prediction)
8. [Inter prediction](#chapter-8--inter-prediction)
9. [HEVC AVC AV1 and VVC](#chapter-9--hevc-avc-av1-and-vvc)
10. [Research directions](#chapter-10--research-directions)

### Part III Inside the coding loop

11. [Closed-loop reconstruction](#chapter-11--closed-loop-reconstruction)
12. [CTU CU PU and TU](#chapter-12--ctu-cu-pu-and-tu)
13. [Transform coding](#chapter-13--transform-coding)
14. [Quantization and QP](#chapter-14--quantization-and-qp)
15. [CABAC entropy coding](#chapter-15--cabac-entropy-coding)
16. [Deblocking and SAO](#chapter-16--deblocking-and-sao)
17. [Rate Distortion Optimization](#chapter-17--rate-distortion-optimization)
18. [One block through the encoder](#chapter-18--one-block-through-the-encoder)

### Part IV Research and implementation

19. [Encoder complexity](#chapter-19--encoder-complexity)
20. [Connection to Professor Sen's research](#chapter-20--connection-to-professor-sens-research)
21. [Quality metrics and BD-rate](#chapter-21--quality-metrics-and-bd-rate)
22. [Bitstream structure](#chapter-22--bitstream-structure)
23. [Reading hardware papers critically](#chapter-23--reading-hardware-papers-critically)

### Appendices

- [Glossary](#appendix-a-glossary)
- [Reading map](#appendix-b-reading-map)
- [Formula reference](#appendix-c-formula-reference)
- [Further-reading routes](#appendix-d-further-reading-routes)

---

# Part I Foundations

## Chapter 1 — Why compress video at all

Raw-video rate is determined by resolution, frame rate, bit depth, and chroma format. For 8-bit YCbCr 4:2:0, the average storage is 12 bits per pixel:

```text
1920 × 1080 × 30 × 12 ≈ 746.5 Mbit/s
3840 × 2160 × 30 × 12 ≈ 2.99 Gbit/s
```

These calculations exclude audio and transport overhead. RGB at 8 bits per component uses 24 bits per pixel and therefore produces twice the rate of the 4:2:0 examples.

Compression is possible because video samples are not independent. Nearby samples often resemble one another, successive pictures often contain the same scene, and the resulting syntax values have non-uniform probabilities. Lossy coding also accepts controlled reconstruction error to achieve rates that lossless coding cannot [[S27]](SOURCES.md#s27).

HEVC's first edition was approved in 2013. Its development objective was substantially better compression than H.264/Advanced Video Coding (AVC); comparative tests reported roughly 50% bitrate reduction at comparable quality under their test conditions [[S3]](SOURCES.md#s3) [[S4]](SOURCES.md#s4). This is a benchmark result, not a guarantee for every sequence or implementation.

HEVC was developed jointly by ITU-T Video Coding Experts Group and ISO/IEC Moving Picture Experts Group through the Joint Collaborative Team on Video Coding. The standard primarily defines the compressed representation and the conforming decoding process. It does not prescribe one motion search, mode-decision strategy, rate controller, or hardware architecture. This distinction explains why two conforming encoders can produce different rate, quality, latency, and energy results for the same source.

Interoperability is bounded through three concepts introduced formally later:

- a **profile** selects supported coding features and formats;
- a **tier** selects a bitrate capability class;
- a **level** limits workload and buffering, including picture size and sample rate.

The standard also leaves preprocessing, display processing, error concealment for damaged streams, and most encoder optimization outside the normative decoding process [[S1]](SOURCES.md#s1) [[S7]](SOURCES.md#s7).

**Mastery check:** Given a resolution, frame rate, bit depth, and chroma format, calculate the raw bitrate and state every assumption.

## Chapter 2 — Digital video representation

A digital picture contains component sample arrays. Resolution describes spatial sampling; frame rate describes temporal sampling; bit depth determines the number of representable values per sample.

Video commonly uses YCbCr rather than RGB. Y represents luma; Cb and Cr are chroma-difference components. The conversion alone does not save samples. It enables chroma subsampling because fine chroma detail is often less visually important than fine luma detail.

For a 2 × 2 luma area:

```text
4:4:4 = 4 Y + 4 Cb + 4 Cr = 12 samples
4:2:0 = 4 Y + 1 Cb + 1 Cr =  6 samples
```

Thus 4:2:0 uses half the samples of 4:4:4 at the same bit depth. Each chroma plane has one quarter as many samples as the luma plane. Exact chroma siting depends on the format and cannot be inferred completely from the `4:a:b` notation.

**Mastery check:** Explain why RGB-to-YCbCr conversion and chroma subsampling are different operations.

## Chapter 3 — Interlaced and progressive scan

Progressive video represents a complete picture at each nominal time. Interlaced video uses two fields containing alternating lines, normally captured at different times. Combining time-separated fields without motion-aware deinterlacing can create combing around moving objects.

Interlacing was developed for television systems that needed acceptable refresh behavior within limited bandwidth. It complicates modern coding because neighboring lines can belong to different moments. HEVC can represent field sequences but does not use AVC's macroblock-adaptive and picture-adaptive frame/field coding tools [[S1]](SOURCES.md#s1) [[S3]](SOURCES.md#s3).

Notation such as `1080i60` can be ambiguous across industries. State the field rate explicitly when precision matters.

**Mastery check:** Explain why a field is not a complete progressive frame.

## Chapter 4 — Redundancy in video

Four categories are useful for organizing codec tools:

| Category | Meaning | Typical response |
|---|---|---|
| Spatial | Nearby samples within a picture are correlated | Intra prediction and transforms |
| Temporal | Pictures close in time contain related content | Inter prediction and motion compensation |
| Statistical | Syntax outcomes have unequal probabilities | Context modeling and arithmetic coding |
| Perceptual | Some reconstruction errors matter less to observers | Chroma subsampling and quantization decisions |

Prediction and transforms reshape data; they do not automatically reduce the bit count. Quantization and entropy coding exploit the resulting distribution. Lossless methods may use spatial, temporal, and statistical structure as long as the original samples remain recoverable. Perceptual reduction is inherently lossy.

**Mastery check:** For each major coding tool, identify the redundancy it is intended to exploit.

## Chapter 5 — The hybrid codec pipeline

HEVC belongs to the hybrid prediction-transform family [[S3]](SOURCES.md#s3):

```text
source block + reproducible prediction
              |
              v
       residual = source - prediction
              |
              v
      transform and quantization
              |
        quantized information
          /                 \
         v                   v
bitstream path        reconstruction path
CABAC and syntax      inverse quantization
         |            inverse transform
         v            add prediction
   transmitted bits   deblocking and SAO
                              |
                              v
                      future reference picture
```

The bitstream contains more than coefficient levels. It may signal partitioning, prediction modes, reference indices, motion-vector differences, transform information, quantization changes, and filter parameters.

After quantization, a reconstruction path applies inverse scaling and transform, adds the same prediction, filters the result, and stores it for possible future reference. This local decoder is needed because the external decoder never has access to the pristine source picture.

Prediction, transform, and quantization prepare syntax that is cheaper to represent. Context-based Adaptive Binary Arithmetic Coding (CABAC) converts the selected syntax to bits. At the encoder, those CABAC output bits are not passed into the filters; the reconstruction branch uses the selected quantized levels directly. At the decoder, CABAC recovers the same syntax values before reconstruction. The two branches therefore share information but serve different purposes.

The standard defines legal bitstreams and the decoding process. It does not prescribe one encoder search, mode-decision, or rate-control algorithm [[S1]](SOURCES.md#s1).

**Mastery check:** Draw both the bitstream path and reconstruction path and explain why they share the same quantized information.

## Part I checkpoint

Before continuing, the reader should be able to calculate raw bitrate, explain 4:2:0 sample reduction, distinguish four redundancy types, and draw the two branches of a hybrid codec. If any step depends on memorized labels rather than cause and effect, revisit the corresponding chapter.

---

# Part II Prediction and temporal structure

## Chapter 6 — I P and B pictures and GOP structure

I, P, and B are formally slice types. The corresponding picture labels are useful shorthand when a picture uses that coding behavior.

| Slice type | Permitted prediction |
|---|---|
| I | Intra coding; no inter prediction from other pictures |
| P | Intra coding or inter prediction from Reference Picture List 0 |
| B | Intra coding or inter prediction from List 0, List 1, or both |

Reference lists are decoder structures; they should not be defined simply as “past” and “future.” Their contents depend on picture order and the reference-picture configuration. A B picture can also be used as a reference.

A Group of Pictures (GOP) describes an encoder's chosen picture and dependency pattern. It is not necessarily a self-contained segment. GOP design affects coding efficiency, random-access interval, latency, buffering, and error propagation.

An intra picture is not automatically a clean random-access point. HEVC defines Intra Random Access Point types such as Instantaneous Decoding Refresh (IDR) and Clean Random Access (CRA). Display order can differ from decoding order when a picture depends on another picture displayed later [[S1]](SOURCES.md#s1).

**Mastery check:** Explain the difference between slice type, reference direction, GOP structure, and random-access type.

## Chapter 7 — Intra prediction

Intra prediction uses already reconstructed samples in the same picture. For luma, HEVC defines planar, DC, and 33 angular modes. The standard also defines reference-sample availability, substitution, filtering, and mode derivation [[S1]](SOURCES.md#s1) [[S32]](SOURCES.md#s32).

An angular mode extends boundary samples into the block along a selected direction. Planar prediction models a smooth two-dimensional surface; DC prediction uses a nearly constant estimate.

The encoder decides which legal mode to use. A practical implementation may screen candidates using a fast distortion measure and then perform more accurate rate-distortion comparisons. The decoder does not repeat this search; it reconstructs the signaled mode.

The selected mode is coded efficiently using neighboring-mode information and a Most Probable Mode list. More modes can reduce residual energy, but mode signaling and search complexity also matter.

The decoder first forms an ordered reference array from available reconstructed samples above and to the left of the current block. When required neighbors are unavailable, the specification defines substitution. Some block sizes and modes apply reference smoothing before prediction. Angular modes then project those boundary samples into the block, with interpolation when the projected position lies between samples. Planar and DC modes use separate equations.

The encoder normally compares several legal modes. A mode that minimizes prediction error can still lose after the encoder includes mode-signaling bits and the rate of the quantized residual. This is the first concrete example of why prediction and entropy coding cannot be optimized independently.

**Further reading:** Use the dedicated intra-coding paper for the design rationale and per-tool analysis [[S54]](SOURCES.md#s54). Use the specification when exact reference availability, interpolation, or mode derivation matters [[S1]](SOURCES.md#s1).

**Mastery check:** Given a simple edge pattern, sketch several candidate predictions and explain why the lowest pixel error is not automatically the final choice.

## Chapter 8 — Inter prediction

Inter prediction constructs samples from decoded reference pictures. The encoder searches candidate references, partitions, and motion parameters. Motion compensation then applies the selected parameters at both encoder and decoder.

- **Motion estimation:** encoder-side search; not standardized.
- **Motion compensation:** normative reconstruction from reference indices, motion vectors, and interpolation filters.
- **AMVP:** Advanced Motion Vector Prediction; signal a predictor choice and motion-vector difference.
- **Merge mode:** derive motion information from a selected spatial or temporal candidate.
- **Skip mode:** a merge-mode case without coded residual data for the Coding Unit.

HEVC supports fractional-sample motion compensation. In common 4:2:0 coding, luma motion has quarter-sample precision and the corresponding chroma displacement is represented at one-eighth-chroma-sample precision [[S3]](SOURCES.md#s3).

Bi-prediction combines two predictions. It is not necessarily a simple average of the immediately previous and next displayed pictures.

HEVC reduces motion-information cost through two related mechanisms:

- **Advanced Motion Vector Prediction (AMVP):** the bitstream identifies a predictor and codes a motion-vector difference.
- **Merge mode:** the bitstream selects a candidate whose reference and motion information are inherited together. **Skip** is a merge case with no coded residual for the CU.

Candidate construction uses specified spatial and temporal locations so the decoder can reproduce the same list. The encoder decides which candidate, reference picture, PU structure, and residual option to select.

In the book's HM 8.0 experiments, merge and skip together provided roughly 6-8% average BD-rate savings across the reported inter configurations. In an HM 6.0 interpolation substitution experiment, HEVC's luma interpolation filters provided average savings of 4.0% for Random Access, 4.9% for Low Delay B, and 2.6% for Low Delay P. These numbers isolate particular tool changes under old reference-software conditions; they are not current encoder guarantees [[S7]](SOURCES.md#s7).

Fractional motion requires interpolation because the referenced position may fall between stored samples. HEVC uses separable finite impulse response filters for luma and chroma interpolation. The increased filter support can improve prediction but raises arithmetic work and reference-memory access. Weighted prediction can compensate for brightness changes such as fades by scaling and offsetting a reference prediction [[S7]](SOURCES.md#s7).

**Further reading:** Read the block-merging paper after the overview to understand why merge mode reduces repeated motion data [[S65]](SOURCES.md#s65). Then inspect x265's motion and analysis options as one encoder implementation, not as normative HEVC [[S21]](SOURCES.md#s21).

**Mastery check:** Distinguish the encoder's motion search from the decoder's motion-compensation operation.

## Chapter 9 — HEVC AVC AV1 and VVC

| Codec | Publication | Position |
|---|---:|---|
| H.264/AVC | 2003 | Widely deployed predecessor to HEVC |
| H.265/HEVC | 2013 | Higher compression efficiency with greater complexity and licensing friction |
| AV1 | 2018 | Separate AOMedia codec developed under a royalty-free patent policy |
| H.266/VVC | 2020 | Formal successor to HEVC with a broader and more flexible tool set |

HEVC and VVC standardization reports use controlled rate-distortion tests to quantify average improvements [[S4]](SOURCES.md#s4) [[S5]](SOURCES.md#s5). AV1 comparisons vary with encoder version, speed setting, content, metric, and bitrate. “AV1 is 30–50% better than HEVC” is therefore not a defensible universal statement.

AOMedia now lists AV2 as a next-generation specification [[S51]](SOURCES.md#s51). Its maturity and measured performance should be checked before comparing it with deployed codecs.

**Further reading:** Start with the overview papers to compare design ideas [[S5]](SOURCES.md#s5) [[S6]](SOURCES.md#s6). Move to the VTM reference software for VVC [[S67]](SOURCES.md#s67), and to the AV1 specification plus SVT-AV1 design notes for AV1 [[S68]](SOURCES.md#s68) [[S69]](SOURCES.md#s69). A software encoder is evidence of one implementation strategy; a specification defines the interoperable decoding contract.

**Mastery check:** When someone quotes a percentage improvement, ask which encoders, sequences, settings, metric, and aggregation method produced it.

## Chapter 10 — Research directions

HEVC research is not limited to inventing new normative coding tools. Important directions include:

- faster partition, mode, and motion search;
- rate control and perceptual bit allocation;
- hardware acceleration and memory-system design;
- parallel processing and low-latency implementations;
- approximate computing under controlled quality constraints;
- machine-learning-assisted decisions inside hybrid encoders;
- learned video compression;
- migration among HEVC, AV1, VVC, and emerging formats.

A systems study should consider bitrate, distortion, complexity, energy, memory traffic, and latency. Improving one dimension may worsen another.

For learned compression, first identify whether a paper augments a conventional hybrid codec or replaces parts of it. *Deep Contextual Video Compression* is a useful entry point because it explicitly reframes temporal coding as learned conditional coding [[S70]](SOURCES.md#s70). Reproduce or inspect such work only after the conventional rate-distortion loop is clear; otherwise familiar names such as “prediction,” “residual,” and “entropy model” can hide important architectural differences.

One possible research formulation is an energy-aware encoder objective:

```text
J_system = D + lambda R + mu E
```

Here `E` could represent measured or modeled energy and `mu` its weight. This is a research formulation, not normative HEVC and not a claim that a production encoder uses this exact equation. A valid study must define the energy boundary, preserve bitstream conformance where claimed, and compare against a named encoder anchor.

**Mastery check:** State a research hypothesis with a measurable baseline, intervention, workload, quality metric, and energy or latency metric.

## Part II checkpoint

Explain one short sequence in display and decoding order. For each predicted block, identify the reference list, motion information, and whether the work belongs to encoder search or decoder reconstruction. Then compare HEVC with another codec without using an unsupported universal percentage.

---

# Part III Inside the coding loop

## Chapter 11 — Closed-loop reconstruction

Suppose a block of source samples is `s`, its prediction is `p`, and the residual is:

```text
e = s - p
```

After transform, quantization, inverse scaling, and inverse transform, the reconstructed residual is `e_hat`. The reconstructed block is:

```text
s_hat = p + e_hat
```

Because quantization is lossy, `e_hat` generally differs from `e`. Future predictions must use the filtered reconstruction available to the decoder, not the source picture available only to the encoder. Otherwise, encoder and decoder form different predictions from different references, producing drift.

The encoder therefore contains a local reconstruction path that follows the same normative inverse operations and in-loop filtering as the decoder. This does not remove ordinary coding distortion; it prevents an additional encoder-decoder mismatch from accumulating [[S33]](SOURCES.md#s33) [[S42]](SOURCES.md#s42).

The mismatch propagates because inter prediction is recursive. If the encoder predicts picture `n+1` from the source version of picture `n`, while the decoder predicts it from the quantized reconstruction of picture `n`, both sides subtract or add different predictions. The resulting disagreement can continue through later references even when both sides parse the same motion vector. Closed-loop reconstruction prevents this reference mismatch; it does not recover information already removed by quantization.

**Mastery check:** Explain drift without claiming that closed-loop coding eliminates quantization error.

## Chapter 12 — CTU CU PU and TU

HEVC separates four related structures [[S1]](SOURCES.md#s1):

| Unit | Role |
|---|---|
| Coding Tree Unit (CTU) | Top-level coding region and associated syntax |
| Coding Unit (CU) | Region produced by recursive coding-tree partitioning |
| Prediction Unit (PU) | Geometry and syntax used to form prediction blocks |
| Transform Unit (TU) | Geometry and syntax used to transform and code residual blocks |

A CTU contains corresponding luma and chroma Coding Tree Blocks. In the commonly taught Main-profile configuration, luma Coding Tree Blocks can be as large as 64 × 64, while minimum luma Coding Blocks are commonly 8 × 8. Sequence parameters constrain the legal sizes.

The distinction between “unit” and “block” matters:

- a **block** is a rectangular sample array for one component;
- a **unit** groups one or more component blocks with the syntax needed to decode them.

Thus, one CTU commonly groups a luma Coding Tree Block, corresponding chroma Coding Tree Blocks, and associated syntax. It should not be described as only one 64 × 64 luma picture crop.

HEVC's CU structure uses recursive quadtree partitioning. Prediction partitions and the Residual Quad Tree (RQT) attach to a CU but answer different questions. The RQT recursively selects Transform Units within its legal depth and size constraints. A prediction boundary may follow object motion, while residual energy may require a different transform partition.

### Why three decision structures are needed

- **CU:** decides where coding decisions share a common region and syntax context.
- **PU:** describes the geometry and parameters used to form prediction.
- **TU:** describes how the residual is partitioned for transform and coefficient coding.

A quadtree CU split always produces four smaller squares. PU partition shapes add directional rectangular and asymmetric choices without making the entire coding tree recursively irregular. TU recursion remains separate because a good prediction geometry does not guarantee that the remaining error has the same spatial structure.

For example, one rectangular PU may predict an object well while leaving residual energy concentrated near only one edge. Splitting that residual into smaller TUs can localize the edge without forcing a different motion model. Conversely, several prediction regions can produce a smooth residual that benefits from a larger transform where legal.

H.264/AVC used a 16 × 16 macroblock as its basic unit but also supported smaller prediction and transform partitions. HEVC's contribution is a larger and more flexible hierarchy, not the first use of variable block sizes.

How an encoder selects among legal partitions is not normative. Exhaustive comparison is possible in principle but practical encoders prune the search.

The encoder may compare split and no-split candidates using rate-distortion cost. The rate includes split flags, prediction syntax, transform syntax, and coefficient data, not only coefficient count. Production encoders use fast screening and early termination because the combined CU, PU, prediction, motion, and TU search grows rapidly [[S7]](SOURCES.md#s7).

The block-structure experiments in the book attribute more than half of the reported HEVC-over-AVC gain for high-definition content to flexible block partitioning under their tested configuration. The correct lesson is that partition flexibility contributed strongly in those experiments, not that one partition size always produces a fixed percentage saving [[S7]](SOURCES.md#s7).

**Further reading:** The block-partitioning paper is the most direct answer to why CU, PU, and TU are separate [[S53]](SOURCES.md#s53). Follow it with one CTU trace in a named HM release [[S22]](SOURCES.md#s22).

**Mastery check:** Explain why PU and TU are separate without relying only on their names.

## Chapter 13 — Transform coding

The transform converts a residual block into coefficients of spatial basis patterns. Correlated residuals often concentrate energy into fewer coefficients, which makes quantization and entropy coding more effective.

HEVC defines integer transforms related to the Discrete Cosine Transform for 4 × 4, 8 × 8, 16 × 16, and 32 × 32 transform blocks. It also defines an alternative transform derived from the Discrete Sine Transform for 4 × 4 intra-predicted luma residual blocks [[S3]](SOURCES.md#s3).

The transform does not mean “remove all high frequencies.” A smooth residual often concentrates energy at low frequencies; sharp detail can produce important high-frequency coefficients. Quantization decides which coefficient precision is retained.

Larger transforms can capture broad correlation but cost more computation and may represent localized structure poorly. Smaller transforms localize detail at the cost of more partition signaling and less broad energy compaction.

The normative decoder uses fixed-point integer operations, shifts, and clipping. Quantization is the principal deliberate loss; avoid describing every finite-precision transform as perfectly lossless or mathematically identical to a floating-point DCT.

### Separable transform calculation

An `N × N` two-dimensional transform can be implemented as a one-dimensional transform across rows followed by another across columns, with intermediate transposition or storage. This separability reduces implementation cost compared with a direct two-dimensional matrix operation.

HEVC's transform matrices use 8-bit integer coefficients so independent decoder implementations can reproduce the same result with practical multiplier hardware. The book describes an engineering compromise among closeness to the ideal DCT, near orthogonality, similar row norms, and hardware-friendly coefficient precision. Those properties cannot all match the floating-point DCT perfectly with such constrained coefficients. The selected matrices preserve row norms closely enough that HEVC does not require the same default frequency-dependent inverse-scaling compensation used by AVC's integer transform. Smaller transform matrices are embedded within the larger design, allowing datapath reuse across sizes [[S7]](SOURCES.md#s7).

### Transform size as a coding decision

A larger transform can compact correlation across a wider residual region, especially when the residual changes smoothly. It also costs more computation and can spread the energy of a localized edge over more coefficients. A smaller transform localizes detail but adds transform-tree syntax and may lose broad correlation. This is why TU size is adaptive rather than fixed globally.

In the book's HM 9.0.1 substitution experiment, allowing 16 × 16 and 32 × 32 transforms in addition to 4 × 4 and 8 × 8 produced average BD-rate savings of about 5.6% for All Intra, 6.4% for Random Access, and 6.8% for Low Delay B under the reported conditions [[S7]](SOURCES.md#s7). These are experiment-specific tool contributions, not universal gains.

HEVC also provides transform skip for selected small blocks, allowing residual samples to bypass the usual transform when that representation is cheaper. The encoder decides when to use this legal mode.

**Mastery check:** Explain energy compaction without saying that the transform itself compresses the bitstream.

## Chapter 14 — Quantization and QP

Quantization maps transform coefficients to coarser integer levels. A teaching approximation is:

```text
level = round(coefficient / step)
reconstructed coefficient ≈ level × step
```

HEVC implements this through defined integer scaling, QP-dependent factors, shifts, and optional scaling lists. The simplified division model explains the tradeoff but is not literal decoder pseudocode [[S1]](SOURCES.md#s1).

The Quantization Parameter (QP) controls the effective step size. An increase of six QP units approximately doubles the step. For 8-bit luma, the basic QP range is 0 to 51; higher bit depths introduce an offset.

Higher QP usually produces more zero coefficient levels and fewer bits, but greater distortion. Lower QP retains more coefficient information at greater rate.

The standard defines how signaled QP values are derived and used. It does not define one QP-selection policy. A practical encoder may use fixed QP, target-bitrate rate control, or an implementation-specific quality mode such as x265's Constant Rate Factor. Local adjustments and RDO operate within that broader policy.

### Four levels that should not be conflated

1. **Application target:** fixed QP, target bitrate, or an encoder-specific quality target.
2. **Rate control:** an encoder feedback or prediction system that allocates bits and chooses working QPs over time.
3. **Signaled QP information:** slice and local syntax from which the decoder derives the applicable QP.
4. **Quantization operation:** QP-dependent integer scaling that maps coefficients to levels and reconstructs their scale at the decoder.

The encoder may also use scaling lists to weight coefficient positions differently. Rate-Distortion Optimized Quantization can compare nearby level choices, including zero, using both distortion and estimated syntax cost. These are encoder decisions; the decoder applies the signaled outcome.

QP often influences the encoder's lambda value, but the relation is implementation-specific. Saying that “RDO selects QP” hides the higher-level rate-control policy and the distinction between a working frame QP and local adjustments.

**Mastery check:** Separate four ideas: requested quality or bitrate, rate control, signaled QP, and the QP-derived quantization step.

## Chapter 15 — CABAC entropy coding

Context-based Adaptive Binary Arithmetic Coding (CABAC) entropy-codes slice data. It is lossless with respect to the syntax values presented to it.

The conceptual stages are:

1. A syntax-element value is converted to bins using its specified binarization.
2. Context-coded bins use a selected probability state; bypass bins use the equiprobable path.
3. The binary arithmetic coder updates and renormalizes its interval, producing bits.

The decoder knows which bin belongs to which field because it follows the normative syntax and parsing process. The active syntax element determines its binarization, stopping rule, and context. There are no textual separators, and the decoder does not infer field boundaries by recognizing patterns in the compressed bits.

Residual coding includes specified syntax for the last significant coefficient location, significance information, coefficient levels, remainders, and signs. A unary “number of positions to check” field is not a valid substitute for that syntax [[S1]](SOURCES.md#s1).

### Residual syntax in the correct order

For a transform block with coded residual data, the decoder follows the `residual_coding` syntax and scan process. At a high level it derives:

1. the last significant coefficient coordinates;
2. coded-sub-block information where applicable;
3. significance flags for coefficient positions visited by the scan;
4. limited greater-than-one and greater-than-two level flags under the specified group rules;
5. sign flags, including the effect of sign-data hiding when enabled;
6. remaining absolute-level information using adaptive Rice coding with an Exp-Golomb escape path where required.

The greater-than-one and greater-than-two questions are not repeated without limit for every coefficient. Their presence and base-level interpretation depend on the scan pass and coefficient-group state. The remainder is not universally “magnitude minus three encoded with Exp-Golomb.” That shortcut is unsuitable as an HEVC syntax explanation [[S7]](SOURCES.md#s7).

The decoder already knows which syntax procedure it is executing because higher-level syntax and loop conditions determine the active field. CABAC supplies bins to that parser; it does not discover field boundaries from visible separators in the arithmetic-coded byte stream.

Arithmetic-coding interval examples can explain the principle, but arbitrary probabilities and a hand-picked final binary fraction are illustrations—not the literal output of HEVC CABAC. Real CABAC uses finite-state probability models, integer range operations, renormalization, and normative parsing rules.

Context-coded bins create sequential state dependencies within a substream; bypass bins follow a simpler path. Tiles, slices, and Wavefront Parallel Processing provide additional work boundaries or substreams rather than making one CABAC state universally parallel.

### Why CABAC helps and what it costs

Quantization creates many zeros and syntax elements with strongly non-uniform probabilities. Context selection lets common outcomes consume fewer average bits. The probability state adapts as bins are processed. This improves compression but creates serial state dependencies, variable workload, and specialized arithmetic-coder control. Hardware therefore uses techniques such as multi-bin bypass processing, context-memory organization, and substream-level parallelism [[S7]](SOURCES.md#s7).

Under the book's controlled experiments, sign-data hiding contributed roughly 0.6-0.9% average saving, while the redesigned HEVC coefficient-coding method contributed roughly 3.35-4.78% relative to the chapter's AVC-style comparison. Keep the test configuration attached to these values; they estimate tool contribution in a reference-software experiment, not the percentage of every final bitstream saved by CABAC [[S7]](SOURCES.md#s7).

**Further reading:** Use the transform-coefficient paper for actual residual syntax [[S55]](SOURCES.md#s55). Use the CABAC-throughput paper for the algorithm-hardware tradeoffs behind that design [[S56]](SOURCES.md#s56).

**Mastery check:** Trace one syntax element from the H.265 `residual_coding` process or HM decoder and label every bin using the standard's names.

## Chapter 16 — Deblocking and SAO

HEVC applies deblocking followed by Sample Adaptive Offset (SAO) before a reconstructed picture becomes available as a reference [[S3]](SOURCES.md#s3).

The deblocking filter targets selected discontinuities across block boundaries. Boundary strength and local sample conditions determine whether and how strongly an edge is filtered. It does not blur every coding boundary.

SAO applies signaled offsets to classified samples:

- **Band Offset:** classifies samples by value range.
- **Edge Offset:** classifies a sample by comparisons with two neighbors in a selected direction.

SAO is not a general smoothing filter. Depending on the selected class and offset, it can increase or decrease a reconstructed sample to reduce systematic bias.

Deblocking first determines whether a selected PU or TU boundary should be filtered and assigns a boundary strength. It then checks local gradients and applies a weak or strong filter when the conditions permit. The goal is to reduce discontinuities caused by independently coded blocks while preserving genuine image edges.

SAO works after deblocking. Band Offset selects a group of adjacent sample-value bands and signals offsets for them. Edge Offset compares a sample with two neighbors in one of four directions, places it into an edge category, and applies the category's signed offset. The encoder estimates whether SAO's distortion reduction justifies its signaling cost; the decoder applies the signaled parameters.

Because these filters are in-loop, filtered pictures can affect future inter prediction. Their encoder-side parameter selection is an optimization problem; their decoder-side application is normative.

**Further reading:** Study deblocking and SAO separately before comparing them [[S57]](SOURCES.md#s57) [[S58]](SOURCES.md#s58). The papers explain the different artifacts, decisions, and implementation constraints each filter addresses.

**Mastery check:** For each filter, identify its input, classification or decision, output, and effect on future reference pictures.

## Chapter 17 — Rate Distortion Optimization

Rate Distortion Optimization (RDO) is an encoder method for comparing candidates with a combined cost [[S44]](SOURCES.md#s44):

```text
J = D + lambda R
```

`D` measures reconstruction distortion, `R` represents estimated or exactly coded rate, and `lambda` sets the exchange rate between them. Exact metrics and lambda formulas are implementation choices.

The rate term must cover the syntax affected by the candidate. For a CU decision this can include split flags, prediction-mode bits, motion information, transform-tree flags, coefficient levels, and other dependent syntax. An exact CABAC simulation gives better rate information but requires context state and computation. Fast encoders often use cheaper estimates for early screening and reserve fuller coding for a small finalist set.

Distortion is measured between the source and the candidate reconstruction, not between the source and its prediction alone. Sum of Squared Differences, Sum of Absolute Differences, and transform-domain proxies can serve different search stages. Their use is encoder-specific.

The following numbers are an illustration, not an HEVC trace:

| Candidate | Distortion D | Rate R | Cost at lambda = 2 |
|---|---:|---:|---:|
| Large block | 18 | 6 | 30 |
| Moderate split | 8 | 10 | 28 |
| Deep split | 5 | 16 | 37 |

The moderate split wins even though it has neither the lowest distortion nor the lowest rate. The example demonstrates the purpose of the combined cost only; it does not prove how any actual block was coded.

Encoders may use RDO or approximations for partitioning, prediction, motion, transforms, coefficient decisions, and local quantization choices. They rarely exhaustively evaluate every legal combination. Fast screening, pruning, early termination, and rate estimation are central implementation topics.

The search is hierarchical rather than one flat table. A candidate prediction changes the residual; the residual changes transform and coefficient choices; those choices change both distortion and CABAC rate. Practical search order matters because an encoder cannot afford to fully encode every possible combination.

QP commonly influences lambda, but QP selection also involves rate control and application policy. RDO is not a complete rate-control algorithm.

**Mastery check:** Change lambda in the table and explain why the preferred candidate can change.

## Chapter 18 — One block through the encoder

For one typical transform-coded block, the conceptual path is:

1. A CTU is reached in coding order.
2. The encoder evaluates legal CU partitions.
3. For a CU, it evaluates intra or inter prediction and legal PU structures.
4. The selected prediction is subtracted from the source to form a residual.
5. The encoder evaluates legal transform partitions and transforms the residual.
6. Quantization converts transform coefficients to coefficient levels.
7. CABAC codes the selected partition, prediction, transform, coefficient, and filter syntax.
8. In parallel, local reconstruction applies inverse operations, adds the prediction, and runs in-loop filters.
9. The filtered picture may enter the Decoded Picture Buffer as a future reference.

This sequence describes relationships, not a mandatory encoder search order. Implementations can rearrange computations, cache results, or terminate searches early as long as the produced bitstream is conforming.

The quantized levels feed two connected but separate branches:

- the **bitstream branch** entropy-codes syntax and sends bytes;
- the **reconstruction branch** inverse-quantizes, inverse-transforms, adds prediction, filters the result, and stores a decoder-matching reference.

CABAC output is not the input to deblocking or SAO. Filters operate on reconstructed samples. This distinction prevents the common but incorrect story that entropy coding and filtering form one serial pixel-processing chain.

Do not attach fabricated coefficient matrices, CABAC intervals, or RDO values to a photograph and call them its “actual journey.” A real numerical trace requires instrumenting a named encoder build and recording the corresponding syntax and reconstruction data.

**Mastery check:** Explain the sequence in two minutes, clearly distinguishing signaled syntax from encoder-only calculations.

## Part III checkpoint

Starting with one source block, explain the complete path through prediction, residual formation, transform, quantization, CABAC, reconstruction, filtering, and reference storage. For CU, PU, TU, QP, and CABAC, state which behavior is normative, which decision belongs to the encoder, and what implementation cost the flexibility introduces.

---

# Part IV Research and implementation

## Chapter 19 — Encoder complexity

HEVC permits many combinations of partitions, prediction modes, references, motion vectors, transform structures, and coefficient decisions. Exhaustive search is generally impractical.

Research and production encoders reduce complexity through:

- hierarchical motion search and predictor-based starting points;
- inexpensive first-stage mode screening;
- early split or no-split decisions;
- reuse of neighboring or lower-resolution information;
- rate and distortion estimation before full coding;
- parallel work allocation;
- hardware acceleration;
- learned classifiers or regressors for candidate pruning.

A fast method must be evaluated against a well-defined anchor using both coding loss and resource savings. Reporting only encoding-time reduction or only BD-rate loss is incomplete. Energy, memory traffic, latency, throughput, model-training cost, and hardware area may also matter.

### Why hardware optimization is a system problem

The book's encoder architecture separates prediction, reconstruction, and bitstream work. Motion estimation can dominate reference-picture traffic; full RDO requires reconstruction and rate estimation for several candidates; CABAC has evolving state; filters require line and boundary storage. Accelerating only the transform may have little system impact if external-memory movement remains dominant.

The decoder has a different workload. It follows signaled choices rather than searching them, but must support variable block sizes, transforms up to 32 × 32, interpolation neighborhoods, in-loop-filter dependencies, and variable CABAC demand. Buffering entropy decoding separately from reconstruction can smooth workload, but it adds storage and delay [[S7]](SOURCES.md#s7).

### Evidence from the book's hardware examples

The decoder test chip reported 0.31 nJ/pixel at 4K30, but it implemented an early working draft, omitted SAO, and used CAVLC instead of final CABAC. The encoder test chip reported 8K30 at 708 mW, but its simplifications incurred about 22.6% BD-rate loss relative to HM 4.0 on the reported tests. These results demonstrate architectural feasibility and tradeoffs; neither is a final-standard baseline that can be compared without its limitations [[S7]](SOURCES.md#s7).

**Further reading:** Use the complexity-analysis paper to identify software bottlenecks [[S59]](SOURCES.md#s59), the parallelism paper to understand scaling limits [[S61]](SOURCES.md#s61), and the book's hardware chapters for architecture case studies [[S7]](SOURCES.md#s7).

**Mastery check:** Define a fair experiment for a fast CU-partition method.

## Chapter 20 — Connection to Professor Sen's research

Professor Shreyas Sen's relevant work emphasizes energy-constrained image sensing, transform and quantization hardware, and sensor-side computation [[S10]](SOURCES.md#s10) [[S15]](SOURCES.md#s15). The closest direct bridge is the transform and quantization datapath.

Important boundary: the cited work is not automatically an HEVC implementation. JPEG or MJPEG transform hardware and analog sensor compression solve related problems but have different block structures, temporal prediction requirements, syntax, and conformance constraints. A research proposal must state which ideas transfer and which must be redesigned.

Promising questions include:

- Can approximate arithmetic reduce encoder energy while keeping a conforming bitstream and bounded rate-distortion loss?
- Which HEVC encoder stages dominate energy after memory movement is included?
- Can early sensor-side processing reduce data movement without preventing later temporal prediction?
- How should rate-distortion objectives be extended to include energy or latency?

Collaborative project ideas may create intellectual property. Keep standards-based study material separate from confidential lab or company work, and follow the ownership guidance recorded in [the presentation discussion summary](presentation/PRESENTATION_DISCUSSION_SUMMARY.md).

**Mastery check:** Explain why “the lab has a DCT paper” is a starting connection, not evidence that the same circuit directly implements HEVC.

## Chapter 21 — Quality metrics and BD-rate

### PSNR

Mean Squared Error (MSE) and Peak Signal-to-Noise Ratio (PSNR) compare source and reconstructed samples:

```text
MSE = (1/N) Σ (x_i - y_i)^2
PSNR = 10 log10(MAX^2 / MSE)
```

PSNR is reproducible and inexpensive but does not perfectly predict perceived quality.

### SSIM and VMAF

Structural Similarity Index (SSIM) compares local luminance, contrast, and structure [[S47]](SOURCES.md#s47). Video Multi-Method Assessment Fusion (VMAF) combines features using a learned model [[S25]](SOURCES.md#s25) [[S48]](SOURCES.md#s48). Results depend on implementation, model version, scaling, and preprocessing.

### BD-rate

Bjøntegaard Delta rate summarizes the average bitrate difference between two fitted rate-distortion curves over an overlapping quality range [[S46]](SOURCES.md#s46). It is not the difference between two single encodes.

A defensible report states:

- anchor and test encoder versions;
- configurations and speed settings;
- sequences, resolution, and frame count;
- objective or subjective quality metric;
- rate points and overlapping integration range;
- per-sequence results and aggregation method.

### What the HEVC comparison actually reported

Under the book's Common Test Conditions comparison of HM 12.1 against JM 18.5, the reported overall luma BD-rate savings were 21.9% for All Intra, 42.7% for Random Access, 36.6% for Low Delay B, and 35.3% for Low Delay P [[S7]](SOURCES.md#s7). These values depend on the named reference encoders, configurations, sequences, rate points, and PSNR-based calculation. They support the claim that HEVC substantially improved coding efficiency, but they do not justify saying every HEVC encode is 50% smaller.

Objective metrics and subjective viewing answer different questions. A PSNR improvement may not map directly to perceived quality, while a subjective test depends on viewers, display conditions, content, and methodology. A research report should state both the metric and the experimental setup.

**Further reading:** Read the verification-test paper for the relationship between subjective and objective results [[S60]](SOURCES.md#s60). Use the original common-test document to understand why anchor versions, configurations, sequences, and QPs must be controlled [[S66]](SOURCES.md#s66).

**Mastery check:** Explain what a negative BD-rate means and why the quality metric must be named.

## Chapter 22 — Bitstream structure

HEVC separates coded video syntax from the transport or file format that carries it.

### NAL units

A Network Abstraction Layer (NAL) unit has a header and payload. Video Coding Layer NAL units carry coded slice-segment data; non-VCL units carry parameter sets and other information [[S1]](SOURCES.md#s1).

The HEVC NAL-unit header is two bytes. It identifies the NAL-unit type, layer identifier, and temporal sublayer. VCL types occupy the lower type range and carry coded-picture data. Non-VCL types include parameter sets, access-unit delimiters, Supplemental Enhancement Information, end markers, and filler-related structures. An application may use start codes or length fields to locate NAL units, depending on the surrounding byte-stream or container format.

### Parameter sets

- **VPS — Video Parameter Set:** high-level information relevant to coded video sequences and layers.
- **SPS — Sequence Parameter Set:** sequence configuration such as picture dimensions, coding-block limits, bit depth, and ordering constraints.
- **PPS — Picture Parameter Set:** coding controls referenced by pictures or slices.

This division avoids repeating stable configuration in every slice.

The **slice-segment header** carries information needed to interpret that slice segment, including references and local coding controls. Parameter-set identifiers connect the hierarchy: slice syntax refers to a PPS, the PPS refers to an SPS, and the SPS refers to a VPS where applicable. The exact dependency and field semantics come from the standard; the summary above explains why the hierarchy exists.

### Slices tiles and WPP

A picture is partitioned into slices or slice segments for syntax and entropy decoding. Tiles divide a picture into rectangular CTU regions with constrained dependencies. Wavefront Parallel Processing creates row-based entropy substreams with defined context initialization. These mechanisms overlap in implementation goals but are not interchangeable.

- A **slice** provides a decoding and resynchronization boundary with its own header and independently decodable slice data subject to reference-picture availability.
- A **dependent slice segment** can start at another CTU address but inherits selected header information and coding dependencies from an earlier segment.
- A **tile** divides the picture into rectangular CTU regions and restricts prediction and entropy dependencies across tile boundaries.
- **WPP** starts CTU-row substreams in a staggered schedule. A row begins after the row above is at least two CTUs ahead, and its CABAC contexts are initialized from the state after the second CTU of that upper row.

These tools trade compression efficiency, error containment, scheduling freedom, header cost, load balance, and latency. Parallelism comes from constrained dependencies and multiple substreams, not from removing every serial dependency inside CABAC.

### Profile tier and level

- **Profile:** which coding features and formats are supported.
- **Tier:** bitrate capability class within a level.
- **Level:** workload and buffer limits, including picture size, luma sample rate, bitrate, and Decoded Picture Buffer constraints.

### Random access and references

IDR and CRA are Intra Random Access Point types with different reference and leading-picture behavior. Reference Picture Sets describe which decoded pictures are used or retained. Temporal identifiers support temporal layering under specified dependency constraints.

An Instantaneous Decoding Refresh picture starts a new coded video sequence and prevents later pictures from depending on pictures before it. A Clean Random Access picture allows more efficient surrounding structures, including leading pictures whose decodability depends on where decoding begins. Broken Link Access supports splicing cases. Therefore, “intra picture” and “random-access point” are not interchangeable descriptions.

The Decoded Picture Buffer serves both reference retention and output reordering. Picture Order Count describes output order, which may differ from decoding order. Reference Picture Sets identify retained pictures, while Reference Picture Lists order the candidates available for inter prediction. List 0 and List 1 must not be defined merely as past and future.

### VUI, SEI, and HRD

- **Video Usability Information (VUI):** interpretation details such as aspect ratio, timing, and color description.
- **Supplemental Enhancement Information (SEI):** auxiliary messages that support systems or display behavior without generally changing the core decoded samples.
- **Hypothetical Reference Decoder (HRD):** a buffering and timing conformance model.

These mechanisms connect coded pictures to real delivery and display systems without turning the core sample reconstruction process into a container specification.

An HEVC elementary bitstream is not the same as an MP4, Matroska, MPEG-2 Transport Stream, or RTP packetization. Those systems carry HEVC NAL units and add their own timing, indexing, and transport structures.

**Further reading:** Use the high-level-syntax paper for NAL units, parameter sets, random access, and reference management [[S52]](SOURCES.md#s52). Use the system-integration paper for RTP, MPEG-2 TS, ISO Base Media File Format, and DASH [[S62]](SOURCES.md#s62). Inspect a real stream with FFmpeg's `ffprobe` and `trace_headers` tools [[S63]](SOURCES.md#s63).

**Mastery check:** Starting from an MP4 file, explain the conceptual layers down to a coded transform coefficient.

## Chapter 23 — Reading hardware papers critically

A hardware result is meaningful only in context. Record:

| Category | Questions |
|---|---|
| Function | Which codec stage or algorithm is implemented? Is the output standard-conforming? |
| Technology | Process node, voltage, frequency, memory assumptions, and precision? |
| Workload | Resolution, frame rate, bit depth, chroma format, and test content? |
| Quality | PSNR, SSIM, VMAF, BD-rate, or task accuracy? Relative to which anchor? |
| Cost | Power, energy per pixel, area, latency, throughput, and external-memory traffic? |
| Approximation | Where is error introduced, and how does it propagate through the closed loop? |

Power and energy are different. If energy is reported in picojoules per pixel:

```text
power = energy per pixel × pixels per second
```

Technology-node comparisons require normalization or careful caveats. A low-power design may simply process fewer pixels per second; a small core may exclude memory; and an image-only result may not include temporal prediction or bitstream generation.

### Minimum reproducibility record

For every hardware or fast-encoder paper, record:

```text
normative target and profile
anchor encoder and exact version
configuration and speed setting
test sequences, resolution, frame count, bit depth, and chroma format
rate and quality metrics
power boundary and memory boundary
throughput, latency, area, and technology node
conformance test
what approximation changes and where its error can propagate
```

The strongest research question is often not whether one block consumes little energy. It is whether the complete system saves energy after memory traffic, control, quality loss, and any extra work elsewhere are included.

**Mastery check:** Write a one-paragraph review that states the paper's contribution, strongest evidence, missing baseline, and most important threat to validity.

## Part IV checkpoint

Choose one proposed optimization and write an experiment plan that names the encoder version, configuration, sequences, quality and rate metrics, performance or energy boundary, conformance test, and expected tradeoff. If those details are missing, the proposal is not yet testable.

---

# Appendix A Glossary

- **AVC:** Advanced Video Coding, commonly H.264.
- **BD-rate:** Bjøntegaard Delta rate, a curve-based coding-efficiency summary.
- **CABAC:** Context-based Adaptive Binary Arithmetic Coding.
- **CB:** Coding Block.
- **CTB:** Coding Tree Block.
- **CRF:** Constant Rate Factor, an encoder-specific quality-control mode used by x264 and x265.
- **CTU:** Coding Tree Unit.
- **CU:** Coding Unit.
- **PU:** Prediction Unit.
- **TU:** Transform Unit.
- **DPB:** Decoded Picture Buffer.
- **GOP:** Group of Pictures, an encoder or application description of picture structure.
- **HEVC:** High Efficiency Video Coding, commonly H.265.
- **IDR:** Instantaneous Decoding Refresh.
- **MSE:** Mean Squared Error.
- **PSNR:** Peak Signal-to-Noise Ratio.
- **SSIM:** Structural Similarity Index.
- **VMAF:** Video Multi-Method Assessment Fusion.
- **MV:** Motion Vector.
- **MVD:** Motion Vector Difference.
- **AMVP:** Advanced Motion Vector Prediction.
- **BLA:** Broken Link Access.
- **CRA:** Clean Random Access.
- **DST:** Discrete Sine Transform.
- **HRD:** Hypothetical Reference Decoder.
- **IRAP:** Intra Random Access Point.
- **NAL:** Network Abstraction Layer.
- **PPS:** Picture Parameter Set.
- **SPS:** Sequence Parameter Set.
- **VPS:** Video Parameter Set.
- **QP:** Quantization Parameter.
- **RDO:** Rate Distortion Optimization.
- **RPS:** Reference Picture Set.
- **RQT:** Residual Quad Tree.
- **SAO:** Sample Adaptive Offset.
- **SEI:** Supplemental Enhancement Information.
- **VCL:** Video Coding Layer.
- **VUI:** Video Usability Information.
- **VVC:** Versatile Video Coding, commonly H.266.
- **WPP:** Wavefront Parallel Processing.

# Appendix B Reading map

| Need | Start with | Then use |
|---|---|---|
| Beginner overview | [[S27]](SOURCES.md#s27) | [[S3]](SOURCES.md#s3) |
| Normative syntax | [[S3]](SOURCES.md#s3) | [[S1]](SOURCES.md#s1) |
| Algorithms and hardware | [[S7]](SOURCES.md#s7) | HM and x265 [[S22]](SOURCES.md#s22) [[S21]](SOURCES.md#s21) |
| RDO | [[S44]](SOURCES.md#s44) | Encoder source and experiments |
| Quality comparison | [[S46]](SOURCES.md#s46) | [[S47]](SOURCES.md#s47) [[S25]](SOURCES.md#s25) |
| Lab connection | [[S10]](SOURCES.md#s10) | [[S15]](SOURCES.md#s15) and the paper's cited literature |

# Appendix C Formula reference

```text
raw bitrate = width × height × bits per pixel × frames per second

residual = original - prediction
reconstruction = prediction + reconstructed residual

J = D + lambda R

MSE = (1/N) Σ (x_i - y_i)^2
PSNR = 10 log10(MAX^2 / MSE)
```

For 8-bit YCbCr:

```text
4:4:4 = 24 bits per pixel
4:2:2 = 16 bits per pixel
4:2:0 = 12 bits per pixel
```

These formulas are starting points. State component format, bit depth, range, metric implementation, and encoder assumptions when reporting results.

# Appendix D Further-reading routes

Do not read every source in numerical order. Choose a route that matches the question being studied and produce an output from each reading.

| Goal | Start here | Deep reading | Practical output |
|---|---|---|---|
| Build signal-processing prerequisites | NPTEL Digital Video Signal Processing [[S64]](SOURCES.md#s64) | Richardson and the HEVC book [[S8]](SOURCES.md#s8) [[S7]](SOURCES.md#s7) | Derive raw rate, 4:2:0 sampling, one transform, and one motion-compensation example |
| Understand the complete standard | HEVC overview [[S3]](SOURCES.md#s3) | H.265 specification [[S1]](SOURCES.md#s1) | Draw the decoder pipeline and label every normative stage |
| Understand CU, PU, and TU | Block-partitioning paper [[S53]](SOURCES.md#s53) | HEVC book Chapter 3 [[S7]](SOURCES.md#s7) | Trace one CTU in HM and record legal versus tested candidates [[S22]](SOURCES.md#s22) |
| Study prediction | Intra-coding paper [[S54]](SOURCES.md#s54) and block-merging paper [[S65]](SOURCES.md#s65) | HEVC book Chapters 4 and 5 [[S7]](SOURCES.md#s7) | Compare intra, AMVP, merge, skip, and motion-compensated prediction on one block |
| Study transform and coefficients | HEVC book Chapter 6 [[S7]](SOURCES.md#s7) | Transform-coefficient paper [[S55]](SOURCES.md#s55) | Separate transform mathematics, quantization, scanning, and coefficient syntax |
| Study CABAC and hardware throughput | CABAC chapter in the HEVC book [[S7]](SOURCES.md#s7) | CABAC-throughput paper [[S56]](SOURCES.md#s56) | Trace one real syntax element and identify context-coded and bypass bins |
| Study in-loop filtering | Deblocking paper [[S57]](SOURCES.md#s57) | SAO paper [[S58]](SOURCES.md#s58) | Explain input, classification, output, signaling cost, and reference-picture effect for each filter |
| Study RDO and QP | RDO tutorial [[S44]](SOURCES.md#s44) | x265 analysis and rate-control documentation [[S21]](SOURCES.md#s21) | Compare fixed QP, CRF, ABR/VBV, lambda, and local mode decisions in one named encoder |
| Study performance methodology | HEVC comparison [[S4]](SOURCES.md#s4) | Verification tests and Common Test Conditions [[S60]](SOURCES.md#s60) [[S66]](SOURCES.md#s66) | Reproduce a small four-rate-point comparison and report BD-rate with its conditions |
| Study bitstream structure | High-level-syntax paper [[S52]](SOURCES.md#s52) | System-integration paper [[S62]](SOURCES.md#s62) | Use `ffprobe` and `trace_headers` to map container, packets, NAL units, and parameter sets [[S63]](SOURCES.md#s63) |
| Study implementation complexity | Complexity-analysis paper [[S59]](SOURCES.md#s59) | Parallelism paper and hardware chapters [[S61]](SOURCES.md#s61) [[S7]](SOURCES.md#s7) | Build a table of rate loss, runtime, memory traffic, power, energy, latency, and conformance |
| Connect to the lab's research | Approximate DCT and quantization paper [[S10]](SOURCES.md#s10) | SPARC publication list and HEVC hardware chapters [[S15]](SOURCES.md#s15) [[S7]](SOURCES.md#s7) | State what transfers to HEVC, what must change, and how total-system energy will be measured |
| Compare newer codecs | VVC and AV1 overviews [[S5]](SOURCES.md#s5) [[S6]](SOURCES.md#s6) | VTM, the AV1 specification, and SVT-AV1 documentation [[S67]](SOURCES.md#s67) [[S68]](SOURCES.md#s68) [[S69]](SOURCES.md#s69) | Compare tools using named versions, matched test material, speed settings, and quality metrics—never a universal percentage |
| Enter learned video compression | First make the hybrid loop and rate-distortion objective fluent | Deep Contextual Video Compression and CompressAI [[S70]](SOURCES.md#s70) [[S26]](SOURCES.md#s26) | Map learned motion/context, latent representation, entropy model, and reconstruction onto—or deliberately outside—the conventional hybrid loop |

## Reading record

For each paper or course module, record:

```text
Question the source answers:
Normative fact, encoder choice, experiment, or interpretation:
Mechanism:
Evidence and test conditions:
Implementation cost:
Limitation:
Connection to the next source:
One explanation I can now give without notes:
```
