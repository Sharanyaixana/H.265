# H.265/HEVC Research Primer

This primer supports the repository's notes and presentations. It is written for a reader who knows basic digital systems but is new to video coding.

Three labels are used throughout:

- **Normative HEVC:** behavior defined by the H.265 decoding specification.
- **Encoder choice:** a decision made by an implementation, such as x265 or HM.
- **Illustration:** a simplified example used to explain an idea; it is not a trace of the HEVC bitstream unless explicitly stated.

Use [[S1]](SOURCES.md#s1) for normative questions, [[S3]](SOURCES.md#s3) for a technical overview, and [[S7]](SOURCES.md#s7) for algorithms and architecture. Track unresolved details in [open-questions.md](open-questions.md).

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
original samples
      ↓
prediction → subtraction → residual → transform → quantization → entropy coding
      ↑                                                        ↓
filtered reconstructed reference                         bitstream
```

The bitstream contains more than coefficient levels. It may signal partitioning, prediction modes, reference indices, motion-vector differences, transform information, quantization changes, and filter parameters.

After quantization, a reconstruction path applies inverse scaling and transform, adds the same prediction, filters the result, and stores it for possible future reference. This local decoder is needed because the external decoder never has access to the pristine source picture.

The standard defines legal bitstreams and the decoding process. It does not prescribe one encoder search, mode-decision, or rate-control algorithm [[S1]](SOURCES.md#s1).

**Mastery check:** Draw both the bitstream path and reconstruction path and explain why they share the same quantized information.

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

**Mastery check:** State a research hypothesis with a measurable baseline, intervention, workload, quality metric, and energy or latency metric.

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

HEVC's CU structure uses recursive quadtree partitioning. Prediction partitions and transform partitions attach to a CU but answer different questions. A prediction boundary may follow object motion, while residual energy may require a different transform partition.

H.264/AVC used a 16 × 16 macroblock as its basic unit but also supported smaller prediction and transform partitions. HEVC's contribution is a larger and more flexible hierarchy, not the first use of variable block sizes.

How an encoder selects among legal partitions is not normative. Exhaustive comparison is possible in principle but practical encoders prune the search.

**Mastery check:** Explain why PU and TU are separate without relying only on their names.

## Chapter 13 — Transform coding

The transform converts a residual block into coefficients of spatial basis patterns. Correlated residuals often concentrate energy into fewer coefficients, which makes quantization and entropy coding more effective.

HEVC defines integer transforms related to the Discrete Cosine Transform for 4 × 4, 8 × 8, 16 × 16, and 32 × 32 transform blocks. It also defines an alternative transform derived from the Discrete Sine Transform for 4 × 4 intra-predicted luma residual blocks [[S3]](SOURCES.md#s3).

The transform does not mean “remove all high frequencies.” A smooth residual often concentrates energy at low frequencies; sharp detail can produce important high-frequency coefficients. Quantization decides which coefficient precision is retained.

Larger transforms can capture broad correlation but cost more computation and may represent localized structure poorly. Smaller transforms localize detail at the cost of more partition signaling and less broad energy compaction.

The normative decoder uses fixed-point integer operations, shifts, and clipping. Quantization is the principal deliberate loss; avoid describing every finite-precision transform as perfectly lossless or mathematically identical to a floating-point DCT.

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

**Mastery check:** Separate four ideas: requested quality or bitrate, rate control, signaled QP, and the QP-derived quantization step.

## Chapter 15 — CABAC entropy coding

Context-based Adaptive Binary Arithmetic Coding (CABAC) entropy-codes slice data. It is lossless with respect to the syntax values presented to it.

The conceptual stages are:

1. A syntax-element value is converted to bins using its specified binarization.
2. Context-coded bins use a selected probability state; bypass bins use the equiprobable path.
3. The binary arithmetic coder updates and renormalizes its interval, producing bits.

The decoder knows which bin belongs to which field because it follows the normative syntax and parsing process. The active syntax element determines its binarization, stopping rule, and context. There are no textual separators, and the decoder does not infer field boundaries by recognizing patterns in the compressed bits.

Residual coding includes specified syntax for the last significant coefficient location, significance information, coefficient levels, remainders, and signs. A unary “number of positions to check” field is not a valid substitute for that syntax [[S1]](SOURCES.md#s1).

Arithmetic-coding interval examples can explain the principle, but arbitrary probabilities and a hand-picked final binary fraction are illustrations—not the literal output of HEVC CABAC. Real CABAC uses finite-state probability models, integer range operations, renormalization, and normative parsing rules.

Context-coded bins create sequential state dependencies within a substream; bypass bins follow a simpler path. Tiles, slices, and Wavefront Parallel Processing provide additional work boundaries or substreams rather than making one CABAC state universally parallel.

**Mastery check:** Trace one syntax element from the H.265 `residual_coding` process or HM decoder and label every bin using the standard's names.

## Chapter 16 — Deblocking and SAO

HEVC applies deblocking followed by Sample Adaptive Offset (SAO) before a reconstructed picture becomes available as a reference [[S3]](SOURCES.md#s3).

The deblocking filter targets selected discontinuities across block boundaries. Boundary strength and local sample conditions determine whether and how strongly an edge is filtered. It does not blur every coding boundary.

SAO applies signaled offsets to classified samples:

- **Band Offset:** classifies samples by value range.
- **Edge Offset:** classifies a sample by comparisons with two neighbors in a selected direction.

SAO is not a general smoothing filter. Depending on the selected class and offset, it can increase or decrease a reconstructed sample to reduce systematic bias.

Because these filters are in-loop, filtered pictures can affect future inter prediction. Their encoder-side parameter selection is an optimization problem; their decoder-side application is normative.

**Mastery check:** For each filter, identify its input, classification or decision, output, and effect on future reference pictures.

## Chapter 17 — Rate Distortion Optimization

Rate Distortion Optimization (RDO) is an encoder method for comparing candidates with a combined cost [[S44]](SOURCES.md#s44):

```text
J = D + lambda R
```

`D` measures reconstruction distortion, `R` represents estimated or exactly coded rate, and `lambda` sets the exchange rate between them. Exact metrics and lambda formulas are implementation choices.

The following numbers are an illustration, not an HEVC trace:

| Candidate | Distortion D | Rate R | Cost at lambda = 2 |
|---|---:|---:|---:|
| Large block | 18 | 6 | 30 |
| Moderate split | 8 | 10 | 28 |
| Deep split | 5 | 16 | 37 |

The moderate split wins even though it has neither the lowest distortion nor the lowest rate. The example demonstrates the purpose of the combined cost only; it does not prove how any actual block was coded.

Encoders may use RDO or approximations for partitioning, prediction, motion, transforms, coefficient decisions, and local quantization choices. They rarely exhaustively evaluate every legal combination. Fast screening, pruning, early termination, and rate estimation are central implementation topics.

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

Do not attach fabricated coefficient matrices, CABAC intervals, or RDO values to a photograph and call them its “actual journey.” A real numerical trace requires instrumenting a named encoder build and recording the corresponding syntax and reconstruction data.

**Mastery check:** Explain the sequence in two minutes, clearly distinguishing signaled syntax from encoder-only calculations.

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

**Mastery check:** Explain what a negative BD-rate means and why the quality metric must be named.

## Chapter 22 — Bitstream structure

HEVC separates coded video syntax from the transport or file format that carries it.

### NAL units

A Network Abstraction Layer (NAL) unit has a header and payload. Video Coding Layer NAL units carry coded slice-segment data; non-VCL units carry parameter sets and other information [[S1]](SOURCES.md#s1).

### Parameter sets

- **VPS — Video Parameter Set:** high-level information relevant to coded video sequences and layers.
- **SPS — Sequence Parameter Set:** sequence configuration such as picture dimensions, coding-block limits, bit depth, and ordering constraints.
- **PPS — Picture Parameter Set:** coding controls referenced by pictures or slices.

This division avoids repeating stable configuration in every slice.

### Slices tiles and WPP

A picture is partitioned into slices or slice segments for syntax and entropy decoding. Tiles divide a picture into rectangular CTU regions with constrained dependencies. Wavefront Parallel Processing creates row-based entropy substreams with defined context initialization. These mechanisms overlap in implementation goals but are not interchangeable.

### Profile tier and level

- **Profile:** which coding features and formats are supported.
- **Tier:** bitrate capability class within a level.
- **Level:** workload and buffer limits, including picture size, luma sample rate, bitrate, and Decoded Picture Buffer constraints.

### Random access and references

IDR and CRA are Intra Random Access Point types with different reference and leading-picture behavior. Reference Picture Sets describe which decoded pictures are used or retained. Temporal identifiers support temporal layering under specified dependency constraints.

An HEVC elementary bitstream is not the same as an MP4, Matroska, MPEG-2 Transport Stream, or RTP packetization. Those systems carry HEVC NAL units and add their own timing, indexing, and transport structures.

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

**Mastery check:** Write a one-paragraph review that states the paper's contribution, strongest evidence, missing baseline, and most important threat to validity.

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
- **CRA:** Clean Random Access.
- **MSE:** Mean Squared Error.
- **PSNR:** Peak Signal-to-Noise Ratio.
- **SSIM:** Structural Similarity Index.
- **VMAF:** Video Multi-Method Assessment Fusion.
- **MV:** Motion Vector.
- **MVD:** Motion Vector Difference.
- **AMVP:** Advanced Motion Vector Prediction.
- **NAL:** Network Abstraction Layer.
- **PPS:** Picture Parameter Set.
- **SPS:** Sequence Parameter Set.
- **VPS:** Video Parameter Set.
- **QP:** Quantization Parameter.
- **RDO:** Rate Distortion Optimization.
- **SAO:** Sample Adaptive Offset.
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
