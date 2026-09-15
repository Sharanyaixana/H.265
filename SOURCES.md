# Sources — Master Reference List

A single indexed list of every source used in this repo. **Cite these from any other file** so references stay consistent.

### How to reference
Each source has a stable ID (`S1`, `S2`, …) and an anchor. To cite it elsewhere, link to the anchor:

From a root-level file:

```markdown
The HEVC overview [[S3]](SOURCES.md#s3) describes the standard's design goals.
```

From `notes/` or `papers/`:

```markdown
The HEVC overview [[S3]](../SOURCES.md#s3) describes the standard's design goals.
```

**Link status:** ✅ previously opened successfully · ⚠️ bibliographic identifier or URL still requires verification before formal citation.

---

## A. Standards

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s1"></a>**S1** | ITU-T Rec. **H.265** — High Efficiency Video Coding | https://www.itu.int/rec/T-REC-H.265 ⚠️ | The official standard text (bitstream + decoder). |
| <a id="s2"></a>**S2** | **ISO/IEC 23008-2** (MPEG-H Part 2) — High Efficiency Video Coding | https://www.iso.org/standard/85457.html ⚠️ | The technically aligned ISO/IEC specification for HEVC. |

## B. Core video-coding papers (read these to learn HEVC)

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s3"></a>**S3** | G. J. Sullivan, J.-R. Ohm, W.-J. Han, T. Wiegand, "Overview of the HEVC Standard," *IEEE Trans. CSVT*, 22(12), 2012 | DOI: 10.1109/TCSVT.2012.2221191 ⚠️ | Technical overview of HEVC's goals and coding tools. |
| <a id="s4"></a>**S4** | J.-R. Ohm et al., "Comparison of the Coding Efficiency of Video Coding Standards—Including HEVC," *IEEE Trans. CSVT*, 22(12), 2012 | DOI: 10.1109/TCSVT.2012.2221192 ⚠️ | Comparative test design and measured HEVC coding efficiency. |
| <a id="s5"></a>**S5** | B. Bross et al., "Overview of the Versatile Video Coding (VVC) Standard," *IEEE Trans. CSVT*, 2021 | DOI: 10.1109/TCSVT.2021.3101953 ⚠️ | H.266/VVC — the successor to HEVC. |
| <a id="s6"></a>**S6** | Y. Chen et al., "An Overview of Core Coding Tools in the AV1 Video Codec," *Picture Coding Symposium (PCS)*, 2018 | DOI: 10.1109/PCS.2018.8456249 ⚠️ | Technical overview of AV1's coding tools. |

## C. Books

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s7"></a>**S7** | V. Sze, M. Budagavi, G. J. Sullivan (eds.), *High Efficiency Video Coding (HEVC): Algorithms and Architectures*, Springer, 2014 | https://link.springer.com/book/10.1007/978-3-319-06895-4 ⚠️ · local copy: [papers/High_Efficiency_Video_Coding_HEVC.pdf](papers/High_Efficiency_Video_Coding_HEVC.pdf) ✅ | Detailed algorithms and hardware-architecture reference. |
| <a id="s8"></a>**S8** | I. E. Richardson, *The H.264 Advanced Video Compression Standard*, 2nd ed., Wiley, 2010 | https://www.wiley.com/en-us/9780470516928 ⚠️ | Clearest ground-up explanation of the hybrid codec framework. |
| <a id="s9"></a>**S9** | Y. Q. Shi, H. Sun, *Image and Video Compression for Multimedia Engineering*, CRC Press | https://www.routledge.com/9781466615915 ⚠️ | Fundamentals: DCT, motion, entropy coding. |

## D. Prof. Shreyas Sen (SPARC Lab) — papers relevant to your topic

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s10"></a>**S10** | M.-C. Li, A. Ghosh, S. Sen, "Approximate DCT and Quantization Techniques for Energy-Constrained Image Sensors," *IEEE TCAD*, 2024 (SPARC J97) | DOI: 10.1109/TCAD.2024.3434333 · arXiv: https://arxiv.org/abs/2406.16358 ✅ · IEEE: https://ieeexplore.ieee.org/document/10609963 ✅ | Approximate transform and quantization hardware for energy-constrained image sensors; relevant to HEVC by method, not an HEVC implementation. |
| <a id="s11"></a>**S11** | "dAJC: A 2.02-mW 50-Mb/s Direct Analog-to-MJPEG Converter for Video Sensor Nodes …," *IEEE JSSC*, 2025 (SPARC J106; CICC 2023 = C116) | via S15 ✅ | Compress in the analog domain at the sensor — hardware co-design philosophy. |
| <a id="s12"></a>**S12** | "A 2 pJ/pixel Time-Domain Weight and Integrating-MAC based direct-Analog-to-MJPEG Compression for Video Sensor Nodes," *CICC*, 2025 (SPARC C136) | via S15 ✅ | Ultra-low-energy per-pixel compression hardware. |
| <a id="s13"></a>**S13** | "A 65nm 21.9 pJ/Sa Pixel-to-PWM Conversion SoC … for ULP Body-Worn Video Sensor Nodes with Distributed Real-Time Inference," *CICC*, 2024 (SPARC C129) | via S15 ✅ | In-sensor low-power imaging + inference. |
| <a id="s14"></a>**S14** | "A Real-Time Memory-Less In-Sensor Time-Domain Convolution Processor with Programmable Kernel for Feature Extraction," *ISCAS*, 2025 (SPARC C137) | via S15 ✅ | In-sensor compute context. |

## E. Lab & author pages

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s15"></a>**S15** | SPARC Lab — publications list (authoritative, up to date) | https://engineering.purdue.edu/~shreyas/SparcLab/publications.html ✅ | Full paper list + PDFs for S10–S14. |
| <a id="s16"></a>**S16** | SPARC Lab — home | https://engineering.purdue.edu/~shreyas/SparcLab/ ✅ | Lab overview, research themes, openings. |
| <a id="s17"></a>**S17** | Shreyas Sen — Purdue ECE faculty page | https://engineering.purdue.edu/ECE/People/ptProfile?resource_id=134162 ✅ | Bio, research interests. |
| <a id="s18"></a>**S18** | Shreyas Sen — Google Scholar | https://scholar.google.com/citations?user=_4mmNBMAAAAJ ✅ | Citation-sorted full publication list. |

## F. Software & tools (for the hands-on labs)

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s19"></a>**S19** | FFmpeg | https://ffmpeg.org ✅ | Encode, decode, transcode, and inspect HEVC using supported libraries such as `libx265`. |
| <a id="s20"></a>**S20** | FFmpeg — H.265/HEVC encoding guide (wiki) | https://trac.ffmpeg.org/wiki/Encode/H.265 ⚠️ | CRF, presets, x265 params — practical recipes. |
| <a id="s21"></a>**S21** | x265 (MulticoreWare) + docs | https://www.videolan.org/developers/x265.html ✅ · docs: https://x265.readthedocs.io/en/stable/cli.html ✅ | Production open-source HEVC encoder; use its named options to study real encoder decisions and rate control. |
| <a id="s22"></a>**S22** | HM — HEVC Test Model (JCT-VC reference software) | https://vcgit.hhi.fraunhofer.de/jvet/HM ✅ · documentation: https://hevc.hhi.fraunhofer.de/HM-doc/ ✅ | Reference encoder/decoder for experiments and syntax tracing; record the exact HM tag. |
| <a id="s23"></a>**S23** | libde265 — open-source HEVC decoder | https://github.com/strukturag/libde265 ⚠️ | Read the decode path. |
| <a id="s24"></a>**S24** | OpenHEVC — open-source HEVC decoder | https://github.com/OpenHEVC/openHEVC ⚠️ | Alternative decoder to study. |
| <a id="s25"></a>**S25** | Netflix VMAF — perceptual quality metric | https://github.com/Netflix/vmaf ✅ | Perceptual quality scoring in experiments. |
| <a id="s26"></a>**S26** | CompressAI — learned image/video compression (PyTorch) | https://github.com/InterDigitalInc/CompressAI ⚠️ | Explore neural codecs (research context). |

## G. Beginner-friendly primers

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s27"></a>**S27** | L. Moreira, *Digital Video Introduction* (open-source) | https://github.com/leandromoreira/digital_video_introduction ✅ | Beginner orientation to samples, color, subsampling, transforms, prediction, and picture types. Verify HEVC-specific details against S1 or S3. |
| <a id="s28"></a>**S28** | Forasoft, *GOP Structure & I/P/B Frames* | https://www.forasoft.com/learn/video-encoding/articles/gop-structure-ipb-frames ✅ | Introductory explanation of I/P/B pictures, open and closed GOPs, random access, hierarchical B structures, and streaming considerations. |
| <a id="s29"></a>**S29** | Videospan, *Video Codecs Explained (H.264/HEVC/AV1/VP9)* | https://videospan.com/resources/video-technology/codecs ⚠️ | Plain-English framing for "what is HEVC" and the codec landscape (talk topics 2 & 6). |
| <a id="s30"></a>**S30** | Reducible (YouTube) — *The Unreasonable Effectiveness of JPEG* | https://www.youtube.com/@Reducible ⚠️ (search "Reducible JPEG") | Visual introduction to transform coding and quantization. |
| <a id="s31"></a>**S31** | vcodex (Iain Richardson) — *H.264/AVC Intra Prediction* | https://www.vcodex.com/h264avc-intra-prediction ✅ | Introductory visual explanation of AVC intra prediction; use it for the underlying idea before comparing HEVC's expanded mode set. |
| <a id="s32"></a>**S32** | Elecard — *Spatial (Intra) Prediction in HEVC* | https://www.elecard.com/page/spatial_intra_prediction_in_hevc ✅ | The 35-mode HEVC intra fan diagram; Planar/DC/angular illustrated. |
| <a id="s33"></a>**S33** | Yao Wang (NYU) — *Predictive & Block-Based Hybrid Video Coding* (lecture slides, PDF) | https://eeweb.engineering.nyu.edu/~yao/EL6123_s16/PredictiveVideocoding.pdf ✅ | Slide deck with the full encoder/decoder loop diagram + reconstruction feedback path. |
| <a id="s34"></a>**S34** | Wikipedia — *High Efficiency Video Coding (HEVC)* | https://en.wikipedia.org/wiki/High_Efficiency_Video_Coding ✅ | Quick factual reference: history, versions, profiles, adoption, licensing. |

## H. Presentation 2 — "Inside the HEVC Coding Loop" (internals)

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s35"></a>**S35** | vcodex (Iain Richardson) — *HEVC: An Introduction to High Efficiency Coding* | https://www.vcodex.com/hevc-an-introduction-to-high-efficiency-coding ✅ | Introductory explanation of HEVC partitioning and coding tools. |
| <a id="s36"></a>**S36** | vcodex — *H.264/AVC Context-Adaptive Binary Arithmetic Coding (CABAC)* | https://www.vcodex.com/h264avc-context-adaptive-binary-arithmetic-coding-cabac ✅ | Deep dive on CABAC's mechanics (binarization, context modeling, arithmetic coding) — HEVC uses the same core method. |
| <a id="s37"></a>**S37** | Wikipedia — *Context-adaptive binary arithmetic coding* | https://en.wikipedia.org/wiki/Context-adaptive_binary_arithmetic_coding ✅ | Quick reference for CABAC's three stages. |
| <a id="s38"></a>**S38** | vcodex — *H.264/AVC 4×4 Transform and Quantization* | https://www.vcodex.com/h264avc-4x4-transform-and-quantization ✅ | How the integer transform concentrates energy and how quantization discards it — HEVC extends the same idea to larger blocks. |
| <a id="s39"></a>**S39** | vcodex — *H.264/AVC Loop Filter* | https://www.vcodex.com/h264avc-loop-filter ✅ | The deblocking filter concept that HEVC's deblocking stage builds on (HEVC adds SAO on top). |
| <a id="s40"></a>**S40** | Wikipedia — *Deblocking filter (video)* | https://en.wikipedia.org/wiki/Deblocking_filter_(video) ✅ | Quick reference on why/how deblocking removes block artifacts. (Doesn't cover SAO — use [S3] for that.) |
| <a id="s41"></a>**S41** | Wikipedia — *Rate–distortion optimization* | https://en.wikipedia.org/wiki/Rate%E2%80%93distortion_optimization ✅ | Confirms the `J = D + λR` Lagrangian cost formulation used across video coding. |

## I. Closed-loop and rate-distortion foundations

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s42"></a>**S42** | A. N. Netravali, J. D. Robbins, "Motion-Compensated Television Coding: Part I," *Bell System Technical Journal*, 58(3), 1979 | DOI: 10.1002/j.1538-7305.1979.tb02237.x ⚠️ | Early analysis of motion-compensated predictive television coding. |
| <a id="s43"></a>**S43** | H. G. Musmann, P. Pirsch, H.-J. Grallert, "Advances in Picture Coding," *Proceedings of the IEEE*, 73(4), 1985 | DOI: 10.1109/PROC.1985.13172 ⚠️ | Historical survey of hybrid picture coding using motion-compensated prediction, transform coding, and reconstruction feedback. |
| <a id="s44"></a>**S44** | G. J. Sullivan, T. Wiegand, "Rate-Distortion Optimization for Video Compression," *IEEE Signal Processing Magazine*, 15(6), 1998 | DOI: 10.1109/79.733497 ⚠️ | Tutorial treatment of Lagrangian rate-distortion methods used in video-encoder decisions. |
| <a id="s45"></a>**S45** | ITU-T Rec. H.261 — Video codec for audiovisual services at p × 64 kbit/s | https://www.itu.int/rec/T-REC-H.261 ⚠️ | Historical standardized example of motion-compensated predictive coding with reconstruction feedback. |

## J. Quality metrics, complexity, and broader approximate-computing literature

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s46"></a>**S46** | G. Bjøntegaard, "Calculation of Average PSNR Differences Between RD-Curves," ITU-T SG16 Q.6 VCEG, Doc. VCEG-M33, 2001 | https://www.itu.int/wftp3/av-arch/video-site/0104_Aus/VCEG-M33.doc ⚠️ | Original curve-based comparison method commonly used for BD-rate reporting. |
| <a id="s47"></a>**S47** | Z. Wang, A. C. Bovik, H. R. Sheikh, E. P. Simoncelli, "Image Quality Assessment: From Error Visibility to Structural Similarity," *IEEE Trans. Image Processing*, 13(4), 2004 | DOI: 10.1109/TIP.2003.819861 ⚠️ | The original SSIM paper — the standard alternative/complement to PSNR. |
| <a id="s48"></a>**S48** | Z. Li, A. Aaron, I. Katsavounidis, A. Moorthy, M. Manohara, "Toward a Practical Perceptual Video Quality Metric," Netflix Technology Blog, 2016 | https://netflixtechblog.com/toward-a-practical-perceptual-video-quality-metric-653f208b9652 ✅ | VMAF's origin and rationale — pairs with [[S25]](SOURCES.md#s25) (the VMAF codebase itself). |
| <a id="s49"></a>**S49** | S. Mittal, "A Survey of Techniques for Approximate Computing," *ACM Computing Surveys*, 48(4), 2016 | DOI: 10.1145/2893356 ⚠️ | Broader field grounding for approximate computing — shows Prof. Sen's approach within the wider research landscape, not as an isolated technique. |
| <a id="s50"></a>**S50** | G. Corrêa, P. Assunção, L. Agostini, L. A. da Silva Cruz, "Complexity Control of High Efficiency Video Encoders for Power-Constrained Devices," *IEEE Trans. Consumer Electronics*, 57(4), 2011 | DOI: 10.1109/TCE.2011.6131165 ✅ | Early HEVC complexity-control work based on constraining Coding Unit depth. |

## K. Emerging open-media standards

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s51"></a>**S51** | Alliance for Open Media — Specifications | https://aomedia.org/specifications/ ✅ | Authoritative current list of AOMedia specifications, including AV1 and the developing AV2 specification. |

## L. Focused HEVC further reading

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s52"></a>**S52** | R. Sjöberg et al., “Overview of HEVC High-Level Syntax and Reference Picture Management,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2223052 ✅ | NAL-unit headers, parameter sets, picture types, random access, references, and SEI. |
| <a id="s53"></a>**S53** | I.-K. Kim et al., “Block Partitioning Structure in the HEVC Standard,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2223011 ✅ | Why HEVC separates CU, PU, and TU, with coding-efficiency and complexity evidence. |
| <a id="s54"></a>**S54** | J. Lainema et al., “Intra Coding of the HEVC Standard,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2221525 ✅ | Intra reference samples, angular and planar prediction, mode coding, scanning, and complexity. |
| <a id="s55"></a>**S55** | J. Sole et al., “Transform Coefficient Coding in HEVC,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2223055 ✅ | Actual coefficient scan, last-significant position, significance maps, levels, signs, and sign-data hiding. |
| <a id="s56"></a>**S56** | V. Sze and M. Budagavi, “High Throughput CABAC Entropy Coding in HEVC,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2221526 ✅ · author PDF: https://www.mit.edu/~sze/papers/sze_tcsvt_2012.pdf ✅ | CABAC throughput bottlenecks, context dependencies, bypass-bin grouping, and hardware-aware design. |
| <a id="s57"></a>**S57** | A. Norkin et al., “HEVC Deblocking Filter,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2223053 ✅ | Boundary strength, filter decisions, strong and weak filtering, clipping, and parallelism. |
| <a id="s58"></a>**S58** | C.-M. Fu et al., “Sample Adaptive Offset in the HEVC Standard,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2221529 ✅ | Band Offset, Edge Offset, encoder parameter selection, decoder application, and coding gain. |
| <a id="s59"></a>**S59** | F. Bossen, B. Bross, K. Sühring, D. Flynn, “HEVC Complexity and Implementation Analysis,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2221255 ✅ · Fraunhofer record: https://publica.fraunhofer.de/entities/publication/7598c239-51e1-4d35-8351-105131729966 ✅ | Decoder profiling, encoder-search complexity, and careful AVC/HEVC implementation comparison. |
| <a id="s60"></a>**S60** | T. K. Tan et al., “Video Quality Evaluation Methodology and Verification Testing of HEVC Compression Performance,” *IEEE TCSVT*, 26(1), 2016 | https://doi.org/10.1109/TCSVT.2015.2477916 ✅ | Objective and subjective verification methodology, content dependence, and interpretation of HEVC gains. |
| <a id="s61"></a>**S61** | C. C. Chi et al., “Parallel Scalability and Efficiency of HEVC Parallelization Approaches,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2223056 ✅ | Tiles, WPP, parallel speedup, scaling limits, and implementation tradeoffs. |
| <a id="s62"></a>**S62** | T. Schierl, M. M. Hannuksela, Y.-K. Wang, S. Wenger, “System Layer Integration of High Efficiency Video Coding,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2223054 ✅ | Relationship among HEVC NAL units, RTP, MPEG-2 TS, ISO Base Media File Format, and DASH. |
| <a id="s63"></a>**S63** | FFmpeg documentation — `ffprobe` and `trace_headers` | https://ffmpeg.org/ffprobe.html ✅ · https://ffmpeg.org/ffprobe-all.html#trace_005fheaders ✅ | Inspect streams, packets, frame order, metadata, and syntax above the coded-block level. |
| <a id="s64"></a>**S64** | NPTEL, “Digital Video Signal Processing,” Prof. Sumana Gupta, IIT Kanpur | https://nptel.ac.in/courses/117104020 ✅ · syllabus: https://archive.nptel.ac.in/content/syllabus_pdf/117104020.pdf ✅ | Structured prerequisite course on video formation, representation, perception, transforms, motion, and coding. |
| <a id="s65"></a>**S65** | P. Helle et al., “Block Merging for Quadtree-Based Partitioning in HEVC,” *IEEE TCSVT*, 22(12), 2012 | https://doi.org/10.1109/TCSVT.2012.2223051 ✅ | Merge candidates, motion-data inheritance, signaling reduction, and complexity. |
| <a id="s66"></a>**S66** | F. Bossen, “Common Test Conditions and Software Reference Configurations,” JCTVC-G1200, 2011 | https://www.itu.int/wftp3/av-arch/jctvc-site/2011_11_G_Geneva/documents/JCTVC-G1200.pdf ✅ | Why codec experiments need agreed sequences, configurations, QPs, and reporting conditions. |
| <a id="s67"></a>**S67** | JVET, VVC Test Model (VTM) reference software | https://vcgit.hhi.fraunhofer.de/jvet/VVCSoftware_VTM ✅ | Reference implementation for studying H.266/VVC tools and running controlled comparisons; always record the exact release tag. |
| <a id="s68"></a>**S68** | Alliance for Open Media, *AV1 Bitstream & Decoding Process Specification* | https://aomediacodec.github.io/av1-spec/av1-spec.pdf ✅ | Normative AV1 bitstream and decoder behavior; use it when comparing actual syntax rather than marketing claims. |
| <a id="s69"></a>**S69** | Alliance for Open Media, SVT-AV1 encoder and technical documentation | https://gitlab.com/AOMediaCodec/SVT-AV1 ✅ · documentation: https://gitlab.com/AOMediaCodec/SVT-AV1/-/tree/master/Docs ✅ | Production AV1 encoder, including design notes on prediction, transforms, filtering, rate control, mode decision, and parallelism. |
| <a id="s70"></a>**S70** | J. Li, B. Li, Y. Lu, “Deep Contextual Video Compression,” *NeurIPS*, 2021 | https://proceedings.neurips.cc/paper/2021/hash/96b250a90d3cf0868c83f8c965142d2a-Abstract.html ✅ | A concrete learned-video-compression architecture that replaces simple residual prediction with learned conditional coding; read critically against named conventional-codec anchors. |

---

### Maintenance notes
- Before formal citation, verify every ⚠️ entry against the publisher or standards body. A working URL is not proof that a technical claim or bibliographic field is correct.
- Add new sources at the end with the next ID, **S71**. Do not renumber existing IDs because other files link to these anchors.
- After reading a paper, record its problem, method, evaluation, main result, and limitation in [papers/README.md](papers/README.md).
