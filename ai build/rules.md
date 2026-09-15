# Repository and Writing Rules

## Purpose

These rules apply to humans and AI assistants working in this repository. They are intended to prevent unsupported claims, fabricated technical detail, repetitive writing, accidental loss of manual work, and confusion about where information belongs.

## Read before editing

Read these files in order:

1. [Project motivation](motivation.md)
2. [Presentation feedback](feedback.md)
3. [HEVC book memory](memory.md)
4. [Presentation discussion summary](../presentation/PRESENTATION_DISCUSSION_SUMMARY.md)
5. [Open questions](../open-questions.md)
6. The specific note, textbook chapter, or presentation being changed
7. The relevant entries in [SOURCES.md](../SOURCES.md)

Inspect the current file before editing it. Do not reconstruct a document or presentation from an older transcript when a newer artifact exists.

## Three evidence categories

Every technical statement must belong to one of these categories.

### Normative HEVC behavior

This is behavior defined by ITU-T H.265 or a technically aligned specification. Cite the standard or an authoritative standards paper. Use normative language only when the source supports it.

Examples:

- “The decoder derives...”
- “The syntax element specifies...”
- “HEVC permits...”

### Encoder implementation choice

This is a strategy used by HM, x265, hardware, or a research encoder. Name the implementation and version when possible. Do not write that “HEVC chooses” something when the standard leaves the choice to an encoder.

Examples:

- search order;
- candidate pruning;
- motion-estimation algorithm;
- rate-control policy;
- lambda formula;
- early termination.

### Teaching illustration

This is a simplified example created to explain an idea. Label it **Illustration** and state its assumptions. Do not call it an actual HEVC output.

Examples:

- a small coefficient matrix;
- an arithmetic-coding interval with invented probabilities;
- an RDO table with round numbers;
- a hand-drawn motion example.

## Prohibited content

Do not add:

- invented bitstream fields;
- arbitrary probabilities presented as CABAC states;
- fabricated encoder traces;
- performance percentages without a cited experiment;
- exact-looking numbers copied from an uncited explanation;
- claims that one codec is universally better than another;
- claims that a JPEG or MJPEG circuit directly implements HEVC without evidence;
- statements that every encoder uses the same RDO, QP, search, or rate-control algorithm;
- motivational filler, exaggerated praise, or slogans;
- repeated paragraphs that restate the same point without adding evidence;
- instructions such as “memorize this” in technical reference material;
- fake quotations written as if they came from a professor or paper.

If a claim cannot be verified, move it to [open-questions.md](../open-questions.md).

## Citation rules

- Add a new external source to [SOURCES.md](../SOURCES.md) before citing it elsewhere.
- Preserve existing source IDs. Add the next unused ID; never renumber earlier entries.
- From the repository root, cite `[[S3]](SOURCES.md#s3)`.
- From a subfolder, cite `[[S3]](../SOURCES.md#s3)`.
- Prefer sources in this order: standard, original paper, official implementation documentation, authoritative textbook, tutorial.
- Wikipedia and general tutorials may help orientation but should not support a disputed or research-level claim.
- Verify bibliographic details and links before using them in a formal presentation or paper.

For a performance claim, record:

- anchor and test implementation;
- versions and configurations;
- content and resolution;
- rate points;
- quality metric;
- speed or complexity constraint;
- aggregation method, such as BD-rate.

## Writing rules

- Begin with the problem or question.
- Give the short answer before the detailed explanation.
- Explain why a mechanism exists before describing its internal steps.
- Use direct sentences and established technical terms.
- Expand an abbreviation on first use in each independent document or presentation.
- Separate facts, interpretations, examples, and open questions.
- State assumptions next to equations and numerical results.
- Prefer one accurate example over several shallow examples.
- Remove sentences that only announce importance without explaining it.
- Avoid addressing the reader with claims about what they “already learned” or “must memorize.”
- Do not hide uncertainty behind confident wording.

## File responsibilities

### README.md

Navigation and repository purpose only. Do not place full technical chapters here.

### TEXTBOOK.md

Systematic explanations and connections between concepts. Keep the distinction between normative behavior, encoder choice, and illustration visible.

### notes

One question per file. Use question-based filenames. A note should contain a short answer, focused explanation, source citations, and a related open question only when one genuinely exists.

### SOURCES.md

Stable source catalogue. Include what each source is useful for and whether its bibliographic information has been verified.

### open-questions.md

Questions that still require a standard clause, paper, or implementation trace. Include current understanding and a concrete next verification step.

### papers

Prioritized reading list and paper-reading records. Separate HEVC literature from papers that are only methodologically related.

### presentation

Final presentation decks, their index, and durable presentation context. Keep temporary renders, generated previews, lock files, and intermediate versions outside the repository.

### ai build

Project motivation, presentation feedback, and operating rules for future AI assistance. Do not store generated drafts or temporary scripts here.

## Presentation rules

- Number every slide and verify numbering after structural edits.
- Put the full form beside an abbreviation on first use.
- Use one principal idea per slide.
- Establish the high-level story before detailed arithmetic.
- Put motivation before mechanism.
- Keep one running image or block consistent across a sequence.
- Preserve the selected region, crop, colors, and labels across related slides.
- Use consistent thin connector lines and arrowheads.
- Do not allow arrows to cross unrelated objects or obscure the image.
- Label a number as measured, derived, or illustrative.
- Put detailed calculations in sequential slides or backup material.
- Rehearse to the scheduled duration; slide count alone does not determine timing.

## Editing workflow

1. Inspect the current files and repository status.
2. Preserve manual changes and unrelated work.
3. Identify the exact technical question being changed.
4. Check the strongest available source.
5. Make the smallest coherent edit that resolves the issue.
6. Update citations and open questions.
7. Check links, source IDs, headings, and terminology.
8. For PowerPoint, render and inspect every slide after editing.
9. Report what changed and what remains unresolved.

Do not restore a deleted slide or section unless the current user explicitly requests it.

## Definition of done

A change is complete only when:

- the technical statement is accurate at the claimed level;
- normative and encoder-specific behavior are distinguished;
- illustrative numbers are labeled;
- sources resolve;
- links are not broken;
- abbreviations are expanded appropriately;
- no useful manual work was overwritten;
- presentation numbering and visual consistency were checked when applicable;
- unresolved questions remain visible rather than being answered speculatively;
- confidential project information has not been moved into a public location.
