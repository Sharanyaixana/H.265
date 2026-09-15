# What are I, P, and B pictures, and what is a GOP?

> Learning sequence: Prediction · Sources: [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3) [[S28]](../SOURCES.md#s28)

## Short answer

I, P, and B describe the prediction capabilities of coded slices. Informally, people also call the corresponding coded pictures I, P, and B pictures. A **Group of Pictures (GOP)** is an encoder or application term for an arranged sequence of pictures and their prediction dependencies; it is not automatically an independently decodable segment.

## The three slice types

| Type | What it permits |
|---|---|
| **I — Intra** | Intra-coded blocks; no inter prediction from other pictures |
| **P — Predictive** | Intra coding or inter prediction using Reference Picture List 0 |
| **B — Bi-predictive** | Intra coding or inter prediction using List 0, List 1, or both |

A B picture is not simply “the average of the previous and next frame.” Each Prediction Unit can select its references, motion vectors, and whether one or two predictions are combined. B pictures can also be stored as references in hierarchical coding structures.

## Random access is a separate property

An intra-coded picture is not automatically a clean random-access point. HEVC defines Intra Random Access Point picture types, including:

- **IDR — Instantaneous Decoding Refresh:** prevents later pictures from referring to pictures before the refresh point.
- **CRA — Clean Random Access:** permits efficient random access with rules for leading pictures.

These details matter more than the informal word “keyframe.”

## GOP structure

A GOP description such as `I B B B P ...` summarizes coding order or display-order relationships chosen by the encoder. GOP design trades among:

- compression efficiency;
- random-access interval;
- encoder and decoder buffering;
- end-to-end latency;
- resilience to loss and segment boundaries.

Open and closed GOPs differ in whether prediction dependencies cross the chosen GOP boundary. A closed GOP is easier to treat as a self-contained access segment; an open GOP may gain efficiency through cross-boundary references.

## Display order and decoding order

When a displayed picture depends on another picture that appears later in display order, the reference must be decoded first. The bitstream therefore carries picture-order information, and decoding order can differ from display order.

## What to remember

I/P/B describes allowed prediction behavior. GOP describes a larger dependency pattern. Random-access behavior depends on the actual HEVC picture type and reference structure, not only the letter I.
