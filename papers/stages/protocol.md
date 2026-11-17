# Stages: preregister an experiment

Three stages. Read the section for yours. The repository's rules (its
`AGENTS.md`, its experiment procedure, template and schema) win over this
guide.

A preregistration fixes, before any run, what would count as a result: the
hypotheses and what refutes them, the measures, the sample, the analysis,
and when to exclude or stop. Nothing in it may depend on seeing the data.

## `design`: the design (you change nothing)

1. Read the repository's experiment procedure, template, manifest schema and
   the paper's manifest, plan and research questions. Read the code
   repository named in the assignment (at its pinned commit) enough to know
   what it can run and what it outputs.
2. Design the experiment for the research question the assignment names.
   Keep it to what the code at that commit can do; what it cannot do is a
   question, not an assumption.
3. Your `handoff` is the design, with exactly these headings, in this order.
   The length guidance Tinto gives for `handoff` does not apply.

```markdown
# Design: <KEY> <experiment id>: <title>

Version: <1, then +1 each time it changes>

## Essentials
<At most 7 one-line items: each hypothesis with what refutes it, the main
measure and its raw file, the sample and why, the analysis, the stopping
rule, the run command and its estimated duration.>

## Question and hypotheses
## Subjects and versions
<Code repository and full commit, datasets, tool versions.>
## Procedure
## Measures
<Each: how it is computed, from which raw output file of a run.>
## Sample size
## Analysis plan
## Exclusions and stopping rules
## Run entry point
<Where it lives, the exact command, what it builds and runs.>
## Files
<The experiment's folder and files, and the paper manifest's fields to set.>
## Open questions
```

Questions for the user go in `findings`, one per line, in plain language
(data that may not be published, a tool the system lacks, two designs with
different costs). Questions are not defects: with none, your status is
`pass`.

## `protocol`: write it from the approved design

1. Read the design at the path the assignment gives. Follow it exactly: a
   change you think it needs is a finding, not an edit.
2. Copy the repository's experiment template to the experiment's folder and
   fill every section and manifest field from the design. Set the status the
   repository uses for a preregistered experiment, list the experiment in the
   paper's manifest, and set the plan fields the assignment names.
3. Write the **run entry point**: a target of the repository's task runner
   in the experiment's folder (for example a `Makefile` with a `run` target),
   so the command is `make -C <experiment folder> run` or its equivalent.
   Tinto runs only commands from the repository's settings, which may not run
   a script by relative path, name an absolute path or use `..`; the target
   itself may call scripts. It must:
   - take the code repository from `.tinto-repos/<name>` at the worktree's
     root (a variable with that default) and fail unless its `HEAD` is the
     pinned commit;
   - write each run into the first `results/run-<n>/` that does not exist,
     never into an existing one;
   - put the raw outputs there as the code produces them: copied, never
     transformed;
   - write `environment.md` in that folder: date, CPU, memory, OS, tool
     versions, the code's commit, the seeds;
   - read any key it needs from the environment and never write it to a
     file;
   - exit non-zero when a step fails or a declared raw output is missing.
4. Do not run the experiment. Change no file outside the experiment's folder
   and the paper's manifest.

## `methods-review`: an independent methodology review

You change nothing. Read the design (path in the assignment), then the
change (`git diff` against the task's base) and the files in full. Each
failure is one finding: what, where (file:line), what would fix it.

1. Each hypothesis is falsifiable: a criterion in measured terms, fixed now,
   says what refutes it.
2. Each measure names the raw output file it comes from and how it is
   computed.
3. The sample size has a stated reason (precision, power, or a resource
   limit named as such).
4. The analysis plan is fixed: estimators or tests, effect sizes, intervals,
   how failures and ties are handled, no comparison left open.
5. Exclusions and stopping rules are stated and cannot be chosen after
   seeing results.
6. Code, data and tools are pinned (full commit, versions); the procedure is
   precise enough for someone else to repeat.
7. The run entry point does exactly the procedure and meets every point of
   `protocol` step 3.
8. Every template section and manifest field is filled as the repository
   requires; nothing outside the experiment's folder and the paper's
   manifest changed.
9. The files match the design. A flaw in the design itself is a finding that
   starts with "Design:".

Wording you would have chosen differently is not a finding. With no findings,
your status is `pass`.

## Output

- `summary`: one line.
- `findings`: one line each; a defect or a question only, no praise.
- `commit_plan` (`protocol` only): one commit in the repository's convention.
