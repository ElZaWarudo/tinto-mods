# Stages: prepare, run and analyse an experiment

Tinto runs the experiment's command between `prepare` and `analysis`.
After a successful analysis, the shared prose stages turn the evidence
into a section or a results report. The repository's experiment procedure
and `AGENTS.md` win over this guide.

Always:

- The protocol, as merged, is the plan. Follow it; never reinterpret it.
- **Never write, move or delete anything under the experiment's results
  folder.** Only Tinto's run writes there. A correction is a new run.
- The code repository is at the path, commit and role the assignment gives.

## `prepare`: ready to run

1. Check that the protocol is merged on the base with the repository's
   preregistered status (or later), and that its run entry point exists and
   does what the protocol says. If a prerequisite is missing, return
   `blocked` with what needs to be fixed before execution.
2. Set the manifest's status to the one the repository uses for a running
   experiment, and its source commit to the code repository's full commit.
3. **In a round with a deviation** (a methodology error, a change the user
   asked for): add one entry under the protocol's Deviations, dated today:
   what changes, why, who reported the issue and what it affects
   (run, measures, analysis). Then make
   only the change the deviation needs (the run entry point, a parameter).
   Never edit the protocol's other sections; never touch earlier runs.
4. Do not run the experiment.

## `analysis`: from raw outputs to figures and tables

1. Read the protocol (with its Deviations) and the run folder the assignment
   names. Check that the run wrote every raw output the protocol declares and
   its environment record. Return `blocked` and name any missing outputs.
2. Write the analysis as scripts in the experiment's `analysis/` folder,
   exactly as the protocol's analysis plan says. Each script names its input
   files (in the run folder) and writes its figures and tables into
   `analysis/`; each figure also as a PNG. Run them.
3. An analysis the plan does not include (one the data suggested) is
   exploratory: write it only if the assignment asks, label it exploratory in
   its file name and output.
4. Point the manifest's declared outputs at the analysed run's files and set
   the status the repository uses for a complete experiment once every
   declared output exists. Set the paper manifest's fields the assignment
   names.
5. In `handoff`, give paths to the protocol, exact raw run, experiment
   manifest, analysis scripts and their outputs. For each hypothesis,
   include the observed value with units, its uncertainty when the
   protocol calls for it, and whether it meets the criterion. List
   deviations and separate any exploratory outputs. The brief and review
   will open the evidence themselves.

## `results`: record for tasks created with the earlier flow

You change nothing in the worktree. You write one record, `<KEY>.md`, in
your QA folder, by Tinto's QA record guide for "a record not about a UI".
Copy the figures you show into your QA folder as `<KEY>-<n>-<slug>.png`.
Never run the experiment's run command.

Verify, then record:

1. Every raw output the protocol declares exists in the run folder, with the
   environment record, and earlier run folders are as the base has them.
2. Every number you report: recompute it from the raw files yourself (a
   short computation, printed in a fenced block), and compare it with the
   analysis' output.
3. Each figure and table comes from a script in `analysis/` that names its
   input.
4. The run followed the protocol; each departure is a recorded deviation.

**The verdict is about the record, not about the hypotheses.** `pass`: the
run followed the protocol (or recorded its deviations) and every number
traces to raw outputs. `fail`: something must be fixed first; each such thing
is a problem. `not verifiable`: what is missing to tell. A hypothesis the data
refutes is a result, never a problem.

The layout, in the user's language, keeping the bold markers as written:

```markdown
# <KEY>: <one line: what the experiment found>

**Verdict:** pass / fail / not verifiable: <why, in one line>

**Summary:** <at most three lines: each hypothesis met or not, with its
number; and what the user has to decide, if anything>

| | |
|---|---|
| Date | <YYYY-MM-DD> |
| Run | <run folder>, code <owner/repo@commit> |
| Environment | command line, no UI |

## H1: <the hypothesis, in one line>

![<one line: what it shows>](<KEY>-1-<slug>.png)

| Measure | Value | Protocol's criterion | Met |
|---|---|---|---|
| <measure> | <number with unit and interval> | <criterion> | yes / no / inconclusive |

<At most three lines: what the number means, within the protocol's
conditions and sample.>

## Deviations

<Each recorded deviation in one line, with its date; or "None".>

**Problems:** <P1: ... one per item; or "None">

**Suggested solutions:** <P1: ... ; or "None">
```

One section per hypothesis, then exploratory results (if any) under their own
heading, labelled exploratory. Problems are defects in the run, the analysis
or the record (a number that does not trace, a missing output, a protocol
step not followed), each fixable on its own.

## Output

- `summary`: one line (for `results`: the verdict and the hypotheses met).
- `findings`: one line each; a defect or a question only.
- `commit_plan` (`prepare`, `analysis`): one commit in the repository's
  convention.
