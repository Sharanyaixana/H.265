# What is the difference between interlaced and progressive scan?

> Learning sequence: Foundations · Sources: [[S1]](../SOURCES.md#s1) [[S27]](../SOURCES.md#s27)

## Short answer

Progressive video represents each displayed picture with all of its scan lines. Interlaced video divides a picture into two **fields** containing alternating lines, and the two fields are normally captured or displayed at different times.

## The distinction

| Property | Interlaced | Progressive |
|---|---|---|
| Basic temporal unit | Field containing alternating lines | Complete picture containing all lines |
| Capture times | Two fields normally represent different moments | One progressive picture represents one nominal moment |
| Motion artifact | Can produce combing when fields are combined incorrectly | Avoids inter-field combing |
| Modern display | Usually requires deinterlacing | Maps naturally to progressive displays |

A field is not a complete frame. Two complementary fields contain the rows needed to form a full-resolution frame, but because the fields are time-separated, simply weaving them together can create comb-shaped edges on moving objects.

## Why interlacing existed

Analog television could refresh alternate line sets more frequently without transmitting every line during every refresh. This reduced flicker and motion judder within a limited channel bandwidth.

## Why it matters for compression

Interlacing mixes spatial and temporal relationships: vertically adjacent lines may come from different moments. Motion estimation, prediction, scaling, and display therefore require field-aware handling. HEVC can represent field sequences, but it did not carry forward H.264/AVC's macroblock-adaptive and picture-adaptive frame/field coding tools [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3).

## Notation caution

Labels such as `1080i60` are not used consistently everywhere: the number may be described in fields per second rather than complete frames per second. State the field rate explicitly when precision matters.

## What to remember

Progressive means a complete picture per temporal sample. Interlaced means alternating-line fields captured at different times.
