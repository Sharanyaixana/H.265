# Paper Reading List

This list separates core video-coding literature from Professor Shreyas Sen's related hardware and sensing work. The second group is relevant by research method and hardware problem; it should not be described as HEVC research unless a paper actually implements or evaluates HEVC.

For each paper, record the problem, method, evaluation setup, strongest result, and one limitation.

## Core video coding

| Priority | Paper | Read for |
|---:|---|---|
| 1 | Sullivan et al., “Overview of the High Efficiency Video Coding Standard” [[S3]](../SOURCES.md#s3) | HEVC architecture, design goals, and coding tools |
| 2 | Ohm et al., “Comparison of the Coding Efficiency of Video Coding Standards Including HEVC” [[S4]](../SOURCES.md#s4) | Test methodology behind HEVC efficiency claims |
| 3 | Sullivan and Wiegand, “Rate-Distortion Optimization for Video Compression” [[S44]](../SOURCES.md#s44) | Lagrangian mode decisions and encoder tradeoffs |
| 4 | Bross et al., “Overview of the Versatile Video Coding Standard” [[S5]](../SOURCES.md#s5) | How VVC extends HEVC and increases complexity |
| 5 | Chen et al., “An Overview of Core Coding Tools in the AV1 Video Codec” [[S6]](../SOURCES.md#s6) | AV1's separate design and tool set |

Use *High Efficiency Video Coding Algorithms and Architectures* as a reference for transforms, entropy coding, in-loop filters, and implementation [[S7]](../SOURCES.md#s7).

## Professor Sen and SPARC Lab

| Priority | Work | Connection to this repository |
|---:|---|---|
| 1 | Li, Ghosh, and Sen, “Approximate DCT and Quantization Techniques for Energy-Constrained Image Sensors” [[S10]](../SOURCES.md#s10) | Directly relevant to transform and quantization energy; verify how its image-coding assumptions would transfer to an HEVC encoder |
| 2 | dAJC direct analog-to-MJPEG converter [[S11]](../SOURCES.md#s11) | Sensor-side compression and reduced data-conversion or movement cost; not an HEVC implementation |
| 3 | Time-domain direct-analog-to-MJPEG compression [[S12]](../SOURCES.md#s12) | Energy-per-pixel and hardware architecture methodology |
| 4 | Pixel-to-PWM system for body-worn video sensors [[S13]](../SOURCES.md#s13) | Energy-constrained sensing context |
| 5 | In-sensor time-domain convolution processor [[S14]](../SOURCES.md#s14) | Near-sensor computation and data-movement context |

Use the SPARC Lab publication page for the current bibliography and official manuscript links [[S15]](../SOURCES.md#s15).

## Reading record

For each completed paper, add:

```text
Problem:
Method:
Evaluation:
Main result:
Limitation or open question:
Relevance to HEVC or the proposed hardware system:
```

## Confidentiality

Keep standards-based literature notes separate from confidential lab or company ideas. Do not publish collaborative project directions or attribute them individually without approval.
