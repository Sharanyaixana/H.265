# Study Guide — Presentation 2: "Inside the HEVC Coding Loop"

Goal: understand **every slide well enough to explain it in your own words**, not read it off the screen. Each section below covers one slide: what you're saying, the fuller explanation behind it, the repo note that backs it up, and external links to go deeper.

**How to use this:** for each slide, read the explanation, then close it and try to say the "what you're saying" line from memory in your own words. If you can't, re-read and try again. Move anything still unclear into [../open-questions.md](../open-questions.md).

---

## Slide 1 — Title: "Inside the High Efficiency Video Coding (HEVC) loop"

**What you're saying:** the whole talk is one journey — Partition → Predict → Transform → Quantize → Code → Reconstruct — and today you're following one block through all six steps.

**Explain it:** this is the same hybrid pipeline from talk 1, just with the internals turned on. Every codec since MPEG-2 uses this loop; HEVC's contribution is *how* each step is done (bigger flexible blocks, more prediction modes, CABAC-only entropy coding, a new SAO filter). Framing the whole talk as "one block's journey" gives the audience a thread to hold onto through 18 slides of internals.

**Repo note:** [what-is-the-hybrid-codec-pipeline.md](../notes/what-is-the-hybrid-codec-pipeline.md)
**Links:** [[S3]](../SOURCES.md#s3) Sullivan et al., HEVC overview (the authoritative source for the whole deck) · [[S33]](../SOURCES.md#s33) Yao Wang (NYU) — pipeline diagram slides

---

## Slide 2 — "Prediction reduces repetition—but it does not finish compression"

**What you're saying:** prediction removes *repetition* (spatial/temporal redundancy) but what's left over — the **residual** — still has to be turned into bits. Four questions this raises, which the rest of the talk answers: (1) how large should each coding block be, (2) how is the residual represented compactly, (3) how does the encoder choose among many options, (4) why must the encoder reconstruct its own output.

**Explain it:** this is the pivot slide — talk 1 stopped at "predict, then store the difference." This talk continues: prediction alone only gets you partway; you still need to encode the residual efficiently (transform+quantize+CABAC), decide *how* to partition/predict (RDO), and keep the encoder and decoder in sync (reconstruction). The four questions map directly onto slides 4–9 (block size), 11–13 (residual representation), 16 (encoder choice/RDO), and 14 (why reconstruct).

**Repo note:** [what-is-the-hybrid-codec-pipeline.md](../notes/what-is-the-hybrid-codec-pipeline.md) (residual section) · [what-are-the-four-types-of-redundancy.md](../notes/what-are-the-four-types-of-redundancy.md)
**Links:** [[S27]](../SOURCES.md#s27) Digital Video Introduction · [[S3]](../SOURCES.md#s3) HEVC overview

---

## Slide 3 — "The bitstream path and reconstruction path form one closed loop"

**What you're saying:** the encoder has two paths — the **forward/bitstream path** (Partition → Predict → Residual → Transform → Quantize → Entropy code → BITSTREAM) and the **reconstruction path** (inverse quantize → inverse transform → add back the prediction → deblock + SAO → stored as a reference picture for future prediction). They form one closed loop.

**Explain it:** this is **the single most important idea in the whole deck** — the encoder contains a decoder inside it. It must predict from *reconstructed* (slightly degraded) frames, exactly what the real decoder will have, or the two would drift apart over time. Everything from slide 4 onward is detail on individual boxes in this diagram — keep returning to it as your anchor.

**Repo note:** [what-is-the-hybrid-codec-pipeline.md](../notes/what-is-the-hybrid-codec-pipeline.md) — this note *is* this slide
**Links:** [[S33]](../SOURCES.md#s33) Yao Wang (NYU) full loop diagram · [[S3]](../SOURCES.md#s3) HEVC overview

---

## Slide 4 — "One block size cannot represent every part of an image efficiently"

**What you're saying:** a smooth region (sky), an object boundary (edge), and fine texture (detail) each need a *different* block size to code efficiently — one fixed size wastes bits somewhere.

**Explain it:** this motivates *why* HEVC uses a flexible quadtree instead of H.264's fixed 16×16 macroblock. Large blocks are cheap (few partition flags, one prediction/residual for a big smooth area); small blocks are precise but cost more signaling. The encoder's job is choosing the right size *per region* — which is exactly what CTU/CU partitioning (next 5 slides) does.

**Repo note:** [what-are-ctu-cu-pu-tu.md](../notes/what-are-ctu-cu-pu-tu.md)
**Links:** [[S35]](../SOURCES.md#s35) vcodex — HEVC Introduction (CTU/CU partitioning)

---

## Slide 5 — "Coding Tree Units are the largest HEVC workspaces"

**What you're saying:** a picture is tiled into CTUs (Coding Tree Units), each up to **64×64 luma samples**. Each CTU has one luma coding tree and matching chroma blocks; the encoder can keep it large or split it. CTU size gives HEVC flexibility and parallel-processing headroom.

**Explain it:** the CTU is HEVC's answer to H.264's macroblock — but 16× bigger in area (64×64 vs 16×16). Think of it as "the encoder's current workspace": everything downstream (CU/PU/TU) happens *inside* one CTU at a time.

**Repo note:** [what-are-ctu-cu-pu-tu.md](../notes/what-are-ctu-cu-pu-tu.md)
**Links:** [[S35]](../SOURCES.md#s35) vcodex — HEVC Introduction · [[S3]](../SOURCES.md#s3) HEVC overview (§ CTU)

---

## Slide 6 — "The Coding Unit (CU) quadtree splits only where more detail is useful"

**What you're saying:** at each node, the encoder asks: keep this CU as one region, or split it into four smaller CUs? Large CU for a smooth region, smaller/deeper-split CUs for a complex region. The split pattern itself has to be signaled in the bitstream (it costs bits too).

**Explain it:** this is **recursive quadtree partitioning** — CTU (64×64) splits into CUs down to 8×8. Each CU is where the encoder decides intra vs. inter. The "should I split further" decision at every node is itself an RDO decision (slide 16) — more detail is only worth the extra split-flag bits if it actually saves residual bits.

**Repo note:** [what-are-ctu-cu-pu-tu.md](../notes/what-are-ctu-cu-pu-tu.md)
**Links:** [[S35]](../SOURCES.md#s35) vcodex — HEVC Introduction

---

## Slide 7 — "Prediction Units (PUs) define how Coding Units (CUs) are predicted"

**What you're saying:** a CU is predicted using one or more PU shapes — **2N×2N, 2N×N, N×2N, or N×N**. Intra PUs carry "which spatial mode"; inter PUs carry "which reference frame + motion vector." PU boundaries group samples that share the same prediction information.

**Explain it:** the CU decides *whether* to split for prediction purposes; the PU shape decides *how* — e.g. a 2N×N split lets the top and bottom halves of a CU use different motion vectors (useful when an object boundary crosses horizontally through the block). This is a second, independent layer of flexibility on top of the CU quadtree.

**Repo note:** [what-are-ctu-cu-pu-tu.md](../notes/what-are-ctu-cu-pu-tu.md) · [how-does-intra-prediction-work.md](../notes/how-does-intra-prediction-work.md) · [how-does-inter-prediction-work.md](../notes/how-does-inter-prediction-work.md)
**Links:** [[S35]](../SOURCES.md#s35) vcodex — HEVC Introduction · [[S31]](../SOURCES.md#s31) vcodex — intra prediction · [[S3]](../SOURCES.md#s3) HEVC overview

---

## Slide 8 — "TU partitioning can differ from prediction partitioning"

**What you're saying:** the **PU partition** (how the CU is predicted) and the **TU partition** (how the residual is transformed) are drawn independently — a large TU where the residual is simple, smaller TUs where it's complex. Prediction geometry and transform geometry solve different problems.

**Explain it:** this trips people up — PU and TU are *not* the same tree. The Residual Quad-Tree (RQT) splits a CU's residual into TUs (4×4 up to 32×32) independently of how the PU divided the prediction. Why separate them? Because "the best way to predict this area" and "the best way to transform its error" are genuinely different questions — a CU predicted as one 2N×2N block can still have a residual that's spatially uneven and benefits from smaller TUs in the busy part.

**Repo note:** [what-are-ctu-cu-pu-tu.md](../notes/what-are-ctu-cu-pu-tu.md)
**Links:** [[S35]](../SOURCES.md#s35) vcodex — HEVC Introduction · [[S3]](../SOURCES.md#s3) HEVC overview (§ RQT)

---

## Slide 9 — "The four coding units answer four different questions"

**What you're saying:** CTU = *where is the encoder working?* (the plot of land). CU = *should this region split?* (how the plot is divided). PU = *how is it predicted?* (prediction geometry). TU = *how is the residual transformed?* (residual geometry).

**Explain it:** this is the summary slide for the whole CTU→CU→PU→TU arc (slides 4–8). It's the cleanest way to keep the four letters straight under Q&A pressure: **one workspace (CTU), one decision node (CU), one prediction shape (PU), one transform shape (TU)** — and PU/TU are independent of each other, both living inside a CU.

**Repo note:** [what-are-ctu-cu-pu-tu.md](../notes/what-are-ctu-cu-pu-tu.md) — this note's "mental model" section is this slide, nearly verbatim
**Links:** [[S35]](../SOURCES.md#s35) vcodex — HEVC Introduction

---

## Slide 10 — "One CTU can adapt to smooth and detailed regions"

**What you're saying:** within a single CTU, the encoder evaluates: does a smaller CU split improve prediction enough to be worth it? Which PU choice (intra mode or motion model) best predicts each area? Where does the residual need a smaller transform? Are the extra split flags worth transmitting (the bit cost)?

**Explain it:** this slide makes concrete what "the encoder decides everything by weighing cost vs. benefit" means — it's a preview of RDO (slide 16) applied specifically to partitioning. Every "should I split further" question here is really "does the quality gain from splitting outweigh the extra signaling bits?"

**Repo note:** [what-are-ctu-cu-pu-tu.md](../notes/what-are-ctu-cu-pu-tu.md) · [what-is-rate-distortion-optimization.md](../notes/what-is-rate-distortion-optimization.md)
**Links:** [[S35]](../SOURCES.md#s35) vcodex — HEVC Introduction · [[S41]](../SOURCES.md#s41) Wikipedia — RDO

---

## Slide 11 — "The transform concentrates residual energy into fewer coefficients"

**What you're saying:** a correlated residual block (nearby pixels have similar error values) gets transformed so that most of the energy moves into a few low-frequency positions. **Transform coding reorganizes information; it does not yet discard it.**

**Explain it:** the transform (an integer approximation of the DCT, sizes 4×4–32×32) exploits the fact that residual errors are spatially smooth, not random — so most of the "information" ends up concentrated in a handful of coefficients while the rest are near-zero. This step is (nearly) lossless by itself; it just sets up quantization (next slide) to discard the near-zero parts cheaply.

**Repo note:** [what-is-transform-coding-and-why-dct.md](../notes/what-is-transform-coding-and-why-dct.md)
**Links:** [[S38]](../SOURCES.md#s38) vcodex — 4×4 Transform and Quantization · [[S30]](../SOURCES.md#s30) Reducible — visual DCT intuition · [[S3]](../SOURCES.md#s3) HEVC overview

---

## Slide 12 — "Quantization creates zeros and sets the rate–quality trade-off"

**What you're saying:** transform coefficients are divided by a step size and rounded. **Lower QP** → more coefficients survive → more bits, less loss. **Higher QP** → more coefficients round to zero → fewer bits, more loss.

**Explain it:** this is the one deliberately lossy step in the whole pipeline. Everything before it (prediction, transform) and after it (entropy coding) is essentially lossless — quantization is where you're actually trading quality for file size, and QP (or CRF in x265) is the dial. This is worth stating explicitly in the talk: *"if someone asks where the quality loss actually happens, the answer is here."*

**Repo note:** [what-is-quantization-and-qp.md](../notes/what-is-quantization-and-qp.md)
**Links:** [[S38]](../SOURCES.md#s38) vcodex — Transform and Quantization · [[S10]](../SOURCES.md#s10) Prof. Sen's approximate-quantization paper (this exact block, optimized for energy)

---

## Slide 13 — "CABAC packs the syntax losslessly"

**What you're saying:** CABAC has three stages — **binarize** (split flags, motion index, coefficients → binary "bins"), **model context** (estimate each bin's probability from context, e.g. P(0)=0.86, P(1)=0.14), **arithmetic-code** (pack into a compact bitstream where frequent symbols cost less than a full bit).

**Explain it:** this is the final, lossless squeeze — after quantization has zeroed out most coefficients, CABAC exploits the fact that some symbols (zero, common split patterns) are far more frequent than others. The tradeoff: it's inherently **serial** (each bin's probability depends on prior context), which is why parallel tools (Tiles, WPP — beyond this talk's scope) exist to help hardware decode it fast.

**Repo note:** [what-is-cabac-entropy-coding.md](../notes/what-is-cabac-entropy-coding.md)
**Links:** [[S36]](../SOURCES.md#s36) vcodex — CABAC deep dive · [[S37]](../SOURCES.md#s37) Wikipedia — CABAC · [[S3]](../SOURCES.md#s3) HEVC overview

---

## Slide 14 — "The encoder reconstructs locally to prevent prediction drift"

**What you're saying:** quantized coefficients → inverse quantize → inverse transform → add back prediction → rebuilt block. **Encoder reference = decoder reference**, because both do the same inverse operations from the same prediction base. If references differ, the error propagates through future inter prediction.

**Explain it:** this is slide 3's insight made explicit and answers unresolved question #4 from slide 2 ("why must the encoder reconstruct its own output?"). If the encoder predicted future frames from the *original* pixels instead of the *reconstructed* ones, it would be predicting from information the decoder doesn't have — errors would compound frame after frame. Reconstructing locally guarantees encoder and decoder never disagree about what a reference frame looks like.

**Repo note:** [what-is-the-hybrid-codec-pipeline.md](../notes/what-is-the-hybrid-codec-pipeline.md) — "the insight I must be able to explain" section
**Links:** [[S33]](../SOURCES.md#s33) Yao Wang (NYU) · [[S3]](../SOURCES.md#s3) HEVC overview

---

## Slide 15 — "Deblocking and Sample Adaptive Offset (SAO) improve the next reference"

**What you're saying:** two filters run **in order**: (1) **deblocking** — reduces visible discontinuities across block edges; (2) **SAO** (new in HEVC) — adds small signaled offsets to sample classes to reduce systematic error (ringing/banding). Order: deblocking → SAO → reference memory.

**Explain it:** both are **in-loop** filters — applied before a frame is stored as a reference, not just before display — so a cleaner reference also shrinks *future* residuals, not only today's picture quality. Deblocking targets block-edge artifacts from coarse quantization; SAO (Band Offset for intensity-range errors, Edge Offset for edge-shape errors) is HEVC's new addition on top of what H.264 already had.

**Repo note:** [what-are-deblocking-and-sao-filters.md](../notes/what-are-deblocking-and-sao-filters.md)
**Links:** [[S39]](../SOURCES.md#s39) vcodex — H.264 loop filter (deblocking concept) · [[S40]](../SOURCES.md#s40) Wikipedia — deblocking filter · [[S3]](../SOURCES.md#s3) HEVC overview (SAO is HEVC-specific — this paper is the best source for it)

---

## Slide 16 — "Rate–Distortion Optimization (RDO) balances quality against bit cost"

**What you're saying:** `J = D + λR` — total cost = distortion + λ×rate. Three example choices: (A) large CU + simple mode: D=18, R=6, J=30. (B) smaller CUs + better prediction: D=8, R=10, J=28. (C) deep split + many flags: D=5, R=16, J=37. **RDO picks the lowest-cost journey — option B.**

**Explain it:** this is the decision engine behind *every* choice you saw in slides 6–10 (CU split? which PU shape? which TU size?). λ is tied to QP and sets the exchange rate between "how much quality am I losing" and "how many bits am I spending." Walk through the worked example live: with λ=2, J = D + 2R for each option — A: 18+12=30, B: 8+20=28, C: 5+32=37 — so B wins even though it's not the cheapest in bits (R=10) or the least distorted (C has D=5) individually; it's the best *combined* cost. That's the whole idea of RDO in one arithmetic check.

**Repo note:** [what-is-rate-distortion-optimization.md](../notes/what-is-rate-distortion-optimization.md)
**Links:** [[S41]](../SOURCES.md#s41) Wikipedia — RDO · [[S3]](../SOURCES.md#s3) HEVC overview

---

## Slide 17 — "One block's journey through the HEVC encoder"

**What you're saying:** the full seven-step walkthrough for one block — Partition → Predict → Subtract → Transform → Quantize → Entropy code → Reconstruct — with the feedback loop back into future prediction. **RDO keeps the lowest-cost journey.**

**Explain it:** this is the payoff slide — it re-runs slide 1's overview, but now every box has a concrete meaning because you've explained each one. Use this slide to narrate out loud, end to end, in under 2 minutes: *"the encoder picks a CTU, decides how to split and predict it, subtracts the prediction to get a residual, transforms and quantizes that residual, entropy-codes everything into the bitstream — and, in parallel, reconstructs its own output so the next block's prediction stays honest."* If you can say that fluently, you understand the deck.

**Repo note:** synthesis of [what-is-the-hybrid-codec-pipeline.md](../notes/what-is-the-hybrid-codec-pipeline.md) + [what-are-ctu-cu-pu-tu.md](../notes/what-are-ctu-cu-pu-tu.md) + [what-is-rate-distortion-optimization.md](../notes/what-is-rate-distortion-optimization.md)
**Links:** [[S33]](../SOURCES.md#s33) Yao Wang (NYU) · [[S27]](../SOURCES.md#s27) Digital Video Introduction

---

## Slide 18 — "HEVC expands the encoder's decision space"

**What you're saying:** four closing points — (1) partition adaptively (CTU/CU place detail where useful), (2) code the residual (PU predicts, TU transforms, quantization creates zeros), (3) stay decoder-consistent (inverse operations + in-loop filters prevent drift), (4) optimize the full choice (RDO balances distortion against bitrate). **Next research question: how do we search this space in real time with less energy?**

**Explain it:** this is your bridge to the research angle. HEVC's coding gain over H.264 comes almost entirely from giving the encoder a *much bigger space of choices* (partition shapes, prediction modes, transform sizes) — but searching that space with RDO is expensive, which is exactly why fast/approximate search and hardware acceleration are active research areas. This closing question connects directly to Prof. Sen's SPARC Lab work on approximate, energy-efficient transform/quantization hardware.

**Repo note:** [where-is-h265-research-heading.md](../notes/where-is-h265-research-heading.md)
**Links:** [[S10]](../SOURCES.md#s10) Prof. Sen — approximate DCT/quantization paper · [[S16]](../SOURCES.md#s16) SPARC Lab home

---

## Quick self-check

Before presenting, you should be able to, without notes:
- [ ] Redraw the closed-loop diagram (slide 3) and explain why the encoder reconstructs its own output (slide 14).
- [ ] Explain CTU → CU → PU → TU using the "four questions" framing (slide 9).
- [ ] Say, in order, what happens to a residual: transform → quantize → CABAC (slides 11–13), and which single step is lossy.
- [ ] Walk through the RDO worked example (slide 16) and get the arithmetic right live if asked.
- [ ] Explain deblocking vs. SAO and why both are "in-loop" (slide 15).
- [ ] Answer "what's next research-wise?" by connecting slide 18's question to Prof. Sen's lab.
