# Presentation Feedback and Improvement Checklist

## Purpose

This file consolidates the feedback from the Continuum discussions associated with Presentations 0, 1, and 2. It is a working checklist for improving later presentations. The meeting transcripts record discussion and tentative explanations; they are not technical sources.

## Presentation 0 discussion on July 25 2026

### Direction established

- Begin with image compression before introducing video compression.
- Use a picture to explain block processing, spatial-frequency content, transform coding, quantization, zigzag scanning, and runs of zeros.
- Explain lossy compression as a controlled removal of information, not merely a smaller file.
- Make the transition from JPEG-style frame coding to video coding through temporal redundancy: much of a scene may remain unchanged between pictures.
- Frame H.265 as the next learning target after image compression and earlier video standards.
- Connect compression to hardware consequences. Lower bitrate alone is not the final research objective; the important systems question is whether computation and data movement can be reduced enough to save energy.
- Maintain regular discussions and send questions as they arise.

### Improvement test

- [ ] Can the motivation move naturally from raw data to image compression, temporal prediction, HEVC, and hardware energy?
- [ ] Are compression-ratio or power claims supported by a source and test conditions?
- [ ] Is the connection to Professor Sen's work stated as a research direction rather than an accomplished HEVC result?

## Presentation 1 discussion on August 17 2026

### Slide construction

- Put a correct slide number on every slide.
- Expand every abbreviation when it first appears.
- State what I, P, and B mean when introducing those labels.
- Use examples when prediction or decoding order is difficult to understand.
- Keep deferred topics explicit rather than giving an uncertain explanation in the moment.

### Technical explanation

- Clarify whether block sizes are fixed or variable and avoid reducing the H.264-to-HEVC difference to one inaccurate sentence.
- Explain what it means for an encoder to choose a diagonal intra mode; do not say only that it “matches.”
- Distinguish intra prediction from an I picture. Intra prediction is a block-level coding method; picture or slice type is a larger syntax classification.
- Explain P and B prediction with a consistent frame example.
- Separate display order from decoding order.
- Define Group of Pictures before using the abbreviation GOP.
- Explain what controls GOP length and the use of B pictures in terms of rate, latency, buffering, random access, and error propagation.
- State the intended sequence of future presentation topics.

### Improvement test

- [ ] Does every abbreviation have a full form on first use?
- [ ] Can a listener understand the prediction example without already knowing I/P/B terminology?
- [ ] Are reference lists described accurately rather than only as “past” and “future”?
- [ ] Are GOP design choices presented as encoder or application choices rather than fixed HEVC rules?

## Presentation 2 discussion on September 14 2026

### Delivery and visual guidance

- Use the pointer when explaining a multi-branch coding-loop diagram.
- Do not present fifty slides at the same pace as a short overview. Give the high-level story first and use detail only where it supports the argument.
- Keep slide numbering correct after adding, deleting, or merging slides.
- Keep full forms visible for Coding Tree Unit, Coding Unit, Prediction Unit, Transform Unit, Quantization Parameter, Context-based Adaptive Binary Arithmetic Coding, and Sample Adaptive Offset.

### Closed-loop reconstruction

- The word “drift” was not intuitive by itself. Explain the encoder-decoder reference mismatch that causes it.
- Show why the encoder must reconstruct the same lossy reference picture that the decoder will use.
- Do not claim that the closed loop removes ordinary quantization error. It prevents additional mismatch between encoder and decoder predictions.

### CTU CU PU and TU

- Verify block-size statements against the standard or a trusted HEVC reference.
- Explain why the hierarchy is divided into Coding Unit, Prediction Unit, and Transform Unit instead of treating the names as an explanation.
- For every structure, answer: what decision does it represent, what legal choices exist, and why is that choice useful?
- Explain why prediction geometry and transform geometry may differ.
- If a reason is not yet understood, state it as an open question.

### Transform and quantization

- Explain why the residual is transformed, not only that a transform is applied.
- Show energy compaction without implying that a transform automatically deletes high-frequency information.
- Explain Quantization Parameter as control information from which the effective quantization step is derived.
- Keep QP selection open until rate control, local QP signaling, and Rate Distortion Optimization have been separated clearly.

### CABAC

- The CABAC explanation was not fully digested by the audience and needs a slower progression.
- Explain syntax value, binarization, context selection, arithmetic coding, and decoding as separate stages.
- Explain why the decoder knows the active syntax element and its stopping rule.
- Do not invent a unary “number of positions to check” field.
- Do not present arbitrary probabilities or a hand-selected binary fraction as the actual compressed output of HEVC.
- If an arithmetic-coding toy example is retained, label it as an illustration and keep it separate from normative H.265 residual syntax.
- Build an actual residual-syntax trace from the standard or a named HM decoder version before calling the calculation an HEVC trace.

### Filtering

- Connect deblocking and Sample Adaptive Offset to the reconstruction branch, not to CABAC output.
- Explain which artifact or bias each filter addresses.
- Do not describe Sample Adaptive Offset as general smoothing.
- Show why in-loop filtering also affects future prediction references.

### RDO and encoder decisions

- Introduce Rate Distortion Optimization from the problem of comparing choices that trade quality against bits.
- Label all numerical RDO tables as illustrations unless they come from an instrumented encoder.
- Do not claim that every encoder exhaustively evaluates every candidate.
- Separate rate control and initial QP policy from local rate-distortion decisions.

### Central mentoring feedback

The presentation explained what the encoder does but did not consistently explain why the structure or operation exists. For each major transition, present the reasoning in this order:

1. What problem exists?
2. Why is the simpler alternative insufficient?
3. What choices are legal?
4. How does the encoder compare those choices?
5. What rate or quality benefit is expected?
6. What complexity, energy, memory, or latency cost is introduced?

Moving more slowly and answering these questions is preferable to covering more tools superficially.

## Recurring open questions

The maintained technical versions are in [open-questions.md](../open-questions.md):

- How is the Coding Unit split selected?
- How are Prediction Unit structure and prediction mode selected?
- Why is Transform Unit partitioning separate from Prediction Unit partitioning?
- How is Quantization Parameter selected at different control levels?
- How does H.265 residual syntax delimit and interpret CABAC bins?

## Checklist before the next presentation

- [ ] The first three slides establish the problem, scope, and high-level story.
- [ ] Every slide answers one main question.
- [ ] Motivation appears before mechanism.
- [ ] Every abbreviation is expanded on first use.
- [ ] Every factual or measured claim has a source.
- [ ] Every number is identified as measured, derived, or illustrative.
- [ ] Normative HEVC behavior and encoder implementation choices are distinguished.
- [ ] One visual example remains consistent across consecutive slides.
- [ ] Selected blocks, labels, colors, arrows, and image crops remain consistent.
- [ ] Open questions are stated honestly.
- [ ] Detailed material that interrupts the main story is moved to backup slides.
- [ ] Slide numbering and cross-references are checked after the final edit.
- [ ] The presentation fits the scheduled time when rehearsed aloud.
