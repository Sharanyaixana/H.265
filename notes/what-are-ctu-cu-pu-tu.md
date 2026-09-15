# What are CTU, CU, PU, and TU?

> Learning sequence: Coding loop · Sources: [[S1]](../SOURCES.md#s1) [[S3]](../SOURCES.md#s3) [[S7]](../SOURCES.md#s7) [[S35]](../SOURCES.md#s35)

## Short answer

The **Coding Tree Unit (CTU)** is the top-level coding region. Its Coding Tree Blocks can be recursively divided into **Coding Units (CUs)**. Each CU has a prediction partition represented by **Prediction Units (PUs)** and a transform partition represented by **Transform Units (TUs)**.

## The four questions

| Structure | Question it answers |
|---|---|
| CTU | Which top-level region of the picture is being processed? |
| CU | How should that region be recursively divided for coding decisions? |
| PU | How is a CU partitioned and described for prediction? |
| TU | How is the CU's residual partitioned for transform and coefficient coding? |

## Important accuracy details

- A CTU groups the corresponding luma and chroma Coding Tree Blocks with their syntax; it is not just one square luma image block [[S1]](../SOURCES.md#s1).
- In the commonly taught Main-profile configuration, luma CTBs can be as large as 64 × 64 and luma Coding Blocks can be as small as 8 × 8. Exact permitted sizes are signaled by sequence parameters and constrained by the profile.
- H.264/AVC used a 16 × 16 macroblock as its basic unit, but it also supported smaller prediction and transform partitions. HEVC's improvement is a larger and more flexible recursive coding-tree structure—not the invention of variable blocks from nothing.
- Prediction and transform partitions are separate because the shape that predicts a region well need not match the shape in which the remaining residual is efficiently transformed.

## Relationship

```text
CTU
└── one or more CUs from the coding quadtree
    ├── one or more PUs for prediction
    └── one or more TUs from the transform tree
```

## What to remember

CTU and CU organize coding decisions; PU describes prediction geometry; TU describes residual-transform geometry.

## Related open questions

- [Q2: CU selection](../open-questions.md#q2-how-does-an-hevc-encoder-select-the-cu-partition)
- [Q3: PU selection](../open-questions.md#q3-how-does-an-encoder-select-the-pu-partition-and-prediction-mode)
- [Q4: Why TU is separate](../open-questions.md#q4-why-does-hevc-separate-tu-from-pu)
