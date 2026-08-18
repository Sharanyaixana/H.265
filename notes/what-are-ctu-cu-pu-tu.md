# What are CTU, CU, PU, and TU?

> Chapter 4 · Day 8

## One-line answer
HEVC's signature structure: a Coding Tree Unit (up to 64×64) is recursively quadtree-split into Coding Units (down to 8×8, each choosing intra/inter); each CU holds Prediction Units (prediction info) and, via a residual quadtree, Transform Units (the transform blocks).

## Mental model
- CTU = the plot of land · CU = how you subdivide it · PU = how you predict each parcel · TU = how you transform the leftover error.

## Key points
- Replaces H.264's fixed 16×16 macroblock → big CUs for flat 4K regions, small CUs for detail.
- This flexible partitioning is a major source of HEVC's coding gain.
- Transform size (TU) can differ from prediction size (PU).

## Diagram to draw (put this in the slides)
- A CTU recursively split into CUs, then PUs and TUs.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
