# What is Rate–Distortion Optimization (RDO)?

> Chapter 5 · Day 10

## One-line answer
RDO is the encoder's decision engine: for every choice (split or not, which mode, which MV, which TU size) it minimizes a Lagrangian cost **J = D + λ·R**, where D is distortion, R is bits, and λ (tied to QP) sets the quality-vs-bits exchange rate.

## Key points
- Beginner framing: "for every choice, weigh quality lost + bits spent, pick the cheapest."
- RDO is **not in the standard** — it's the encoder's private strategy → why encoders differ and why speeding up/approximating RDO is an active research topic.
- The huge quadtree search space is why HEVC encoding is slow.

## List every place RDO makes a decision
- CU split? · intra mode? · inter vs intra? · which MV / merge candidate? · TU size? · ...

## Questions this raised
- (move unresolved ones to ../open-questions.md)
