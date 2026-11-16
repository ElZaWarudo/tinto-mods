# Stage: review the prose

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
(file:line), and what the brief or the evidence allows instead.

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
6. The argument follows the brief's order; the length is within range.

**Evidence**
7. Every value in the prose equals its source file (units, rounding,
   conditions).
8. Each claim stays within the conditions and the sample of its evidence.
   If the brief itself authorised more than the evidence supports, say so
   as a finding that starts with "Brief:", so the coordinator fixes the brief
   rather than the prose.

**Citations**
9. Every citation key exists in the repository's bibliography.
10. Each citation is used only for what the brief says it supports.

**Coherence and style**
11. Terms and notation match the brief's "Style" and the neighbouring
    sections.
12. The text reads as one argument: no contradictions, no repetition of
    another section, no orphan references or labels.
13. The repository's own conventions for this kind of file hold (its
    `AGENTS.md`).

## Not findings

Wording you would have chosen differently, when it is correct and within the
brief, is not a finding: put such notes in `handoff`. With no findings, your
status is `pass`.

## Re-review in a round

When the instructions list a round's items, check that each was addressed
and that the fix changed nothing else. Do not review the rest again.
