---
name: video-script-builder
description: Section-by-section methodology for writing the pseudo-script (pseudoguion) of one episode in an educational video series — objective, what the audience already saw, the minimum the presenter must know about the data, the concepts in order, complete code cells run for real with their actual output pasted in, likely audience questions, what to leave out, and the hand-off to the next episode. Claude drafts one section at a time, the presenter refines and approves it, and only then does the next section get drafted. Before a section is approved, Claude asks the presenter the questions the audience is most likely to ask about it, so nobody records a segment they can't defend on camera. Sibling to repo-deep-dive (understand an existing codebase) and build-to-learn (build something new while learning it), but for teaching something to an audience on video. Use when the user says things like "hagamos el pseudoguion del video N", "preparemos el guion del siguiente video", "armemos el script de este episodio sección por sección", "let's write the script for episode N", or otherwise wants to build a video script incrementally with verified code.
---

# Video Script Builder

A method for producing the pseudo-script of one episode in an educational video series, one section
at a time, so that every line of code shown on camera has actually been run, every claim fits the
audience's level, and the presenter can answer what the audience will ask.

It shares its mechanics with two sibling skills (neither one needs to be installed for this one to
work):

- From `repo-deep-dive`: calibration, incremental disclosure (only the active section is filled
  in), an index document as the source of truth, and a running findings document.
- From `build-to-learn`: a gate before each section is closed, and the rule that code is
  smoke-tested, never hand-computed.

What's different here is the reader. The output is for a **presenter** who has to explain it live
to an **audience** they can't see. So the gate doesn't test whether the reader understands the code
well enough to write it. It tests whether the presenter can survive the audience's questions about
it.

A pseudo-script is not a word-for-word script. It holds the topics, the order, and the code. The
presenter improvises the narration around it.

---

## Before starting: read the series, then calibrate

### Read what the project already decided

Series projects usually keep their rules in a project-level instructions file (e.g. `CLAUDE.md`)
or a series-bible document. Read it in full before asking anything. Look for, and treat as binding:

- the syllabus (episode number → topic → what is done with the shared dataset),
- the pseudo-script template, if the project defines one (use it exactly, header for header, and
  don't swap in the default below),
- language rules (e.g. code and comments in one language, narration in another),
- audience assumptions (what they already know before episode 1),
- scope rules for the dataset (how much domain knowledge is allowed on camera),
- dates, presenters, rotation,
- earlier material the series builds on or replaces (a previous course, notebook, or playlist).
  Read it, not just its title. It may already teach part of this series' syllabus, which changes
  what the audience already saw and which episodes overlap. When it does, ask whether this series
  continues it, replaces it, or is independent, and record the answer in the findings document.

Then read the previous episode's pseudo-script, if one exists. It is the real source for "what the
audience already saw": the variables it left defined, the concepts it introduced, and what its
closing promised this episode would do. If the previous episode doesn't exist yet (episodes written
out of order), say so and work from the syllabus, flagging every assumption about it.

Inspect the dataset itself by running something (shapes, dtypes, sizes, value ranges, missing-value
counts), not only the description of it. If a data-preparation script exists, read it. Its
docstring often records deliberate choices (something left messy on purpose for a later episode)
that the script must not "fix".

### Calibrate the presenter of this episode

Series often rotate presenters, so calibrate **the person presenting this episode**, not whoever
happens to be typing. The person in the session is often the **script author**, who writes the
pseudo-scripts for every presenter. Ask who presents this episode. When the author and presenter are
different people:

- the calibration levels below are the presenter's, not the author's;
- the author answers the gate questions on the presenter's behalf, and the questions are aimed at
  the presenter's level;
- the pseudo-script must be enough on its own for the presenter, who wasn't in the session.

Ask briefly, on two separate axes (the same split `build-to-learn` uses):

1. **The tool or subject being taught** (the library, the technique).
2. **The domain of the shared dataset** (the science or business context the examples come from).

A presenter can be Level 4 on the tool and Level 1 on the dataset's domain. Place them on each axis
with this scale, kept identical to the sibling skills:

1. Everyday language only. Vague goals. Can't evaluate an explanation beyond "that makes sense."
2. Some domain terms, possibly imprecise. Catches obvious errors in an explanation; misses
   plausible-looking wrong ones.
3. Decomposes problems. Uses general analytical language (parameterize, decompose, normalize).
   Recognizes a wrong approach but relies on the summary for the correct one.
4. Specifies what and how at a technical level. Precise domain terms. Catches non-obvious errors.
   Proposes alternatives with justification.
5. Full command of the domain. Evaluates nuanced alternatives; distinguishes better from merely
   different.

(The level scale is adapted from the dialogic-bootstrapping teaching methodology in
[SwRI-IDEA-Lab/vocal_prompt](https://github.com/SwRI-IDEA-Lab/vocal_prompt), the same source both
sibling skills credit.)

Also ask how the presenter best internalizes a new concept: a precise definition, a worked example,
an analogy, a contrast with something they'd confuse it with, or seeing it in context. Use it for
how you explain things to them in chat and for how you phrase the gate questions. Keep it separate
from how the *audience* will be taught, which the series' own style decides.

Confirm the output language for the pseudo-script and the chat if the project file doesn't already
fix it. Don't infer it from the language of the request.

Don't re-ask anything the project file or an earlier episode already answered. Calibration is
there to fill gaps, not to repeat a questionnaire every episode.

---

## The work order

### Show the outline first, then fill one section at a time

Create the pseudo-script file with every template section as a heading plus a one-line summary of
what it will contain:

```markdown
## <section heading from the template>
<one line: what this section will hold for this episode>
```

Then fill **one section at a time**, in the template's order. Never pre-fill several sections at
once, even when it would save a round trip. The pacing is the point: each approved section
constrains the next one (the concept list decides the code cells, the code cells decide the likely
questions).

When the template has a large "code cells" section, treat **each concept's group of cells as its own
sub-step**: draft, run, show, approve, then the next concept. A long block of twenty cells approved
in one go hides exactly the cell nobody looked at.

### The loop for each section

1. **Draft** the section and write it into the file. For code, see "Code cells" below: run it
   first, then write it.
2. **Show it** in chat in short form: what changed, plus anything you were unsure of, flagged as a
   question, never silently decided.
3. **Gate: the audience's questions.** Ask the presenter 1–3 questions the audience is most likely
   to ask about *this* section, phrased as a viewer would ask them ("why not just use a loop?",
   "what happens if a value is missing?", "why is this number different from the one before?").
   Prefer questions with real stakes over recalling a definition.
   - Good answer: confirm it and move on. A good question becomes a candidate for the "likely
     questions" section, together with the answer the presenter gave, refined.
   - Shaky answer: explain it in the presenter's internalization style and ask a related question.
     After two attempts, teach it directly and move on. This is rehearsal, not an exam.
   - Most important: if the gap comes from the **dataset's domain** rather than the subject being
     taught, that's a signal to **simplify the data or the framing**, not to make the presenter
     study more. Record it in the findings document (see below).
   - Before an answer becomes a "likely question", check any factual or quantitative claim in it
     with a quick real run. Refine the answer with the result, even when the gist was right.
   - Skip the gate for purely mechanical sections (a closing line, a link) unless the presenter
     asked for it everywhere. Ask once at calibration which they prefer and apply it consistently.
4. **The presenter refines.** Apply their edits. Their version wins over yours on phrasing, order
   and emphasis. Push back only on factual errors or code that won't run as written.
5. **Explicit approval** ("ok", "siguiente", "next") before moving to the next section. Silence or
   a topic change is not approval.

### Section-specific rules

- **Summary** (always first, before the objective, even when the project's template lacks it; if
  so, propose adding it to the project's template): 3–4 short paragraphs so the presenter grasps the
  whole episode before reading it section by section. Cover the question this episode answers within
  the series, which data it uses and *why that data*, and the path the episode walks from start to
  finish. Its job is that the presenter never meets a dataset that seems to come out of nowhere.
  Write it from what's already decided; don't presume sections still pending approval.
- **Objective**: one sentence, written from the audience's side ("by the end, you can ..."), not the
  presenter's ("I will show ..."). If it makes a quantitative promise ("much faster", "uses half
  the memory"), measure it at the data scale this episode actually uses before keeping it. A claim
  that only holds at a larger scale gets reworded.
- **What the audience already saw**: taken from the previous pseudo-script, not from the syllabus
  alone. For episode 1, take it from the prior material the project points to, and check each
  "they already know X" by searching that material (grep its code cells), not by assumption.
  Also list what they have *not* seen that the cells might be tempted to use. List the variables that exist at the start of this episode and how to rebuild them in the
  first cell, because every episode must run on its own, from files, never from the previous
  episode's session state.
- **What you need to know about the data**: respect the project's limit (if it sets one, e.g. "five
  lines maximum"). If it doesn't fit, don't write a longer section. Stop and propose a change to the
  data preparation instead, recorded in the findings document.
- **Concepts to present**: respect the project's cap (default: at most 3–4). Order them
  concrete-first: the thing the audience can see before the abstraction that explains it. Every
  concept must lead to a visible result on the dataset. A concept with no on-screen payoff gets cut
  or moved.
- **Code cells**: see below.
- **Likely questions**: built mostly from the gate rounds, each with a short answer the presenter
  has already said in their own words. Add questions the gate didn't cover only if they're likely.
- **What NOT to explain in this episode**: the tempting tangents, each with where it does belong
  ("episode N" or "out of scope for the series"). This protects the presenter from going down a
  rabbit hole live.
- **Closing and link to the next episode**: one concrete hook into the next syllabus item, using
  something visible on screen (a problem this episode left unsolved). Check it against the next
  episode's syllabus line. Don't promise what the next episode won't deliver.

---

## Code cells

### Run every cell for real, in order, in a fresh session

- Use the project's own environment (its virtual environment or lockfile), not a global one.
- Run the episode's cells **top to bottom in a fresh process** before pasting any output. A cell
  that only works because of a variable left over from an earlier experiment will fail on camera.
- Paste the **actual output**, never a hand-computed one. A wrong "expected output" on screen is
  worse than none, because the presenter will trust it and the audience will see the mismatch live.
- Keep a runnable reference file holding exactly the cells in the script, in the format the series
  records with (often a notebook), plus a small runner that executes it top to bottom in a fresh
  kernel or process and prints every output. It lets anyone re-verify the whole episode in one
  command after the data or the environment changes. See "What the presenter receives" for who it
  belongs to. If the runner needs packages the environment lacks, install them into the project's
  environment and say so.
- When an output depends on the machine (timings, memory addresses, random numbers without a seed),
  say so next to it ("≈, varies per machine") and show the order of magnitude, not a fake-precise
  number. If the series has a fixed seed, use it.
- When an output is long, show the truncated version the presenter will actually see and say
  that it's truncated.

### Stay inside what the audience has seen

Every cell may use only:

- what the project says the audience knows before episode 1,
- what earlier episodes introduced,
- what this episode is introducing.

A cell that needs something from a later episode (a feature that's still to come in the syllabus)
gets rewritten without it. If that's impossible, it's marked inline as deliberate, e.g. `# ojo:
esto lo vemos en el video N` / `# note: covered in episode N`, and listed under "What NOT to explain".
Check this explicitly for every cell against the syllabus. It's the most common way a series
quietly breaks its own order.

### Cell format

Each cell, in the pseudo-script:

````markdown
**Celda N: <what it shows, in a few words>**

```python
<the exact code, in the code language the project fixes>
```

Salida:

```
<the real output, pasted>
```

<one line for the presenter: what to point at on screen>
````

Use the project's header language. The example above is the Spanish form; the English form is
`**Cell N: ...**` and `Output:`. Never mix the two in one document.

Plots: save the image next to the pseudo-script, link it in place of a text output, and look at it
yourself before claiming what it shows.

---

## What the presenter receives

Ask the script author at the start of the first episode how the hand-off works, and record the
answer in the project's memory or instructions so it isn't re-asked. The default, when the author
writes and someone else presents:

- **The presenter receives only the pseudo-script**, plus a short structure guide for their own
  material. They build their own recording material (notebook, slides, script file) from the
  pseudo-script. Implementing it themselves is how they come to understand what they'll explain.
- **The reference file is the author's.** It holds every approved cell already run, so the author
  can confirm everything works. It isn't handed over by default, only if the presenter gets stuck.
  Keep it in a folder separate from where presenters keep their own material, so the two never
  collide at the same path. Mark its author line as a placeholder.
- **The presenter's material follows a fixed structure** that the project points to (often the
  structure of an earlier series), with the presenter's own authorship, language and phrasing.
  Write that structure once as a guide next to the pseudo-scripts: where the file goes, the paths
  that work from there, the header with an author line, the fixed sections, the language rules,
  and how to verify it with the same runner. If the project points to no structure, ask; don't
  borrow one from an unrelated general standard.
- **What is fixed for the presenter:** the code and its output. If they change a cell, their
  output must still match the pseudo-script's.

---

## Default template (only when the project defines none)

Spanish:

```markdown
# Pseudoguion: <Serie> <N> - <Tema>

## Resumen (léelo primero)
## Objetivo (1 frase)
## Lo que la audiencia ya vio
## Lo que necesitas saber del dato (máximo 5 líneas)
## Conceptos a presentar (en orden, máximo 3 o 4)
## Celdas de código (completas, probadas, con la salida esperada)
## Preguntas probables y cómo responderlas
## Lo que NO hay que explicar en este video
## Cierre y enlace al siguiente video
```

English:

```markdown
# Pseudo-script: <Series> <N> - <Topic>

## Summary (read this first)
## Objective (1 sentence)
## What the audience already saw
## What you need to know about the data (5 lines max)
## Concepts to present (in order, 3–4 max)
## Code cells (complete, tested, with real output)
## Likely questions and how to answer them
## What NOT to explain in this episode
## Closing and link to the next episode
```

---

## Series index

Keep one index document for the series (default `<scripts dir>/README.md` or `series_index.md`,
or reuse one the project already has). It is the source of truth for status, not repeated inside
every pseudo-script:

```markdown
| # | Tema | Presentador | Pseudoguion | Estado |
|---|---|---|---|---|
| 1 | <topic> | <name> | [`01-<slug>.md`](01-<slug>.md) | Aprobado |
| 2 | <topic> | <name> | `02-<slug>.md` (propuesto) | En curso |
```

Update a row only when the presenter confirms the whole pseudo-script is approved, not one section
of it. Every link is a relative link, checked to resolve (`ls` the path) before writing it.

## Findings document

Writing a script closely enough to run it surfaces problems no upfront plan catches: a value in the
data that needs a paragraph of domain explanation, a cell that only works with a feature from a
later episode, a syllabus item whose on-screen payoff is invisible on this dataset. Record these in
a living `prep_findings.md` next to the index, with one `##` heading per episode, added only when
that episode has a finding:

```markdown
## <N>. <topic, matching the index exactly>

### <short name for the finding>

Qué pasa: <one or two sentences>.

Propuesta: <change to the data preparation, the syllabus, or the script>.

Evidencia: <the real run that shows it: output, shape, plot. Never a claim without one>.

Afecta a: <which other episodes this change would touch>.
```

These are proposals for the project owner, not changes to make silently. Never edit the
data-preparation script, the syllabus, or another episode's approved pseudo-script without explicit
approval. If a change is approved, re-run the reference files of **every** episode it touches and
update their pasted outputs.

## What never goes into a pseudo-script

- Code that wasn't run, or output that wasn't pasted from a real run.
- Domain jargon beyond what the project allows on camera, even when it's "technically more
  correct".
- A concept, function, or keyword from a later episode, unless it's marked as deliberate.
- Private data, credentials, or personal details, since a video script gets published.

---

## Hard rule for this skill file itself

This file is meant to be reused across many different, unrelated video series. When updating it
after using it somewhere, genericize any lesson learned before it lands here. Never let a specific
series' topic, library, dataset, presenters, dates, or findings leak into this file. This applies
to every part of an edit: the prose, the code identifiers, the sample values. Before considering an
edit done, reread (or grep) the exact text for any term specific to the series you were just
working on, and replace it with a generic placeholder. Project specifics belong in that project's
own instructions file, which this skill reads at the start.
