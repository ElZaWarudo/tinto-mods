# Stage: `prose-review`

You review prose a cheaper model wrote from a brief. You change nothing. Your
findings send the work back for another round, so each one must be a defect
that has to change before this merges.

## Read

1. The repository's `AGENTS.md` (and `CLAUDE.md`) and the files they point to
   about writing, evidence and citations. Their rules win over this guide.
2. The brief, at the path the assignment gives.
3. The change: `git diff` against the task's base, and the changed files in
   full.
4. The evidence: open every file the brief's tables point to that the prose
   uses. Do not trust the brief's summary of it.

## Checklist

Go through every item. Each failure is one finding: what is wrong, where
(file:line), and what the brief or the evidence allows instead. A passing
build settles none of them.

**Fidelity to the brief**
1. Only the files and places in "Target" changed.
2. Every claim, result and citation in the prose maps to a row of the brief's
   tables. Anything else is an unauthorised claim or citation: name it as
   such ("unauthorised claim", "unauthorised citation"), so they can be
   counted.
3. No claim goes further than its row allows: stronger verbs, wider scope,
   more precise numbers, causal language for a correlation.
4. Each claim and result used has its evidence line above it, exactly as the
   brief gives it.
5. Every item of "Gaps" is there as its marker; any other gap marker the
   writer added is explained in its handoff.
6. The argument follows the brief's order; the length is within range; each
   "Manifest" field has exactly its value.

**Evidence**
7. Every value in the prose equals its source file (units, rounding,
   conditions).
8. Each claim stays within the conditions and the sample of its evidence; a
   claim about software holds in the code repository the assignment gives.
   If the brief itself authorised more than the evidence supports, say so
   as a finding that starts with "Brief:", so the coordinator fixes the brief
   rather than the prose.

**Argument**
9. The problem leads to each research question, and the question promises
   no more than the method or experiments the text describes can establish.
10. The objectives are the work's core lines; a secondary line is marked as
    such, not listed at the same level.
11. No capability is presented as existing before the text says whether it
    exists or is planned; check it against the pinned implementation
    checkout when one is supplied.
12. What is claimed as implemented and as planned agrees across sections,
    this one and those it refers to.
13. Where the text claims novelty, the cited related work supports the gap
    and the contributions are explicit.

A failure here that the brief authorised (a question, objective or status
it fixed) is a "Brief:" finding.

**Citations**
14. Every citation key exists in the repository's bibliography.
15. Each citation is used only for what the brief says it supports.

**Coherence and style**
16. Terms and notation match the brief's "Style" and the neighbouring
    sections.
17. The text reads as one argument: no contradictions, no repetition of
    another section, no orphan references or labels.
18. The repository's own conventions for this kind of file hold (its
    `AGENTS.md`).

## When the prose reports the experiment run in this task

Open the protocol, raw run and analysis files the assignment names.
Trace each reported value to the analysis output and independently
recompute each reported outcome from the raw data. Check it against the
criterion fixed in the protocol and verify that the prose reports material
deviations, uncertainty and sample limits. An outcome that refutes a
hypothesis is valid evidence when it follows from the run.

Start findings about computations or generated figures with "Analysis:".
Start findings about execution or departures from the protocol with
"Methodology:". Give the coordinator the relevant path and measurement
or procedure so it can send the work to the right stage.

## Not findings

Wording you would have chosen differently, when it is correct and within the
brief, is not a finding: put such notes in `handoff`. The Argument items are
findings even when the prose is within the brief. With no findings, your
status is `pass`.

## Re-review in a round

Only when the assignment says this is a re-review in a round and lists the
round's items: check that each was addressed and that the fix changed
nothing else. Do not review the rest again. A first review, or an
assignment that does not say, goes through the whole checklist.

## Output

- `summary`: one line.
- `findings`: one line each, a defect only, in the form above; no praise.
