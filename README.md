# H.265 / HEVC — Knowledge Base

A personal knowledge base for learning **H.265 (HEVC)** video compression from the ground up. This README explains what's in the repo and how to navigate it.

## What's here

| File / folder | What it is |
|---|---|
| **[notes/](notes/README.md)** | The heart of this repo — one short page per concept, each named after the question it answers. See **[notes/README.md](notes/README.md)** for the full index, grouped into chapters. |
| **[presentation/](presentation/)** | The actual slide decks (`1_introduction.pptx`, `2_inside_hevc_coding_loop.pptx` …). |
| **[SOURCES.md](SOURCES.md)** | Master index of every source (S1–S34) with links. Cited across the repo as `[[S3]](SOURCES.md#s3)`. |
| **[papers/](papers/README.md)** | Curated reading list — core video-coding papers + Prof. Sen's (SPARC Lab) papers. |
| **[open-questions.md](open-questions.md)** | Running list of unresolved questions to revisit. |

## How to navigate

- **Learning a concept?** Open the notes index at **[notes/README.md](notes/README.md)** and pick the question — each note is a self-contained one-pager.
- **Preparing / giving a talk?** The slide decks live in **[presentation/](presentation/)**.
- **Need a source or citation?** Everything is indexed in [SOURCES.md](SOURCES.md).
- **Stuck on something?** Add it to [open-questions.md](open-questions.md) and come back to it.

## Repo structure

```
H.265/
├── README.md          ← you are here (repo guide)
├── notes/             ← one page per concept
│   └── README.md      ← notes index (start here to learn)
├── presentation/      ← slide decks (1_introduction.pptx …)
├── SOURCES.md         ← master link index (S1–S34)
├── papers/            ← reading list
└── open-questions.md  ← unresolved questions
```

## Conventions

- **Notes** are named after the question they answer (e.g. `what-are-i-p-and-b-frames-and-gop.md`). The index is at [notes/README.md](notes/README.md).
- **Sources** are cited inline as `[[S3]](SOURCES.md#s3)`; new sources are appended to [SOURCES.md](SOURCES.md) with the next ID (never renumber existing ones).
