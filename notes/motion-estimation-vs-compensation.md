# Motion estimation vs. motion compensation?

> Chapter 2 · Day 5

## One-line answer
Motion **estimation** is the encoder-only *search* for the best-matching block in a reference frame (expensive, not defined by the standard); motion **compensation** is *applying* the resulting motion vector to form the prediction (cheap, done by both encoder and decoder, fully specified).

## Key points
- ME is the single most compute-heavy part of encoding → a prime target for hardware acceleration and fast-search heuristics. (Ties to Prof. Sen's lab [[S10]](../SOURCES.md#s10).)
- Scoring metric example: SAD (sum of absolute differences).
- The standard specifies *how MC uses an MV*, not *how to search* — that freedom is where research lives.

## Questions this raised
- (move unresolved ones to ../open-questions.md)
