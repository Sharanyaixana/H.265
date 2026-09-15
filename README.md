# H.265 / HEVC Knowledge Base

A study and presentation repository for **H.265 / High Efficiency Video Coding (HEVC)**. It separates explanatory material, short revision notes, primary references, unresolved questions, papers, and presentation artifacts.

## Start here

Choose the resource that matches your immediate goal:

| Goal | Resource |
|---|---|
| Learn the subject systematically | [TEXTBOOK.md](TEXTBOOK.md) |
| Review one concept quickly | [Notes index](notes/README.md) |
| Prepare or deliver a talk | [Presentation index](presentation/README.md) |
| Find a paper, standard, tutorial, or implementation | [SOURCES.md](SOURCES.md) |
| Follow the prioritized paper-reading list | [Papers to read](papers/README.md) |
| Track unresolved questions | [Open questions](open-questions.md) |
| Follow the current study schedule | [Study log](log/README.md) |
| Recover the HEVC book's technical conclusions | [HEVC book memory](ai%20build/memory.md) |
| Understand the project's purpose and working standards | [AI build guidance](ai%20build/motivation.md) |

## Repository structure

```text
H.265/
├── README.md
├── TEXTBOOK.md
├── SOURCES.md
├── open-questions.md
├── log/
│   └── README.md
├── ai build/
│   ├── feedback.md
│   ├── memory.md
│   ├── motivation.md
│   └── rules.md
├── notes/
│   ├── README.md
│   └── one question-focused note per concept
├── papers/
│   ├── README.md
│   └── High_Efficiency_Video_Coding_HEVC.pdf
└── presentation/
    ├── README.md
    ├── PRESENTATION_DISCUSSION_SUMMARY.md
    ├── Presentation 1 - Introduction to HEVC.pptx
    └── Presentation 2 - Inside the HEVC Coding Loop.pptx
```

## Main learning resources

### Textbook

[TEXTBOOK.md](TEXTBOOK.md) is the main study reference. Its 23 chapters cover video representation, prediction, the HEVC coding loop, encoder decisions, quality measurement, bitstream structure, implementation, and research framing. Simplified examples are labeled as illustrations and are not presented as traces of normative HEVC syntax.

### Notes

[notes/README.md](notes/README.md) organizes short notes into a learning sequence. Each file answers one question and uses a descriptive filename, such as `what-is-quantization-and-qp.md`.

### Presentations

[presentation/README.md](presentation/README.md) indexes the presentation series. [PRESENTATION_DISCUSSION_SUMMARY.md](presentation/PRESENTATION_DISCUSSION_SUMMARY.md) records the feedback and unresolved issues that should guide future revisions.

- [Presentation 1 - Introduction to HEVC](presentation/Presentation%201%20-%20Introduction%20to%20HEVC.pptx)
- [Presentation 2 - Inside the HEVC Coding Loop](presentation/Presentation%202%20-%20Inside%20the%20HEVC%20Coding%20Loop.pptx)

### Sources and papers

[SOURCES.md](SOURCES.md) is the stable source catalogue. References use IDs such as `S3`; do not renumber existing IDs. [papers/README.md](papers/README.md) turns that catalogue into a prioritized reading list, including papers relevant to Professor Shreyas Sen's SPARC Lab.

### AI build guidance

The `ai build/` folder is the operating brief for any AI assistant working on this repository:

- [motivation.md](ai%20build/motivation.md) explains why the project exists and how H.265 connects to the longer-term research objective.
- [feedback.md](ai%20build/feedback.md) converts feedback from Presentations 0–2 into concrete improvement checks.
- [memory.md](ai%20build/memory.md) preserves the important technical conclusions, implementation lessons, quantitative results, and research questions from the repository's HEVC textbook.
- [rules.md](ai%20build/rules.md) defines evidence standards, writing quality, file placement, slide conventions, and confidentiality boundaries.

## Repository conventions

- Keep one focused question per note.
- Add every new external source to [SOURCES.md](SOURCES.md) before citing it elsewhere.
- Cite sources as `[[S3]](SOURCES.md#s3)` from root files or `[[S3]](../SOURCES.md#s3)` from subfolders.
- Distinguish normative HEVC behavior, encoder implementation choices, and teaching illustrations.
- Add unresolved technical questions to [open-questions.md](open-questions.md), including the current understanding and the next verification step.
- Store final slide decks in `presentation/`; do not commit rendered previews or temporary PowerPoint files.
