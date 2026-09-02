# H.265 / HEVC — Knowledge Base

A personal knowledge base for learning **H.265 (HEVC)** video compression from the ground up. This README explains what's in the repo and how to navigate it.

## What's here

| File / folder | What it is |
|---|---|
| **[TEXTBOOK.md](TEXTBOOK.md)** | Full 23-chapter textbook covering everything behind Presentations 1 & 2 plus quality metrics, bitstream structure, and critical paper-reading skills — history, math, one running worked example threaded through the whole coding loop, quizzes with an answer key, and a chapter-by-chapter reading/video list. The deep-study reference. |
| **[notes/](notes/README.md)** | The heart of this repo — one short page per concept, each named after the question it answers. See **[notes/README.md](notes/README.md)** for the full index, grouped into chapters. |
| **[presentation/](presentation/)** | The actual slide decks (`1_introduction.pptx`, `2_inside_hevc_coding_loop.pptx` …) and a [slide-by-slide study guide](presentation/2-study-guide.md) for deck 2. |
| **[SOURCES.md](SOURCES.md)** | Master index of every source (S1–S50) with links. Cited across the repo as `[[S3]](SOURCES.md#s3)`. |
| **[papers/](papers/README.md)** | Curated reading list — core video-coding papers + Prof. Sen's (SPARC Lab) papers. |
| **[open-questions.md](open-questions.md)** | Running list of unresolved questions to revisit. |

## How to navigate

- **Want the deep, textbook-level understanding?** Read **[TEXTBOOK.md](TEXTBOOK.md)** front to back — 20 chapters, quizzes, worked examples, and a full reading/video list per chapter.
- **Learning a concept fast?** Open the notes index at **[notes/README.md](notes/README.md)** and pick the question — each note is a self-contained one-pager.
- **Preparing / giving a talk?** The slide decks live in **[presentation/](presentation/)**; deck 2 has a matching [study guide](presentation/2-study-guide.md) to learn every slide properly.
- **Need a source or citation?** Everything is indexed in [SOURCES.md](SOURCES.md).
- **Stuck on something?** Add it to [open-questions.md](open-questions.md) and come back to it.

## Repo structure

```
H.265/
├── README.md          ← you are here (repo guide)
├── TEXTBOOK.md         ← full 20-chapter textbook (deep study)
├── notes/             ← one page per concept
│   └── README.md      ← notes index (start here to learn)
├── presentation/      ← slide decks (1_introduction.pptx …)
├── SOURCES.md         ← master link index (S1–S50)
├── papers/            ← reading list
└── open-questions.md  ← unresolved questions
```

## Conventions

- **Notes** are named after the question they answer (e.g. `what-are-i-p-and-b-frames-and-gop.md`). The index is at [notes/README.md](notes/README.md).
- **Sources** are cited inline as `[[S3]](SOURCES.md#s3)`; new sources are appended to [SOURCES.md](SOURCES.md) with the next ID (never renumber existing ones).
