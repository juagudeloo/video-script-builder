# CLAUDE.md

This repository is a single Claude Code **skill package** (`video-script-builder`), not an
application. `SKILL.md` is the deliverable; `README.md` is for humans. No build or test tooling.

## Sibling to repo-deep-dive and build-to-learn

It shares their calibration scale, incremental disclosure, index document and findings document
almost verbatim. That duplication is intentional, since skills are self-contained. The gate is
adapted: instead of testing whether the reader can build the code (build-to-learn), it rehearses the
audience's likely questions with the presenter, and a gap in the dataset's *domain* is treated as a
signal to simplify the data, not to make the presenter study more.

## The hard rule

`SKILL.md` must stay generic across video series. Never let a specific series' topic, library,
dataset, presenters or dates leak into `SKILL.md`, `README.md`, or this file. Project specifics
live in the series project's own `CLAUDE.md`, which the skill reads first.

## Conventions to preserve

- The project's own template and rules override the skill's defaults.
- One section at a time, explicit approval before the next one. Code cells are approved per
  concept, not as one block.
- Every cell is run top to bottom in a fresh process in the project's environment. Real output only.
  A runnable companion file accompanies each pseudo-script.
- Cells use only what the audience has already seen, checked against the syllabus.
- Spanish and English header variants stay structurally in sync; never mix them in one document.
