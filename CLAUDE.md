# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

## What this is

An academic paper (work in progress), *"LaTeX through the lens of variation
theory: misconceptions and critical aspects of document preparation"*
(Bosk), cataloguing learners' misconceptions of LaTeX — the compile model,
error handling, semantic markup — and analysing how to teach against them
using phenomenography and variation theory. Companion to
vt-prog-misconceptions (same method, introductory programming), vt-debug
(debugging), and vt-terminal (the Unix shell); it grows out of the datintro
course-revision work in the introtools repository (see
`introtools:evaluation/improvements.md` and
`introtools:literature/mental-models.md`).

Distinctive constraint: the LaTeX-misconception literature is essentially
empty (the seed round found only Knauff & Nejasmic 2014, an efficiency
comparison of professionals). This paper therefore leans on adjacent
literatures (error messages, markup education) and on datintro course data
more than the companions do.

## Build

```sh
make            # builds both article.pdf and slides.pdf
make article.pdf
make clean
```

Build machinery comes from the `makefiles/` and `didactic/` git submodules;
after a fresh clone:

```sh
git submodule update --init --recursive
```

Requires a TeX distribution with `minted` (Pygments, `-shell-escape`),
`pythontex`, and `biber`. Outputs land in `ltxobj/`; `article.pdf` and
`slides.pdf` at the root are symlinks.

## Architecture: one source, two outputs

The content `.tex` files are compiled twice from the same source — once as a
prose article (`article.tex`, memoir + beamerarticle) and once as Beamer
slides (`slides.tex`). Both input the same `preamble.tex` and the same
content files (`introduction.tex`, `background.tex`, `method.tex`,
`compilation.tex`, `errors.tex`, `markup.tex`, `related-work.tex`,
`conclusions.tex`). The article alone appends `quiz.tex` (woven from
`quiz.nw`, see below) and `search-protocol.tex`.

Consequences when editing content files:

- Every content file starts with `\mode*`; slide-only material goes in
  `\mode<presentation>{...}` or `\begin{frame}...`; article-only prose is the
  default outside frames.
- Research questions live in `restatable` environments (thm-restate) —
  stated once in `introduction.tex` **inside a frame** (so both builds
  execute them), restated with `\rqmisconceptions*` etc. in `method.tex` and
  `conclusions.tex`. Never write literal "RQ1".
- Citations use `\autocite` (biblatex verbose style; didactic places them in
  the margin/footnotes).

## Bibliography discipline

`misconceptions.bib` entries carry provenance blocks (CLAIM / FOUND-VIA /
PICKED / QUOTE / VERIFIED) per the backing-claims skill; do not add a
citation without one, and do not cite beyond what the VERIFIED line
supports. Knauff & Nejasmic 2014 must not be over-claimed: professionals,
text-typing tasks, equations favoured LaTeX. Entries with `VERIFIED: TODO`
(du Boulay 1981, Ben-Ari 1998) are tracked as introtools issues #124–#125.
`theory.bib` holds the variation-theory base (NCOL etc.), shared with the
companion papers.

## The instrument (`quiz.nw`)

`quiz.nw` is a literate program (noweb; `make programs` tangles
`quiz-knowledge-{start,end}.json` and `analyze_quiz.py`, all gitignored).
Each quiz has an opener (consent / preparation, position 1), six open
essay items (positions 3–8, the phenomenographic accounts — placed
*before* the closed items and shown one at a time without backtracking so
the distractors cannot seed the accounts) and the closed knowledge items
(positions 11–21, one per candidate critical aspect in chapter order, plus
a second item for *references are relations*; item 11, *where the change
goes*, is the closed twin of open item 4 and is in the end quiz only, so
the start quiz has 10 closed items and the end quiz 11). Canvas New-Quiz
settings: `multiple_attempts` and `result_view_settings` are *nested*
objects inside `quiz_settings` (flat keys are silently dropped by Canvas);
the start quiz hides correctness and correct answers, the end quiz shows
them. The two JSONs carry canvaslms `modules` specs: each quiz is the sole,
must-submit item of its own datintro26 module ("LaTeX pre-test" / "LaTeX
post-test") bracketing the "Report writing" module, and the appendix prose
gives the `modules create`/`modules edit --prerequisite` commands that
chain pre-test → Report writing → post-test.
Items are keyed by title in the Canvas report (substring match — no title
may be a substring of another); `analyze_quiz.py` reads the answer key
from the tangled **end**-quiz JSON, filters by consent, prints per-item
facility and distractor counts, paired pre/post gains (`--quiz both
--results start.csv end.csv`) and writes a long-format coding sheet for
the accounts (optional `--llm` pre-coding in separate `suggested_*`
columns). It imports `canvaslms` only when fetching from Canvas; run it
with the pipx interpreter (`~/.local/pipx/venvs/canvaslms/bin/python`) for
that. Activate the `literate-programming` skill before editing `quiz.nw`.

## State of the paper

Draft v1 plus the deployment round: research questions, method (literature
review, course-data stub, variation-theory analysis, course instruments),
the systematic search rounds (all documented in `search-protocol.tex`,
confirming the LaTeX-specific gap), preliminary aspect/pattern analyses per
chapter, and the pre/post-test appendix above. `% TODO`/`% XXX` comments
mark the open work and are mirrored as GitHub issues.
