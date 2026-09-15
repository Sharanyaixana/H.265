# HEVC Book Memory

## Purpose

This file is a durable technical memory of *High Efficiency Video Coding (HEVC): Algorithms and Architectures*, edited by Vivienne Sze, Madhukar Budagavi, and Gary J. Sullivan, Springer, 2014 [[S7]](../SOURCES.md#s7).

It is a synthesis, not a replacement for the book or the H.265 specification. Use it to:

- recover the book's overall argument quickly;
- locate the chapter, section, figure, or table that deserves a full reread;
- distinguish normative HEVC behavior from encoder and hardware design choices;
- avoid repeating incorrect simplified explanations in notes or presentations;
- identify research questions at the algorithm-hardware boundary.

Page references below use the printed book page numbers, not the PDF viewer's page counter. The PDF has 384 pages; printed technical content runs from page 1 through page 373.

## Evidence labels used here

- **Normative:** behavior required by the HEVC bitstream or decoding process.
- **Encoder choice:** a non-normative decision made by an encoder implementation.
- **Book experiment:** a measured result under the software, sequence, and configuration stated in the book.
- **Architecture example:** a particular hardware design, not a requirement of HEVC.

## The book's central argument

HEVC gains compression efficiency by giving the encoder a more flexible representation: larger and recursively partitioned blocks, more prediction choices, variable transforms, improved coefficient coding, and in-loop filtering. Flexibility creates a second problem: the encoder has many more candidates to evaluate, and the decoder has more variable-sized operations and dependencies to implement. The standard was therefore designed with coding efficiency and implementation cost considered together.

The recurring engineering tradeoff is:

```text
better prediction or representation
        ↕
more search, state, memory traffic, control, latency, or hardware
```

The book repeatedly shows that a tool should not be judged only by bitrate. Throughput, serial dependencies, memory size, off-chip bandwidth, power, and implementation regularity influenced the final HEVC design.

## Book map

| Chapter | Printed pages | Main purpose | Revisit when |
|---|---:|---|---|
| 1. Introduction | 1-12 | Motivation, history, standardization, scope, profiles and licensing | Explaining what HEVC is and is not |
| 2. High-Level Syntax | 13-48 | NAL units, access and reference structure, parameter sets, buffering, VUI, SEI and HRD | Preparing the bitstream presentation |
| 3. Block Structures and Parallelism | 49-90 | CTU/CU/PU/TU, RQT, slices, tiles, WPP and low delay | Explaining partitioning and parallelism |
| 4. Intra-Picture Prediction | 91-112 | Reference preparation, 35 modes, mode signaling and efficiency | Explaining spatial prediction |
| 5. Inter-Picture Prediction | 113-140 | AMVP, merge, skip, interpolation and weighted prediction | Explaining motion information |
| 6. Transform and Quantization | 141-170 | Integer transform design, scaling, DST, quantization, QP and special modes | Explaining residual representation |
| 7. In-Loop Filters | 171-208 | Deblocking, SAO, implementation and measured effect | Explaining the reconstruction loop |
| 8. Entropy Coding | 209-274 | CABAC, exact residual syntax, throughput and memory design | Correcting or expanding CABAC slides |
| 9. Compression Performance | 275-302 | Test conditions, RDO, objective and subjective comparisons | Making efficiency claims responsibly |
| 10. Decoder Hardware | 303-342 | Pipeline, memory, transform, prediction, filters and test chip | Studying decoder architecture |
| 11. Encoder Hardware | 343-373 | Motion search, RDO hardware, memory hierarchy, CABAC and 8K test chip | Developing a hardware research direction |

---

# 1. What HEVC standardizes

## A common format, not one encoder

The strictest description of HEVC is a common compressed representation and a decoding process. It is convenient to say “HEVC codec,” but the standard does not prescribe one complete encoder and one physical decoder.

**Normative:** syntax, parsing, reconstruction rules, constraints, and conformance behavior.

**Encoder choice:** search strategy, motion-estimation algorithm, candidate pruning, distortion metric, rate-control policy, speed-quality tradeoff, error-resilience strategy, and most resource allocation.

This explains why two conforming encoders can produce different bitrates, quality, latency, and energy for the same input. Encoder design is deliberately open.

## Why it was developed

HEVC was created jointly by ITU-T VCEG and ISO/IEC MPEG through JCT-VC. The project began formally with the 2010 Call for Proposals; version 1 was completed in January 2013 and published later that year.

The motivation was not file size in isolation. Video systems were moving toward:

- Ultra High Definition resolution;
- higher frame rates;
- higher dynamic range and wider color gamut;
- greater sample precision;
- ubiquitous capture, streaming, communication, and surveillance.

The intended coding-efficiency step was approximately a halving of bitrate relative to H.264/AVC at comparable quality under controlled conditions. The book is explicit that the syntax only provides the capability; an encoder is not guaranteed to realize it.

## Interoperability and extensibility

The value of a standard is that independently designed products can communicate. The standard leaves preprocessing, postprocessing, display, damaged-bitstream recovery, and many application behaviors outside its core scope. Profiles, tiers, and levels bound interoperability:

- **Profile:** supported coding tools and formats.
- **Tier:** bitrate capability class.
- **Level:** limits on picture size, sample rate, buffers, bitrate, and related workload.

High-level syntax also provides extension and supplemental-information mechanisms.

## Complexity lesson

The book estimated HEVC software decoding at roughly 1.5-2 times AVC decoding complexity in the early implementation period. Encoding is much harder because the encoder must choose among many legal alternatives. Treat this as a 2014-era observation, not a current universal benchmark.

---

# 2. High-level syntax and bitstream memory

## Coded-video layers

The conceptual stack is:

```text
container or transport system
    └── HEVC elementary bitstream
        └── Network Abstraction Layer units
            ├── Video Coding Layer NAL units: coded slice-segment data
            └── non-VCL NAL units: parameter sets and supplemental data
```

An MP4 file, RTP stream, or MPEG-2 Transport Stream is not itself the HEVC elementary bitstream. It carries HEVC data and adds timing, indexing, multiplexing, or transport behavior.

## NAL-unit header

The HEVC NAL-unit header is two bytes. Its fields identify the NAL-unit type, layer, and temporal sublayer. VCL NAL-unit types occupy values 0-31; non-VCL types occupy 32-63.

The temporal identifier enables temporal sublayers. A conforming temporal hierarchy prevents a lower temporal sublayer from depending on a higher one, allowing higher layers to be discarded for frame-rate adaptation.

## Access and picture types

The book distinguishes:

- **IRAP:** Intra Random Access Point pictures, coded without inter-picture references.
- **IDR:** Instantaneous Decoding Refresh; starts a new coded video sequence and prevents later dependency on pictures preceding it.
- **CRA:** Clean Random Access; supports more efficient structures in which some leading pictures may depend on earlier decoded pictures.
- **BLA:** Broken Link Access; supports splicing cases where earlier dependencies are unavailable.
- **RADL/RASL:** leading pictures that are respectively decodable or potentially skipped when random access begins at the associated IRAP.
- **TRAIL, TSA, STSA:** trailing pictures and temporal-sublayer switching points.

An intra-coded picture is not automatically an independently usable seek point. Random-access semantics depend on its NAL-unit type and the surrounding reference structure.

## Decoding order and output order

Picture Order Count determines output order within a coded video sequence. Output order may differ from bitstream decoding order because a picture displayed earlier can depend on a reference decoded earlier but displayed later.

Terms such as “past” and “future” are therefore unsafe substitutes for Reference Picture List 0 and List 1. The lists are constructed from decoded reference pictures according to signaled rules.

## Parameter sets

- **VPS - Video Parameter Set:** sequence/layer-level organization and operating-point information.
- **SPS - Sequence Parameter Set:** dimensions, bit depth, block-size constraints, picture-order and buffering parameters, and sequence-level tool controls.
- **PPS - Picture Parameter Set:** controls that may vary among pictures, including tiles, entropy synchronization, initial QP-related information, deblocking controls, and other coding options.
- **Slice-segment header:** information applying to one slice segment, including references and local coding controls.

Parameter sets prevent stable configuration from being repeated in every slice.

## Decoded Picture Buffer and Reference Picture Sets

The Decoded Picture Buffer holds pictures needed for reference or output. Reference Picture Sets identify which pictures are retained and used. Reference Picture Lists provide ordered references for inter prediction.

Buffer management is part of conformance, not an informal “keep the last frame” rule. Pictures can be retained for reference, held for output reordering, or removed when no longer needed.

## VUI, SEI, and HRD

- **VUI - Video Usability Information:** interpretation information such as aspect ratio, timing, color description, and restrictions.
- **SEI - Supplemental Enhancement Information:** auxiliary messages that aid systems or display but generally do not change the core decoded samples.
- **HRD - Hypothetical Reference Decoder:** a model for coded-picture buffering, timing, and delivery conformance.

The chapter also introduces decoding units for sub-picture timing, supporting low-delay delivery of portions of a picture.

---

# 3. Block structure: the hierarchy and its purpose

## The four levels

| Structure | Meaning | Question answered |
|---|---|---|
| CTU | Coding Tree Unit | What top-level picture region and associated syntax are being processed? |
| CU | Coding Unit | How is the CTU recursively partitioned into coding-decision regions? |
| PU | Prediction Unit | What geometry and parameters form the prediction? |
| TU | Transform Unit | How is the residual partitioned for transform and coefficient coding? |

A CTU is not merely one luma square. It groups a luma Coding Tree Block, corresponding chroma Coding Tree Blocks when present, and associated syntax.

## Coding-tree partition

HEVC permits luma CTB sizes of 16, 32, or 64 samples, constrained by sequence parameters. The common maximum is 64 x 64, compared with AVC's 16 x 16 macroblock. A quadtree recursively divides a square Coding Block into four smaller square Coding Blocks. The minimum luma CB can be 8 x 8 in the common configuration.

The larger root serves smooth or coherently moving regions; deeper splits adapt to edges, motion boundaries, and texture. The standard defines the legal tree and syntax. It does not define the encoder's split-search algorithm.

## Prediction partition

PU partitions give prediction geometry without requiring the CU quadtree to represent every rectangle. Inter CUs can use square, symmetric rectangular, and asymmetric motion partitions, subject to size and mode constraints. This lets prediction follow object or motion boundaries while retaining a regular square CU hierarchy.

Intra and inter prediction do not share exactly the same partition menu. Check the applicable CU size, prediction mode, and standard edition before drawing a universal PU diagram.

## Transform partition and RQT

The residual quadtree, or RQT, partitions a CU's residual into Transform Units. Transform sizes range from 4 x 4 through 32 x 32. SPS parameters constrain minimum size, maximum size, and transform-tree depth; some split or leaf decisions are implied rather than explicitly signaled.

PU and TU are separate because prediction geometry and residual statistics solve different problems. A large PU can predict one moving surface while its residual contains a localized edge requiring smaller TUs. Conversely, multiple prediction regions can leave residuals that are efficiently handled by larger legal transforms where constraints permit.

## Why the hierarchy matters quantitatively

The Chapter 3 conclusion attributes more than half of HEVC's HD coding-efficiency improvement over AVC, in the experiments discussed there, to flexible block partitioning for prediction and transform coding. This is a book result, not a universal percentage for every encoder.

## Slices and slice segments

An independent slice segment establishes an entropy-decoding start and breaks selected dependencies. A dependent slice segment has a reduced header and inherits coding dependencies from preceding data. This supports packetization and sub-picture delay, but a dependent segment is not independently decodable.

More slice boundaries generally increase robustness or accessibility while losing coding efficiency through headers and broken prediction/context continuity.

## Tiles

Tiles divide a picture into rectangular CTU groups. They change CTU scan order to tile raster scan and constrain parsing and prediction dependencies across tile boundaries. In-loop filtering may cross tile boundaries when enabled.

Tiles are useful for:

- rectangular parallel work allocation;
- distributing regions across hardware units;
- region-of-interest organization;
- packet-loss isolation.

Costs include boundary overhead, load imbalance, and coding loss from broken dependencies and context reinitialization.

## Wavefront Parallel Processing

Wavefront Parallel Processing (WPP) assigns separate CABAC substreams to CTU rows. A lower row begins with a two-CTU lag behind the row above. Context state from the second CTU of the preceding row initializes the new row, retaining much of the learned probability information.

WPP preserves more spatial dependency than tiles and usually incurs lower coding loss. Its parallelism ramps up and down, and one slow CTU can stall following rows. It also requires context-state storage and synchronization.

The version described by the book does not allow tiles and WPP simultaneously in the same coded video sequence.

---

# 4. Intra prediction memory

## Purpose

Intra prediction removes spatial correlation by constructing a block from already reconstructed samples in the same picture. Reconstructed, rather than original, neighbors are essential because the decoder has only reconstructed data.

## Reference preparation

Reference samples are normally taken from the top and left neighborhood. HEVC defines availability checks and substitution so all prediction modes remain usable even when some neighbors are unavailable. If none are available, a mid-range value is substituted; if some exist, available samples propagate according to the specified process.

Reference samples can be filtered depending on component, block size, and mode. This improves prediction for some smooth or directional structures but is not applied indiscriminately.

## The 35 luma modes

- 1 planar mode for a smooth two-dimensional surface.
- 1 DC mode for a nearly constant prediction.
- 33 angular modes for directional structures.

Angular modes project a sample location onto a one-dimensional reference array. Fractional positions use linear interpolation at 1/32-sample directional precision. Directions are denser near horizontal and vertical because those structures are common.

Planar prediction blends horizontal and vertical linear estimates to avoid discontinuities in smooth regions. DC predicts an average. Selected modes receive boundary post-processing to improve continuity.

## Mode signaling

HEVC derives three Most Probable Modes from neighboring prediction modes. If the selected mode belongs to that list, it can be signaled economically. Otherwise, one of the remaining modes is indicated. Chroma can derive its mode from luma or signal another permitted choice.

## Encoder decision

The standard defines how each mode is reconstructed, not how the encoder searches all modes. Practical encoders use inexpensive screening followed by more accurate rate-distortion comparisons. Lowest prediction error alone does not necessarily win because mode bits and resulting coefficient bits also matter.

## Book measurements

Chapter 4 reports still-picture experiments, including approximately 23% average bitrate reduction over AVC intra in one cited study. It also reports larger gains over JPEG and JPEG 2000 under specific photographic datasets and quality ranges. These results concern particular reference implementations and should not be generalized without their test conditions.

---

# 5. Inter prediction memory

## Motion estimation versus compensation

- **Motion estimation - encoder choice:** search reference pictures and candidate displacements.
- **Motion compensation - normative reconstruction:** generate a prediction from decoded reference samples, reference index, motion vector, and interpolation rules.

The decoder never repeats the encoder's search.

## Advanced Motion Vector Prediction

Advanced Motion Vector Prediction (AMVP) forms a small candidate list from spatial and temporal motion information. The encoder selects a predictor and codes the Motion Vector Difference. This exploits correlation in neighboring motion fields while allowing a new vector.

Candidate construction includes availability, redundancy removal, temporal scaling, and fallback behavior. The syntax does not carry the entire motion search.

## Merge and skip

Merge mode derives reference indices, motion vectors, and prediction direction from a selected spatial or temporal candidate. It is especially effective when neighboring blocks share motion.

Skip is a merge case with no coded residual for the CU. It does not mean “copy the previous frame” universally; it means derived motion information plus the absence of residual syntax under the skip conditions.

**Book experiment:** enabling merge and skip in HM 8.0 produced average BD-rate savings of roughly 6-8% across the reported Random Access and Low Delay conditions, with larger gains on some static-background conferencing sequences.

## Parallel merge-estimation regions

Neighbor-derived merge candidates introduce encoder dependencies. A signaled merge-estimation-region size can exclude candidates inside the same region, allowing parallel candidate derivation. Larger regions expose more parallelism but remove more useful candidates.

**Book experiment:** HM 5.0 showed increasing BD-rate loss as the region grew; reported losses ranged from roughly 0.1-0.2% at 8 x 8 to roughly 2.7-3.4% at 64 x 64 for the tested configurations.

## Fractional-sample interpolation

Motion vectors can point between stored samples. HEVC uses separable finite impulse response interpolation:

- luma uses 8-tap filtering for half-sample positions and 7-tap filtering for quarter-sample positions;
- chroma uses 4-tap filtering at eighth-sample positions in common 4:2:0 operation.

Higher internal precision is retained between filtering and bi-prediction to avoid premature rounding.

The longer filters improve prediction but increase arithmetic, reference fetches, intermediate precision, and memory bandwidth. The book estimates roughly 20% more multiply-accumulate work than AVC interpolation for 8-bit video and notes worst-case bandwidth pressure for small blocks.

**Book experiment:** substituting AVC-style interpolation into HM 6.0 suggested average luma BD-rate gains around 4.0%, 4.9%, and 2.6% for the reported RA, Low Delay B, and Low Delay P conditions. Chroma gains were larger in that experiment. These are isolated-tool comparisons, not total HEVC gains.

## Weighted prediction

Weighted prediction applies a scale and offset to motion-compensated samples. It is useful for fades and illumination changes. The PPS controls whether weighted uni- or bi-prediction is enabled, while slice data carries relevant weights and offsets.

---

# 6. Transform and quantization memory

## Why transform the residual

Prediction removes much correlation but usually leaves a residual. A spatial transform represents that residual as weighted basis patterns. Correlated residual energy often becomes concentrated in fewer coefficients. The transform does not delete information merely by changing domains; quantization creates the principal deliberate loss.

## HEVC transforms

The book describes square transforms of 4 x 4, 8 x 8, 16 x 16, and 32 x 32. The two-dimensional transform is separable into row and column one-dimensional operations.

HEVC uses finite-precision integer matrices related to the Discrete Cosine Transform (DCT). For 4 x 4 intra-predicted luma residuals, an alternate transform derived from the Discrete Sine Transform (DST) is used.

## Why the matrix is engineered rather than exact

The real DCT has useful properties:

- orthogonal basis vectors;
- good energy compaction;
- equal basis-vector norms;
- symmetry and nested relationships useful for fast implementation.

HEVC's integer transform was selected as a compromise among closeness to the DCT, near orthogonality, nearly equal norms, symmetry, shared structure, and bounded arithmetic cost. Design properties include:

- 8-bit signed matrix elements;
- smaller transforms embedded within the 32-point matrix;
- 16-bit transpose-buffer intermediates;
- multipliers no wider than 16 bits and accumulators below 32 bits under the analyzed bounds.

Only 31 unique magnitudes occur in the full transform matrices. Symmetry, odd-even decomposition, and embedded matrices enable hardware reuse.

Nearly equal norms remove the need for the non-flat default dequantization correction matrices used to compensate AVC transform-basis norm differences. Optional scaling lists still permit frequency-dependent quantization.

## Transform-size tradeoff

Large transforms capture broad smooth residual correlation and reduce partition overhead. Small transforms localize sharp changes and can avoid spreading energy across a large region. The correct size depends on the residual, not directly on the object label or PU size.

**Book experiment:** in HM 9.0.1, allowing 16 x 16 and 32 x 32 transforms in addition to 4 x 4 and 8 x 8 produced average BD-rate savings of 5.6% for All Intra, 6.4% for Random Access, and 6.8% for Low Delay B. Gains were larger for high-resolution classes.

## Quantization Parameter

For 8-bit video, QP has 52 values from 0 through 51. The effective step grows approximately as:

```text
QP + 1  -> step x 2^(1/6), approximately +12%
QP + 6  -> step x 2
```

Higher QP usually creates more zero levels and lower rate at greater distortion. Lower QP preserves finer coefficient information at greater rate.

The standard defines how QP-related syntax is derived and applied. It does not define the encoder's sequence-level rate-control policy. Delta QP can support local adaptation; the minimum quantization-group size depends on signaled parameters and can be as small as 8 x 8 in the configuration discussed by the book.

## Scaling lists

Scaling lists modify quantization by coefficient position, allowing frequency-dependent weighting. Default flat scaling is possible because the HEVC transform basis norms are nearly uniform. Custom lists must be signaled or inferred through parameter-set syntax.

## Special modes

- **I_PCM:** conveys sample values through a special path rather than ordinary prediction-transform coefficient coding.
- **Transquant bypass / lossless mode:** bypasses transform and quantization under the applicable syntax, supporting mathematically lossless reconstruction when other conditions are met.
- **Transform skip:** for permitted 4 x 4 TUs, quantizes and codes spatial residual samples as coefficient-like values without applying the transform; useful for sharp screen content.

Do not merge these into one generic “lossless mode.” They bypass different operations and have different syntax and use cases.

---

# 7. In-loop filtering memory

## Reconstruction branch

Deblocking and Sample Adaptive Offset operate on reconstructed samples, not on CABAC bits. After quantized coefficients are inverse-scaled and inverse-transformed, the reconstructed residual is added to the prediction. The resulting samples pass through deblocking and then SAO before they can be stored as references.

## Deblocking filter

Block prediction, transform partitioning, and quantization can create artificial discontinuities. HEVC evaluates selected PU and TU boundaries on an 8-sample grid. Boundary strength uses coding mode, nonzero coefficients, motion information, and neighboring conditions. Sample tests distinguish a likely coding artifact from a genuine edge.

Depending on conditions, the filter is off, normal, or strong. QP-dependent thresholds control decisions and clipping. Vertical boundaries are processed before horizontal boundaries.

Deblocking is not a blanket blur. Filtering only eligible boundaries and constraining changes protects real edges.

## Sample Adaptive Offset

SAO addresses systematic reconstruction bias and ringing after deblocking. The encoder estimates offsets; the decoder applies the signaled classification and offsets.

### Edge Offset

A reconstructed sample is compared with two neighbors along one of four directions. The comparisons classify it as a local minimum, local maximum, one of two edge-transition categories, or flat. Four non-flat classes can receive offsets; the flat class has no offset.

### Band Offset

The sample range is divided into 32 bands. For 8-bit samples, each band covers eight values. The encoder selects four consecutive bands and signals offsets for them.

### Signaling and merging

SAO parameters are associated with CTUs and components. Parameters can merge from left or above neighbors to reduce signaling cost. Luma and chroma decisions can differ.

SAO is not inherently smoothing: an offset can raise or lower a sample, restoring contrast or reducing bias.

## Implementation lesson

Both filters require neighboring data, line storage, boundary scheduling, and interaction with tiles/slices. SAO classification can be implemented with comparisons and additions, but encoder-side parameter estimation also needs source-versus-reconstruction statistics.

**Book experiments:** SAO added less than roughly 2-3% average decoding time in the reported tests. Deblocking plus SAO produced 2.6-15% coding-efficiency improvement depending on configuration in the Chapter 7 comparison, with about 10% decoding-time increase. These figures are HM-era isolated tests.

---

# 8. CABAC memory: correct syntax story

## CABAC's job

Context-based Adaptive Binary Arithmetic Coding (CABAC) losslessly maps syntax values to compressed bits. Prediction, transform, and quantization have already made the coding decisions. CABAC does not choose a CU, motion vector, transform, or QP.

The three stages are:

1. **Binarization:** map a syntax value to a specified bin string.
2. **Context modeling:** choose a probability state for context-coded bins.
3. **Binary arithmetic coding:** update a finite-precision interval and emit bits through renormalization.

HEVC also has bypass-coded bins with an equiprobable path and termination bins at defined boundaries.

## How the decoder knows field boundaries

The parser follows normative syntax in a known order. At each point it knows which syntax element is active. That syntax element defines:

- whether a value or flag is present;
- its binarization and stopping rule;
- context selection or bypass mode;
- any inference when syntax is absent.

There are no textual separators in the arithmetic bitstream. A decoder does not inspect a run of ones and guess whether it represents significance or magnitude.

## Exact transform-block progression

For a nonzero Transform Block, the Chapter 8 flow is:

```text
transform-tree and coded-block information
    -> last significant coefficient x/y prefix
    -> optional x/y suffix
    -> select coefficient scan
    -> coded_sub_block_flag for eligible 4 x 4 groups
    -> sig_coeff_flag within nonzero groups
    -> coeff_abs_level_greater1_flag pass
    -> coeff_abs_level_greater2_flag pass
    -> coefficient sign pass
    -> coeff_abs_level_remaining pass where required
```

This replaces the earlier incorrect model that inserted a unary “number of positions to check.” HEVC has no such residual field.

## Last-significant position

HEVC sends the x and y coordinates of the last significant coefficient before the significance map. Prefix bins are truncated-unary and context-coded; suffix bins, when present, are fixed-length and bypass-coded. The significance flag at the last scan position is inferred to be one.

This avoids AVC's tight dependency from interleaving a significance flag with a last flag after every nonzero event.

## Scans and coefficient groups

Transform Blocks larger than 4 x 4 are processed as 4 x 4 coefficient groups. Diagonal scan is the default. For selected 4 x 4 and 8 x 8 intra cases, the prediction mode can select horizontal or vertical scanning.

`coded_sub_block_flag` states whether an eligible 4 x 4 group contains a nonzero coefficient. Groups containing the DC position or last-significant position can have the flag inferred. If a group is all zero, its individual significance flags are omitted.

Within eligible groups, `sig_coeff_flag` indicates significant coefficient positions, with some values inferred. Contexts depend on component, transform size, scan, coefficient-group neighborhood, region, and position according to the standard's rules.

## Coefficient magnitudes: the important correction

The greater-than-one and greater-than-two flags are not two universal questions asked for every nonzero coefficient.

For each 4 x 4 coefficient group:

- at most eight `coeff_abs_level_greater1_flag` values are transmitted;
- at most one `coeff_abs_level_greater2_flag` is transmitted;
- remaining magnitude information uses `coeff_abs_level_remaining` where required;
- greater1 and greater2 are context-coded;
- remainder and sign information are bypass-coded.

The absolute-level representation adapts by coefficient group. It combines truncated-unary decisions with adaptive Rice coding and an Exp-Golomb escape region. The Rice parameter changes according to previously decoded magnitudes. Therefore, “magnitude minus three is always Exp-Golomb coded” is not a correct general HEVC description.

## Sign coding and sign-data hiding

Sign bins are bypass-coded and grouped for throughput. Optional Sign Bit Hiding omits one sign in an eligible coefficient group. The encoder adjusts a quantized level so the parity of the sum of absolute levels represents the hidden sign. This is a quantization decision with a rate-distortion cost, not free lossless packing.

**Book experiment:** sign-data hiding contributed about 0.6-0.9% bitrate savings under the reported common conditions.

## Why HEVC CABAC is hardware-aware

CABAC is serial because interval state and many context states depend on preceding bins. HEVC reduces the bottleneck by:

- reducing the number of regular context-coded bins;
- grouping bypass bins;
- grouping bins that use similar context logic;
- reducing immediate context-selection dependencies;
- reducing total worst-case bins;
- reducing context and line-buffer memory;
- processing coefficients in 4 x 4 groups.

**Book comparison:** relative to AVC CABAC, Chapter 8 reports 154 versus 441 contexts, a 30-fold worst-case line-buffer reduction for the stated 4K case, about nine-fold fewer worst-case regular bins, and 1.5-fold fewer total worst-case bins. These are design-analysis figures under the chapter's assumptions, not end-to-end decoder speed ratios.

**Book experiment:** improved HEVC transform-coefficient coding provided average BD-rate savings of roughly 3.35-4.78% over the chapter's straightforward AVC-style extension, depending on coding configuration. Most later changes emphasized throughput with little coding loss.

---

# 9. Rate-distortion optimization and performance memory

## The encoder problem

The standard offers many legal combinations. The encoder needs a common score for choices that trade fewer bits against lower distortion.

```text
J = D + lambda R
```

- `D`: distortion between source and reconstruction under a selected metric.
- `R`: actual or estimated syntax rate.
- `lambda`: exchange weight relating rate and distortion.

Neither the distortion metric, lambda formula, candidate order, nor pruning method is universally mandated by HEVC.

## Candidate hierarchy

A high-quality encoder may compare:

- CU split and no-split structures;
- intra and inter prediction;
- intra mode, reference, merge/AMVP candidate, and motion vector;
- PU partition;
- transform tree and transform size;
- coefficient levels through Rate-Distortion Optimized Quantization.

The search is nested, and exact CABAC-rate estimation carries state. Exhaustive evaluation is computationally expensive, so practical encoders screen and prune.

## RDOQ

Rate-Distortion Optimized Quantization evaluates nearby coefficient-level choices, including zero, using their distortion and signaling cost. Scalar rounding is therefore not always the final encoder decision. RDOQ is encoder logic; the decoder only receives the resulting levels.

## Common Test Conditions

Chapter 9 compares HM 12.1 and JM 18.5 under defined Common Test Conditions using:

- All Intra;
- Random Access hierarchical B coding;
- Low Delay P;
- Low Delay B;
- four initial QP points: 22, 27, 32, and 37;
- standard sequence classes spanning high resolution, conferencing, mobile, and screen content.

Rate-distortion curves, not single files, are compared. Bjontegaard Delta rate measures average horizontal separation over an overlapping quality range. PSNR is reproducible but not a complete model of perception.

## Objective results from Chapter 9

The reported overall luma BD-rate savings of HEVC HM 12.1 over AVC JM 18.5 were approximately:

| Configuration | Reported saving |
|---|---:|
| All Intra | 21.9% |
| Random Access | 42.7% |
| Low Delay B | 36.6% |
| Low Delay P | 35.3% |

These numbers support the broad efficiency claim but do not equal 50% in every mode. Random Access benefits more from HEVC's inter tools than All Intra.

Some 4K sequences showed larger gains, up to 76% in the reported non-CTC table. That upper value is sequence-specific and must not be quoted as a general HEVC advantage.

## Subjective results

The controlled subjective tests in Chapter 9 reported large savings on a small set of 4K sequences. A separate production-encoder comparison found viewers had no preference or preferred HEVC in 69.4% of trials when the HEVC encodes used about 60% lower bitrate. These results depend on selected sequences, encoders, viewing setup, and operating points.

The correct presentation statement is: controlled objective and subjective studies demonstrated substantial HEVC gains over AVC, often approaching the design goal, with the measured percentage depending on configuration and content.

---

# 10. Decoder hardware memory

## Main challenges

The decoder must support variable CTU/CU/PU/TU sizes, large 64 x 64 roots, transforms up to 32 x 32, longer interpolation filters, neighboring dependencies, CABAC's variable workload, and off-chip reference memory.

The chapter identifies four system-pipeline pressures:

1. processing dependencies;
2. large CTU storage;
3. picture-width line buffers;
4. variable off-chip DRAM latency.

## Pipeline granularity

Smaller pipeline units reduce latency and buffering, but an operation cannot begin before its required data are available. The inverse transform needs a complete TU. Prediction and filters need boundary data. The design uses variable-sized pipeline blocks tied to CTU geometry.

A useful example is coefficient buffering. Two full variable-sized blocks would require about 24 kB in the architecture. Because the maximum TU is 32 x 32, ping-pong buffering two TUs needs only about 4 kB, saving 20 kB. This is a strong example of algorithm structure enabling memory reduction.

## Split pipeline and entropy decoupling

The design separates entropy decoding from reconstruction using buffering because CABAC workload varies with bitrate while reconstruction workload scales more closely with pixel rate. Frame-level entropy decoupling can smooth workload and expose CTU-row parallelism, but adds frame delay and memory bandwidth.

If latency cannot tolerate this, slices, tiles, or WPP permit multiple entropy substreams when the bitstream enables them.

## Inverse transform hardware

A separable N x N transform performs column and row stages with transpose storage. The 32-point transform increases arithmetic and memory cost dramatically relative to AVC's maximum 8-point transform. Hardware exploits odd-even decomposition, matrix symmetry, repeated coefficient magnitudes, and shared datapaths across sizes.

## Motion-compensation memory

Interpolation requires neighborhoods beyond the requested prediction block, producing irregular DRAM access and redundant fetches. A motion-compensation cache, DRAM-aware mapping, and parallel cache banks reduce external traffic. Cache power must be judged against the DRAM power it avoids.

## Intra and filters

Intra prediction uses hierarchical storage for top and left reconstructed neighbors. Deblocking and SAO need carefully scheduled line and column storage. SAO is computationally simple per sample but still has neighbor and parameter dependencies.

## Test-chip result and caution

The Chapter 10 test chip used 40 nm CMOS, 715K gates, and 124 kB SRAM. It reported 249 Mpixel/s and 76 mW average core power for 3840 x 2160 at 30 fps, or 0.31 nJ/pixel.

Important limitation: the chip implemented HEVC Working Draft 4 / HM 4.0 low-complexity features, omitted SAO, and used CAVLC rather than final-standard CABAC. It must not be presented as a full final HEVC decoder result.

The cache consumed meaningful on-chip power, but the estimated DRAM power saving was about six times the cache power. This supports the research principle that local memory can save total system energy when it replaces costly external transfers.

---

# 11. Encoder hardware memory

## Why encoding dominates research complexity

The encoder must search among partitions, prediction modes, motion candidates, transforms, coefficient levels, and filter parameters. HM was designed primarily as a coding-efficiency reference, not as a real-time hardware blueprint.

The Chapter 11 architecture separates:

- **prediction core:** intra, integer and fractional motion estimation, transform/quantization, and candidate generation;
- **reconstruction core:** inverse operations, reconstructed references, deblocking, and SAO;
- **bitstream core:** CABAC and final output.

SAO parameter estimation must occur before those parameters are entropy-coded.

## CTU order and locality

CABAC syntax must follow the required coding order, but motion-search memory benefits from two-dimensional locality. A frame-level CABAC pipeline can permit prediction/reconstruction to use a locality-friendly order and entropy coding to return to conforming order, at the cost of external storage and latency.

The design problem is a joint choice among:

```text
coding loss - CABAC throughput - on-chip area - DRAM bandwidth - latency
```

## Motion-estimation hierarchy

Integer Motion Estimation searches a broad region coarsely. Fractional Motion Estimation refines candidates using interpolation. Parallel search increases throughput but multiplies reference bandwidth. The chapter's memory hierarchy reuses search-window data at several levels and changes data layout to support subsampled IME and full-sample FME access patterns.

This chapter's motion-estimation algorithm is an architecture example, not the HEVC algorithm.

## Fast intra search

The example hardware performs a coarse directional search, refines near the best direction, and uses any remaining budget for additional modes. In its reported All Intra experiment, 12 candidates produced about 0.19% BD-rate increase relative to the anchor.

The architecture also replaces unavailable reconstructed neighbors with original samples in a hybrid open/closed-loop approximation to remove dependencies and expose PU parallelism. That change produced about 1.03% BD-rate increase in the reported Low Delay P test. It is not normative and can create encoder-decision loss even though the final bitstream remains conforming.

## Transform simplification

The hardware chooses the largest permitted transform within the PU boundary instead of performing a full transform-tree search. This regularizes computation but caused about 3.02% BD-rate increase in the reported Low Delay P condition.

This is direct evidence for why a separate TU exists: adaptive transform partitioning has measurable coding value, but searching it has hardware cost.

## Two-stage RDO

HM-style mode decision uses:

1. fast RDO with SAD or SATD and approximate mode/motion bits to prune;
2. full RDO with transform, quantization, reconstruction, SSD, and CABAC-rate estimation for finalists.

Removing full RDO entirely produced very large losses in the chapter's study: roughly 10-15% BD-rate increase for intra pictures and over 40% for some inter conditions. The proposed hardware retained full RDO for six finalists and accepted about 5.93% BD-rate increase.

## CABAC-rate estimation

Exact rate estimation for every candidate would require separate evolving CABAC context state, which is expensive to replicate. The chapter studies:

- table-based state-aware estimation;
- bypass-bin counting without contexts;
- Context-Fixed Binary Arithmetic Coding, holding states fixed within a CTU and updating after the winner is selected.

The fixed-context approach shares state memory and estimates bits from zero/one counts per context. The chapter reports around 1.25% BD-rate increase for that approach relative to its CABAC-based anchor, with much lower state cost.

## Encoder test-chip result and caution

The example 28 nm encoder supports 8192 x 4320 at 30 fps, 1062 Mpixel/s, and a large predictor-centered search range. It reports 8.35 million gates, a 5 x 5 mm core, 312 MHz operation, 708 mW, and 6.80 GB/s bandwidth.

Its accumulated algorithm simplifications produced approximately 22.6% BD-rate increase relative to HM 4.0 on the reported cropped 8K tests. The result demonstrates real-time feasibility, not reference-software-equivalent efficiency. It also included an HM4 frame-level loop-filter implementation later removed from the final standard, so module costs need care when comparing against final-compliant designs.

---

# 12. Quantitative results worth retaining

Every value in this table is tied to the book's stated software or hardware. Do not detach the number from its condition.

| Topic | Reported result | Condition or limitation |
|---|---:|---|
| Flexible block partitioning | More than half of the HEVC-over-AVC HD gain in Ch. 3 | Chapter-level attribution under its experiments |
| Merge and skip | About 6-8% average BD-rate saving | HM 8.0, reported RA/LB/LD configurations |
| HEVC luma interpolation | 4.0% RA, 4.9% LB, 2.6% LP average saving | HM 6.0 substitution experiment |
| Larger 16/32 transforms | 5.6% AI, 6.4% RA, 6.8% LDB average saving | HM 9.0.1 versus 4/8-only transforms |
| Sign-bit hiding | About 0.6-0.9% saving | Common test conditions reported in Ch. 8 |
| Improved coefficient coding | About 3.35-4.78% saving | Versus the chapter's AVC-style extension |
| In-loop filters together | About 2.6-15% coding gain | Depends strongly on configuration |
| HEVC versus AVC objective | 21.9% AI, 42.7% RA, 36.6% LDB, 35.3% LDP | HM 12.1 versus JM 18.5, luma BD-rate |
| Decoder test chip | 0.31 nJ/pixel at 4K30 | WD4, no SAO, CAVLC instead of final CABAC |
| Encoder test chip | 8K30 at 708 mW | 28 nm architecture; about 22.6% BD-rate loss versus HM4 |

These results demonstrate tradeoffs; they are not universal constants.

---

# 13. Research lessons for this project

## The most promising bridge to Professor Sen's work

The book's final chapters support a disciplined connection between HEVC and energy-constrained imaging hardware:

1. Flexible coding tools reduce bitrate but increase search and control.
2. Reference-picture and search-window movement can dominate energy.
3. Local SRAM and data reuse may cost power locally while reducing larger DRAM cost.
4. Approximate encoder decisions can preserve bitstream conformance while trading BD-rate for energy or throughput.
5. Approximate reconstruction arithmetic is riskier because mismatch can propagate through the closed loop.
6. Transform and quantization hardware is only one part of a video encoder; temporal prediction, memory, RDO, and entropy coding must be included in system claims.

## Research questions derived from the book

- Which encoder stages dominate total energy after external memory is included?
- Can CU, PU, or TU candidate pruning use a measured energy term in addition to rate and distortion?
- Which arithmetic approximations alter only encoder decisions, and which alter decoder-visible reconstruction?
- Can sensor-side compression preserve enough information for later motion estimation?
- What is the Pareto frontier among BD-rate, energy per pixel, latency, area, and bandwidth?
- Can CABAC-rate estimation be simplified without degrading mode decisions excessively?
- Can reconstructed-reference storage or motion-search windows be compressed without increasing prediction error disproportionately?
- How do conclusions change for modern x265, current hardware nodes, and contemporary memory systems compared with the HM-era examples in this 2014 book?

## Experiment template

For an implementation study, record:

```text
Normative target and profile:
Anchor encoder and exact version:
Configuration and speed setting:
Sequences, resolution, frame count, bit depth, chroma format:
Candidate tool or hardware change:
Rate metric and quality metric:
Energy, power, throughput, latency, area, and memory traffic:
What is included or excluded from measurement:
Per-sequence results and aggregate method:
Conformance verification:
```

---

# 14. Statements to avoid

- “HEVC is an encoding algorithm.” It is primarily a bitstream format and decoding specification with substantial encoder freedom.
- “HEVC always halves the file size.” Gains depend on encoder, content, configuration, rate, and metric.
- “An I picture is automatically a random-access point.” IRAP type and reference structure matter.
- “List 0 means past and List 1 means future.” They are ordered reference lists, not fixed time labels.
- “The transform removes high frequencies.” It changes representation; quantization discards precision.
- “Increasing QP by six halves the bitrate.” It approximately doubles quantization step, not predictably bitrate.
- “RDO chooses everything in one exhaustive search.” Real encoders screen, prune, estimate, and use implementation-specific order.
- “CABAC sends a count of significant positions.” HEVC residual syntax sends last position, group/significance data, levels, signs, and remainders under specified parsing rules.
- “Greater1 and greater2 are asked for every nonzero coefficient.” Their occurrence is limited per 4 x 4 coefficient group.
- “The remainder is always magnitude minus three encoded with Exp-Golomb.” HEVC uses adaptive Rice coding with an escape mechanism and context-dependent base levels.
- “SAO smooths the image.” It applies class-dependent signed offsets and can restore edge contrast.
- “Tiles and WPP make CABAC internally parallel.” They create multiple substreams or scheduling boundaries; dependencies remain inside each substream.
- “The hardware results are final HEVC baselines.” Both hardware chapters analyze specific early-HM or working-draft architectures with explicit limitations.

---

# 15. High-value figures and tables

| Locator | Why it matters |
|---|---|
| Fig. 2.1 | Encoder/decoder and high-level syntax context |
| Table 2.1 | HEVC VCL NAL-unit types |
| Figs. 2.3-2.9 | Temporal layers, leading/trailing pictures, random access and splicing |
| Fig. 3.1 | Hybrid block-based encoder structure |
| Figs. 3.2-3.7 | CTU/CU/PU/TU and residual quadtree relationships |
| Fig. 3.12 | Two-CTU-offset WPP schedule and context propagation |
| Figs. 4.2 and 4.5 | All intra modes and angular directions |
| Fig. 5.4 | AMVP and merge candidate locations |
| Tables 5.2 and 5.3 | Merge/skip gain and parallel merge-region tradeoff |
| Fig. 6.2 | Embedded 4/8/16 transforms inside the 32-point matrix |
| Tables 6.6 and 6.7 | Transform-size and transform-design coding gains |
| Figs. 7.13-7.16 | SAO Edge Offset and Band Offset motivation |
| Figs. 8.1 and 8.2 | CABAC stages and finite-state probability model |
| Fig. 8.9 | HEVC 4 x 4 coefficient-coding example |
| Fig. 8.21 | Correct transform-block significance-map flow |
| Figs. 8.22-8.25 | Absolute-level binarization, scan passes, signs and remainders |
| Tables 8.18-8.21 | Throughput and memory reductions in HEVC CABAC |
| Fig. 9.4 | Rate-distortion curves and BD-rate versus BD-PSNR |
| Tables 9.5-9.10 | Objective and subjective HEVC/AVC comparisons |
| Figs. 10.1-10.3 | Decoder split pipeline and buffer organization |
| Table 10.14 | Decoder test-chip specification and its limitations |
| Fig. 11.1 | Encoder prediction, reconstruction, and bitstream cores |
| Figs. 11.14-11.17 | HM RDO and hardware-oriented rate estimation |
| Tables 11.4-11.6 | Encoder test-chip results and algorithmic losses |

---

# 16. One coherent explanation of HEVC

HEVC divides a picture into Coding Tree Units and recursively selects Coding Units. For each CU, the encoder chooses intra or inter prediction and a legal Prediction Unit structure. It subtracts that reproducible prediction from the source to obtain a residual. A separately chosen Transform Unit structure represents the residual with integer DCT-like transforms, or the 4 x 4 intra-luma DST where applicable. Quantization maps coefficients to levels under QP-derived scaling, creating the main deliberate loss. CABAC then codes partition, prediction, transform, coefficient, filter, and related syntax using normative binarization, context, bypass, and arithmetic-coding rules.

At the same time, the encoder inverse-quantizes and inverse-transforms the chosen levels, adds the prediction, and applies deblocking followed by SAO. This local reconstruction matches what the decoder will use as a future reference and prevents encoder-decoder drift. The standard defines how a selected bitstream is decoded; the encoder's search and rate-control strategy remain largely open. That freedom is where coding efficiency, runtime, power, memory traffic, and latency are traded—and where much of the research opportunity lies.

## Maintenance rule

When this memory conflicts with the current H.265 specification, the specification wins. When it conflicts with a named implementation, first determine whether the disagreement concerns normative decoding, an encoder choice, a later HEVC extension, or a book-era HM version. Update this file only with a source-backed correction and preserve the distinction.
