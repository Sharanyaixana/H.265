# Sources — Master Reference List

A single indexed list of every source used in this repo. **Cite these from any other file** so references stay consistent.

### How to reference
Each source has a stable ID (`S1`, `S2`, …) and an anchor. To cite it elsewhere, link to the anchor:

```markdown
The HEVC overview [[S1]](../SOURCES.md#s1) reports ~50% bitrate savings.
```

(from a root-level file use `SOURCES.md#s1`; from `notes/` or `papers/` use `../SOURCES.md#s1`).

**Legend:** ⭐ = start here · ✅ link verified in-session · ⚠️ identifier from memory — **verify the DOI/URL before citing in a formal document**.

---

## A. Standards (the actual specifications — reference, not reading)

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s1"></a>**S1** | ITU-T Rec. **H.265** — High Efficiency Video Coding | https://www.itu.int/rec/T-REC-H.265 ⚠️ | The official standard text (bitstream + decoder). |
| <a id="s2"></a>**S2** | **ISO/IEC 23008-2** (MPEG-H Part 2) — identical HEVC text | https://www.iso.org/standard/85457.html ⚠️ | The ISO/MPEG twin of H.265. |

## B. Core video-coding papers (read these to learn HEVC)

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s3"></a>**S3** ⭐ | G. J. Sullivan, J.-R. Ohm, W.-J. Han, T. Wiegand, "Overview of the HEVC Standard," *IEEE Trans. CSVT*, 22(12), 2012 | DOI: 10.1109/TCSVT.2012.2221191 ⚠️ | The single best starting paper; the ~50% goal, all key tools. |
| <a id="s4"></a>**S4** | J.-R. Ohm et al., "Comparison of the Coding Efficiency of Video Coding Standards—Including HEVC," *IEEE Trans. CSVT*, 22(12), 2012 | DOI: 10.1109/TCSVT.2012.2221192 ⚠️ | Where the measured ~50% bitrate-saving claim comes from. |
| <a id="s5"></a>**S5** | B. Bross et al., "Overview of the Versatile Video Coding (VVC) Standard," *IEEE Trans. CSVT*, 2021 | DOI: 10.1109/TCSVT.2021.3101953 ⚠️ | H.266/VVC — the successor to HEVC. |
| <a id="s6"></a>**S6** | Y. Chen et al., "An Overview of Core Coding Tools in the AV1 Video Codec," *Picture Coding Symposium (PCS)*, 2018 | DOI: 10.1109/PCS.2018.8456249 ⚠️ | AV1 — the royalty-free competitor. |

## C. Books

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s7"></a>**S7** ⭐ | V. Sze, M. Budagavi, G. J. Sullivan (eds.), *High Efficiency Video Coding (HEVC): Algorithms and Architectures*, Springer, 2014 | https://link.springer.com/book/10.1007/978-3-319-06895-4 ⚠️ | The HEVC book; architecture/hardware chapters fit Prof. Sen's lab. |
| <a id="s8"></a>**S8** | I. E. Richardson, *The H.264 Advanced Video Compression Standard*, 2nd ed., Wiley, 2010 | https://www.wiley.com/en-us/9780470516928 ⚠️ | Clearest ground-up explanation of the hybrid codec framework. |
| <a id="s9"></a>**S9** | Y. Q. Shi, H. Sun, *Image and Video Compression for Multimedia Engineering*, CRC Press | https://www.routledge.com/9781466615915 ⚠️ | Fundamentals: DCT, motion, entropy coding. |

## D. Prof. Shreyas Sen (SPARC Lab) — papers relevant to your topic

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s10"></a>**S10** ⭐ | M.-C. Li, A. Ghosh, S. Sen, "Approximate DCT and Quantization Techniques for Energy-Constrained Image Sensors," *IEEE TCAD*, 2024 (SPARC J97) | arXiv: https://arxiv.org/abs/2406.16358 ✅ · IEEE: https://ieeexplore.ieee.org/document/10609963 ✅ | **The key HEVC↔lab bridge:** transform+quantization datapath optimized for energy. |
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
| <a id="s19"></a>**S19** | FFmpeg | https://ffmpeg.org ✅ | Encode/inspect HEVC (`libx265`); your main tool. |
| <a id="s20"></a>**S20** | FFmpeg — H.265/HEVC encoding guide (wiki) | https://trac.ffmpeg.org/wiki/Encode/H.265 ⚠️ | CRF, presets, x265 params — practical recipes. |
| <a id="s21"></a>**S21** | x265 (MulticoreWare) + docs | https://www.videolan.org/developers/x265.html ⚠️ · docs: https://x265.readthedocs.io ⚠️ | Production open-source HEVC encoder behind libx265. |
| <a id="s22"></a>**S22** | HM — HEVC Test Model (JCT-VC reference software) | https://vcgit.hhi.fraunhofer.de/jvet/HM ⚠️ | The canonical reference encoder/decoder researchers cite. |
| <a id="s23"></a>**S23** | libde265 — open-source HEVC decoder | https://github.com/strukturag/libde265 ⚠️ | Read the decode path. |
| <a id="s24"></a>**S24** | OpenHEVC — open-source HEVC decoder | https://github.com/OpenHEVC/openHEVC ⚠️ | Alternative decoder to study. |
| <a id="s25"></a>**S25** | Netflix VMAF — perceptual quality metric | https://github.com/Netflix/vmaf ✅ | Perceptual quality scoring in experiments. |
| <a id="s26"></a>**S26** | CompressAI — learned image/video compression (PyTorch) | https://github.com/InterDigitalInc/CompressAI ⚠️ | Explore neural codecs (research context). |

## G. Beginner-friendly primers (best free starting materials)

| ID | Source | Link | Use it for |
|---|---|---|---|
| <a id="s27"></a>**S27** ⭐ | L. Moreira, *Digital Video Introduction* (open-source, free) | https://github.com/leandromoreira/digital_video_introduction ✅ | **The best single free beginner text.** Covers frames, pixels, bit depth, YCbCr, chroma subsampling, redundancy, DCT, intra/inter, frame types — matches Week 1 almost exactly. |
| <a id="s28"></a>**S28** | Forasoft, *GOP Structure & I/P/B Frames* | https://www.forasoft.com/learn/video-encoding/articles/gop-structure-ipb-frames ✅ | Focused GOP reference: I/P/B bitrate ratios, open vs closed GOP, IDR/CRA keyframes, hierarchical B-pyramid, GOP length for streaming/ABR. |
| <a id="s29"></a>**S29** | Videospan, *Video Codecs Explained (H.264/HEVC/AV1/VP9)* | https://videospan.com/resources/video-technology/codecs ⚠️ | Plain-English framing for "what is HEVC" and the codec landscape (talk topics 2 & 6). |
| <a id="s30"></a>**S30** | Reducible (YouTube) — *The Unreasonable Effectiveness of JPEG* (DCT/quantization, visual) | https://www.youtube.com/@Reducible ⚠️ (search "Reducible JPEG") | Best *visual* intuition for spatial/perceptual/statistical redundancy (talk topic 3). |
| <a id="s31"></a>**S31** | vcodex (Iain Richardson) — *H.264/AVC Intra Prediction* | https://www.vcodex.com/h264avc-intra-prediction ✅ | Clearest intro to intra prediction — learn H.264's 9 modes first, then HEVC's 35. By the author of [S8]. |
| <a id="s32"></a>**S32** | Elecard — *Spatial (Intra) Prediction in HEVC* | https://www.elecard.com/page/spatial_intra_prediction_in_hevc ✅ | The 35-mode HEVC intra fan diagram; Planar/DC/angular illustrated. |
| <a id="s33"></a>**S33** | Yao Wang (NYU) — *Predictive & Block-Based Hybrid Video Coding* (lecture slides, PDF) | https://eeweb.engineering.nyu.edu/~yao/EL6123_s16/PredictiveVideocoding.pdf ✅ | Slide deck with the full encoder/decoder loop diagram + reconstruction feedback path. |
| <a id="s34"></a>**S34** | Wikipedia — *High Efficiency Video Coding (HEVC)* | https://en.wikipedia.org/wiki/High_Efficiency_Video_Coding ✅ | Quick factual reference: history, versions, profiles, adoption, licensing. |

---

### Maintenance notes
- **Before formal citation** (a real presentation bibliography or paper), resolve every ⚠️ DOI/URL against IEEE Xplore / the publisher / [S18](#s18). The ✅ links were opened during setup; the ⚠️ identifiers are from memory and may be slightly off.
- Add new sources at the **end** with the next ID (S27, S28, …) — never renumber existing IDs, or you'll break references in other files.
- When you actually read S3, S7, or S10, add your one-line takeaway in [papers/README.md](papers/README.md), and cite back here with the ID.
