# video-script-builder

A Claude Code skill for writing the pseudo-script of one episode in an educational video series,
section by section: objective, what the audience already saw, the minimum about the data, the
concepts in order, code cells that were actually run (with their real output), likely audience
questions, what to leave out, and the hand-off to the next episode.

Claude drafts one section at a time, and the presenter refines and approves it before the next one
is drafted. Before each section is closed, Claude asks the presenter the questions the audience is
most likely to ask, so nobody records a segment they can't defend on camera.

Sibling to [`repo-deep-dive`](https://github.com/juagudeloo/repo-deep-dive) (understand an existing
codebase) and [`build-to-learn`](https://github.com/juagudeloo/build-to-learn) (build something new
while learning it).

See [SKILL.md](SKILL.md) for the full methodology.

## Install

```bash
git clone git@github.com:juagudeloo/video-script-builder.git ~/.claude/skills/video-script-builder
```

## Use

Inside the series project (ideally one whose `CLAUDE.md` holds the syllabus, template and dataset
rules):

```
/video-script-builder
```

or "hagamos el pseudoguion del video N".

## Credits

The presenter-level scale is adapted from the dialogic-bootstrapping teaching methodology in
[SwRI-IDEA-Lab/vocal_prompt](https://github.com/SwRI-IDEA-Lab/vocal_prompt), the same source both
sibling skills credit.
