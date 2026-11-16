# Papers: coordinator guide

You coordinate a Delivery batch that writes or revises an academic paper. The
split of work is fixed:

- **Claude** does the intellectual work: it decides what each section argues,
  checks that every result, claim and citation has evidence in the repository,
  and reviews the prose.
- **A cheap model through OpenCode** only turns an approved brief into prose.

The **brief** is the contract between them: a file with a fixed structure
(`stages/brief.md` has the template) that says exactly what the writer may
write. Because everything the writer needs is in the brief, the writer model
can be swapped without changing anything else.

This guide sits on top of Tinto's own coordinator instructions (the
`tinto-delivery` MCP server's instructions and tool descriptions). Where they
say how a tool works, they win. In this guide, `MOD` is the absolute path of
the folder that holds this file; every job of the repository can read it.

## 1. Intake: the repository's rules win

Before creating anything:

1. Read the target repository's `AGENTS.md` (and `CLAUDE.md` if there is
   one), its `README.md`, and every file they send you to for writing,
   citations, evidence or experiments. Read the manifest of the paper you are
   asked to work on (for example a `paper.yaml`), its plan or outline, and
   its build file (`Makefile`, `latexmkrc`, scripts named in the README).
2. Write down, for yourself, the repository's answers to these questions.
   You will copy them into every brief:
   - Which paper, and which files of it may change.
   - How a claim cites its evidence (for example a comment line above each
     empirical claim) and what counts as evidence (repository paths, pinned
     commits of other repositories, completed experiments only).
   - Where the bibliography lives and the rule for adding an entry.
   - The marker for a gap (for example `\todo{...}`).
   - Files nobody may edit (raw results, other papers).
   - The branch name convention, the commit message convention, the pull
     request template and who reviews.
   - The commands that check the repository and build the document.
3. **When the repository's rules and this guide disagree, the repository
   wins.** Follow its rule and say so in one line to the user. Example: the
   repository allows one paper per branch, so the batch covers one paper.
4. Call `read_settings` for the repository and fix what is missing:
   - `checks`: when the repository's check and build commands are not there,
     propose them with `propose_settings` `checks_add`, exactly as the
     repository runs them (for example `make check` and the command that
     builds this paper). A dependency install the build needs (a Python
     requirements file) goes in `setup_add`; a system install (a TeX
     distribution) is the user's: tell them.
   - `stages`, `skip_stages`, `no_jira_steps`: this mod proposes explore
     and review on `claude`, implementation on `opencode`, no tests stage,
     no QA stage and no Jira steps. The user approves that proposal in Tinto
     when they choose the mod for the repository. If `read_settings` shows
     they are not in force yet, tell the user once, in one line, that the
     mod's settings wait for their approval, and until then pass `agent` on
     every `dispatch_job` as section 4 says.
   - The writer model: the mod fixes none. Never pick it yourself: use the
     model set for the implementation stage, or ask the user once with
     `request_decisions` which of their OpenCode models writes, then propose
     it for the implementation stage with `propose_settings` `stages`.
   - `scope`: if it is `never`, the explore stage cannot run, and the brief
     comes from it. Ask the user to set Alcance to always or optional in
     Ajustes before going on.
5. Make sure `.agent/` is ignored in the main checkout (it holds the briefs,
   section 3). If `git check-ignore .agent/x` prints nothing, add the line
   `.agent/` to `.git/info/exclude`. Never add it to a committed file.

## 2. Cutting the paper into tasks

One task is one section, or one coherent revision (for example "align the
terminology of sections 3 to 5 with the new definition"), and ends in one
pull request.

- Never split one section across tasks, and never put two papers in one task.
- A task owns its target files. Two tasks that would edit the same file
  conflict on merge: chain them (`set_task_blocked` on the first one's
  `merged`) or merge them into one task.
- Sections that summarise others (abstract, introduction, conclusion,
  discussion) come last: create them with the rest so the user sees the plan,
  then block each with `set_task_blocked`, `{"type": "task", "task": "<key>",
  "until": "merged"}` for every section it summarises.
- Tasks whose claims rest on missing evidence (an experiment not complete) are
  not created: list them to the user as waiting on that evidence.
- Bibliography changes are their own task, done by a Claude implementation
  job, never by the writer (section 4, "Citations").
- Keys: the paper's id plus a short section slug, for example `P02-threat-model`.
  Pass the repository's branch name in `create_task` `branch`
  (for example `paper/p02/threat-model`).

Create the batch with `create_run`, then every task with `create_task`. Before
each `create_task`, create the empty folder `<main checkout>/.agent/papers/<KEY>/`
and pass its absolute path in `read_dirs`: every job of the task can then read
the brief you will store there.

Then, for each task, `set_task_state` with `checks` (the repository's check
and build commands from `read_settings`, each exactly as written there) and,
if the build needs one, `setup`. Leave `needs_ci` true unless the repository
has no CI for its documents.

## 3. Stages

| Order | Role in `dispatch_job` | Agent | Writes | What it does | Stage guide |
|---|---|---|---|---|---|
| 1 | `explore` | `claude` | no | Writes the brief; verifies evidence and citations before authorising them; returns questions as findings | `MOD/stages/brief.md` |
| 2 | `implementation` | `opencode`, writer model | yes | Writes only the target files from the brief | `MOD/stages/write.md` |
| 3 | `review` | `claude` | no | Checks fidelity to the brief and the evidence; findings send the task back | `MOD/stages/review.md` |
| - | checks | Tinto | - | Runs the task's `checks` after each writing stage | - |

There is no tests stage and no QA stage: with the mod's settings approved
(`skip_stages`), Tinto does not ask for them and refuses a job of those roles.
If a task's `next_stage` is `tests`, the settings are not approved yet: do not
dispatch it, and tell the user.

### 3.1 Explore: the brief

Dispatch `dispatch_job` with `role: "explore"`, `agent: "claude"` (omit
`agent` when the explore stage is already fixed on Claude) and an assignment
that contains, in this order:

1. "Read and follow `MOD/stages/brief.md`." (with the absolute path).
2. The task: which section or revision, the paper's id and its manifest path.
3. Your intake notes from section 1, step 2, copied whole.
4. For a revision: what has to change and why (the reviewer's comment, the
   user's request), with paths and line numbers.

When it ends (`read_job`):

- Its `handoff` is the brief. Save it verbatim, without editing, as
  `<main checkout>/.agent/papers/<KEY>/brief.md`. If it does not follow the
  template's sections, dispatch explore again saying which are missing.
- Record the plan with `set_task_state` `plan`: `what` from the brief's Task,
  `places` from its Target, `risks` from its Gaps, `out_of_scope` from its
  Forbidden list. Do not copy the whole brief into the plan.
- Its `findings` are questions for the user. Ask them with
  `request_decisions` (section 5). The writer does not start until they are
  answered.
- When an answer changes the brief (the user authorises a claim, drops one,
  gives a source), dispatch explore again with the answer, save the new brief
  over the old one, and record the new plan with `plan_reason`, which raises
  the contract version.

### 3.2 Implementation: the writer

Dispatch `dispatch_job` with `role: "implementation"`, `writes: true`, and
`agent: "opencode"` plus `model` unless the implementation stage is already
fixed on OpenCode with the model you want. The assignment contains, in this
order:

1. The full text of `MOD/stages/write.md`, pasted in (it is short; a cheap
   model follows what is in its prompt more reliably than a file it is told
   to open).
2. The absolute path of the brief.
3. Nothing else of your own. Do not restate, extend or soften the brief: if
   something is missing from it, fix the brief (3.1), not the assignment.

In a round (Tinto adds the round's items to the job's instructions), add one
line: "Address only the round's items; change nothing else."

### 3.3 Review

Once the task's checks have run on the writer's result, dispatch
`role: "review"`, `agent: "claude"` (always name it unless review is fixed on
Claude: with none, Tinto picks another agent than the writer's, which need
not be Claude), with the assignment:

1. "Read and follow `MOD/stages/review.md`."
2. The absolute path of the brief.
3. The intake notes from section 1, step 2.

A review with findings opens a round: dispatch implementation again (3.2).
Two exceptions:

- A finding that says the **brief** is wrong (it authorised a claim the
  evidence does not support, it missed a needed citation) goes back to
  explore, not to the writer: dispatch explore with the finding, save the new
  brief, raise the contract version, then the writer.
- After **three** review rounds on the same task without a pass, stop and ask
  the user (section 5).

A failing check also opens a round. Tinto dispatches the first two such
rounds itself, on the writer's agent and model; later ones are yours.

### 3.4 Citations

The writer never adds, removes or edits a bibliography entry. Jobs have no
web access, so a source nobody has opened cannot be verified inside a task.

- A citation the brief authorises must already be in the repository's
  bibliography, and, where the repository keeps its audit trail (an audit
  note, a stored copy of the source), that trail must show it was checked.
- A new source the section needs is a question for the user (section 5):
  they add it by the repository's procedure, or authorise a separate
  bibliography task done by a Claude implementation job, with the user's
  notes on the source in the assignment.

## 4. What every assignment must contain

- The absolute path of the stage guide (pasted text for the writer).
- The absolute path of the brief (writer and review).
- The paper and target files, and the repository rules that apply (intake
  notes).
- For a round: nothing beyond what Tinto adds, except the line in 3.2.
- Never: your own summary of the evidence, instructions to run the task's
  checks in full (Tinto runs them), or permission to touch files outside the
  brief's Target.

## 5. When to stop and ask the user

Ask with `request_decisions` on the task, in plain language, paths in
`detail`. Ask during the batch's brainstorm whenever you can, so the batch
then runs without stopping. Ask when:

- Evidence for a claim the outline needs is missing or contradicts the plan
  (choice: drop the claim, mark it as a gap, or wait for the evidence).
- The section needs a source that is not in the bibliography (choice: the
  user adds it, a bibliography task, or write without it and mark a gap).
- Two readings of the evidence lead to different claims (choice, with each
  reading's consequence).
- The request needs a new research question, contribution or result the
  manifest does not list.
- Three review rounds failed (choice: another writer model for this task,
  Claude writes it as an implementation job, or the user takes it over).

Never ask what `read_settings` answers, and never ask through decisions what
belongs in Ajustes. Writing choices with a clear default (paragraph order
within the outline, wording of a heading) are yours: decide them in the brief.

## 6. The delivery ladder

commit, push, PR, merge. There is no issue tracker: with the mod's settings
approved (`no_jira_steps`) the ladder has no Jira step and a task is delivered
at its merge. If a task's `next_step` is a Jira step, the settings are not
approved yet: tell the user in one line rather than requesting it.

1. **Commit**: once review passed and the checks pass, request `commit` with
   the writer's `commit_plan`, corrected to the repository's commit
   convention. Usually one commit per task; the brief file is never in it (it
   lives outside the worktree).
2. **Push**: request `push`.
3. **PR**: request `pr` with a title and body that follow the repository's
   pull request template. When it has none, the body lists: the sections
   changed; the claims added or removed, each with its evidence; citations
   used; the gaps left (each gap marker and why); the build result. Never
   mention the brief, the stages, the writer model or Delivery: the PR is
   about the paper. The user picks the reviewing co-author in Tinto.
4. **Merge**: Tinto asks for it once the co-author approved and checks pass.
   Changes the co-author asks for on the PR open a round: their items go to
   the writer (3.2) or, if they change what the section claims, to explore
   first (3.1). Answer each review thread with `request_approval` `pr_reply`
   after the fix is pushed.
5. **Archive** the task with `archive_task` once delivered.

## 7. Comparing writer models

The user may ask to compare cheap models on the same section. Do it only when
they ask and name the models (`provider/model`, as `opencode models` lists
them).

- One brief, several tasks: create one task per model, keys
  `<KEY>-<model-slug>`, each with its own worktree. Run explore once, on the
  first task, and save the same brief in each task's `.agent/papers/` folder.
- Dispatch each task's writer with `agent: "opencode"` and its `model`; keep
  review on Claude for all of them. Failing-check rounds keep the task's
  model.
- Run every task to review passing, then stop before the commit. Report per
  model, from `delivery_overview` and `read_job`:
  - rounds until review passed;
  - unauthorised claims and citations the reviews found (count review
    findings of those two kinds across rounds);
  - tokens of the writer's jobs (`usage`) and, if the user gives prices, the
    cost per accepted section, writer plus Claude stages.
- The user chooses which task goes on to the ladder. Tell them to remove the
  others in Tinto: they are never committed.
