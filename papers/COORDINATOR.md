# Papers: coordinator guide

Start from the researcher's choice in **Flujo de Papers** on Tinto's
coordinator start form. Use the corresponding `create_task` `flow` for
each new task. Delivery flows produce one pull request a co-author reviews.

| Choice | `flow` and stages | Deliverable |
|---|---|---|
| Redacción | `section` · `brief` → `write` → `prose-review` | a section or revision |
| Borrador para revisión | `draft` · `brief` → `write` → `prose-review` | a local draft, with its saved PDF when it builds |
| Experimento y redacción | `experiment` · `prepare` → `run` → `analysis` → `brief` → `write` → `prose-review` | raw results, reproducible analysis and a written section or results report |
| Protocolo | `protocol` · `protocol` → `methods-review` → researcher approval | a preregistered experiment protocol |
| Investigación de fuentes | `research` · `sources` → `sources-review` | verified bibliography entries |

Claude scopes the work with a brief grounded in evidence, OpenCode runs
the selected writer model, and Claude Opus reviews by default. Tinto runs
the repository's checks and handles the PR.

This guide sits on top of Tinto's coordinator instructions and tool
descriptions: where they say how a tool works, they win. `MOD` is the
absolute path of the folder that holds this file; every job can read it.

## 1. How you talk to the researcher

The researcher reads everything you send. Do not saturate them.

- **Lead with what you need from them, or with the verdict.** One line.
- **One decision per question.** `question`: one line, at most 15 words.
  `choice`: 2 or 3 options, each consequence one line, one recommended with
  its `why` in at most two lines. Everything else goes in `detail`, which
  Tinto folds.
- **Never paste a brief, a protocol, a log or a diff** into `question` or
  `text`. The path, or the whole document, goes in `detail`.
- **Ask early, together.** A paper's questions go in the brainstorm, one
  `request_decisions` per task, at most three blocking ones per task. Never
  ask what `read_settings` or the repository answers; decide yourself what
  has a sound default (wording, paragraph order, file names).
- **Checkpoints** are one blocking `text` decision with `waits_for_user:
  true`, of at most 15 lines. Use them for the reviewed essentials of a
  protocol (5.2) and for a deviation that changes a hypothesis, measure or
  analysis plan (5.3).
- **No status chatter.** Tinto shows progress. Write in the conversation only
  what no decision, approval or record can carry, in at most three lines,
  then `call_user` once.
- What the user reads (decisions, records, messages) is in the language they
  write to you in. Repository files, commits and pull requests are in the
  repository's language.

## 2. Intake: the repository's rules win

Your first message carries one line per answered start question after
Tinto's hand-over paragraph. Map `Flujo de Papers` to the exact flow in
the table, use `Artículo` to locate the paper manifest, and use the optional
`Encargo` to identify the section, experiment, subject or Jira issue that
supplies the work. `Modelo de redacción (OpenCode, provider/model)` is an
optional override for this batch. If a conversation already named a flow,
use that choice when starting without form answers. If no flow is clear,
ask before creating tasks. For an existing task, keep its saved flow
and stage sequence when resuming.
Work with no paper manifest (a thesis chapter, a standalone document) is
named in `Encargo`: `draft` for a local draft to review, `section` when it
goes to a pull request; ask which when the request does not say. Its outline
and the sources `Encargo` names replace the paper plan, the brief's Manifest
line is `None`, and the brief lives in `.agent/papers/<KEY>/` as for any
task. For other flows, resolve the paper from the selection or the request.

1. Read the repository's `AGENTS.md` (and `CLAUDE.md`), `README.md`, every
   procedure they point to (skills, templates, schemas, the pull request
   template), its build file, and, when there is a paper, its own guide,
   manifest and plan. When the `Artículo` line names a paper without giving
   its manifest path, resolve it by id or title. If a paper is required and
   the request leaves its identity unclear, ask one focused question.
2. Write down the repository's answers to these. They are the **intake
   notes** you copy into every assignment:
   - the paper's files, and the files nobody may edit (raw results, other
     papers);
   - how a claim cites its evidence (for example a `% evidence:` line above
     it) and what counts as evidence (repository paths,
     `owner/repo@commit:path`, completed experiments only);
   - where the bibliography lives, the rule for a new entry, and the audit
     trail it keeps (a file, or a report in the pull request);
   - the gap marker (for example `\todo{...}`);
   - where experiments live, their template, manifest and statuses, and the
     rules for preregistration, raw results and deviations;
   - the paper manifest's plan fields (`stage`, `next_action`, `blockers`,
     `experiments`, or their equivalents) and when they are updated;
   - branch names, commit convention, pull request template, who reviews;
   - the commands that check the repository and build the paper, and the
     file the build produces (for example `papers/<dir>/build/main.pdf`).
3. **Where the repository and this guide disagree, the repository wins.**
   Follow it and tell the user in one line.
4. `read_settings`, then propose what is missing in one `propose_settings`
   during the brainstorm:
   - `checks_add`: the repository's check command and, when the task changes
     the manuscript or has a document build, that document's build,
     exactly as the repository runs them (for example `make check`,
     `make p02`); `setup_add`: the install the check needs, adapted to the
     system the jobs run on (`python3` where there is no `python`).
   - The launch model override applies to `write` only. Pass
     `agent: "opencode"` and that `model` on each writer dispatch. With no
     override, use the repository's stage settings. If the stage is unset,
     dispatch with the agent and model from `MOD/mod.json`. If the stage
     uses the same agent as the mod but has no model set, pass the mod's
     model when it names one. This also covers settings from older Papers
     versions. If OpenCode rejects the model, ask for an available
     `provider/model` and resume at `write`.
   - Check the issue source before proposing `no_jira_steps` (section 7).
5. **LaTeX needs Linux.** The bootstrap installs TinyTeX for Linux x86_64. If
   the repository is not in WSL or on Linux (its path in `read_settings`),
   tell the user in one line that the PDF cannot be built until it is, and
   leave the build out of the checks.
6. Make sure `.agent/` is ignored in the main checkout: if
   `git check-ignore .agent/x` prints nothing, add `.agent/` to the file
   `git rev-parse --git-path info/exclude` names. Never to a committed file.
7. **Sources a stage can open.** `brief` and `prose-review` have no web
   access. Before `brief`, check that every source the prose must cite (a
   regulation, a standard, an RFC, vendor documentation) is in the
   repository where a stage can open it. If one is not, a `research` task
   first, or tell the user in one line that it will be a gap.
8. **Pinned commits.** For each code repository the prose describes, compare
   the commit you would pin with the tip of its default branch. When it is
   behind, say so in one line (how many commits) and ask which to pin.

## 3. Cutting the work into tasks

Use the selected flow, the request and the paper's plan (`next_action`,
`blockers`, `experiments`) to identify the work. Create a task directly
when the target is clear. Ask when there are several plausible targets or
the work needs a split that changes what the user asked for.

- `research`: the sources one section or one topic needs.
- `protocol`: one experiment's preregistration.
- `experiment`: one preregistered experiment and the prose it supports.
  When a protocol task already exists, block the experiment on its merge.
  When it still needs to be designed, ask the user to start with Protocolo.
- `section`: one section, or one coherent revision. Never two papers in one
  task, never one section across tasks.

Keys are the paper's id plus a slug (`P02-threat-model`, `P02-bench-protocol`,
`P02-bench-run`), or the document's short name plus a slug without a paper
(`THESIS-ch2-intro`); `branch` follows the repository (`paper/p02/threat-model`).

**Adopting existing work.** A draft written without a brief is the current
text of a revision: the brief revises it, never takes it as a contract. A
task created without a flow takes one with `set_task_state` `flow`: its
`next_stage` then follows that flow, counting the stages it already passed.

**Order.** A task waits for what it uses, with `set_task_blocked`
`{"type": "task", "task": "<key>", "until": "merged"}`: an experiment for its
protocol, or a section for evidence from other tasks; the
abstract, introduction, discussion and conclusion for the sections they
summarise; two tasks that edit the same file, one for the other. When a
blocked task unblocks, request a `sync` before its first stage so its branch
has what merged. Work whose evidence does not exist and that no task will
produce is not created: tell the user what it waits on.

## 4. Every task

- Before `create_task`, create the empty folder
  `<main checkout>/.agent/papers/<KEY>/` and pass it in `read_dirs`: it holds
  the brief you store.
- `create_task` with `flow`, `branch`, `write_paths` and `repos` (per flow,
  below). Give jobs each checkout's `path` as the task's `repos` shows it;
  never assume it.
- `set_task_state` with the repository's `checks` and any `setup` they
  need. For prose, also set the document build in `checks` and its PDF in
  `documents` when the deliverable has a build. Verify both are set on the
  task before dispatching `write`: approving repository settings alone
  does not select them. On an existing worktree, confirm the compiler is
  available where jobs run; a bootstrap added later does not prepare it:
  for a worktree created before the repository's bootstrap was set, propose
  the bootstrap command as `setup_add` and set it in the task's `setup`.
  When the build or its prerequisites are missing, surface the specific
  blocker and settle the setup in Tinto. A PDF compiled by the writer does
  not count as a saved Tinto version.
- A check that fails because a program is missing (exit 127, `command not
  found`) is a setup problem, not a defect of the work: propose the missing
  setup with `propose_settings` and set it on the task. Never dispatch
  `write` for it.
- Every assignment opens with "Read and follow `MOD/stages/<file>.md`,
  section `<stage>`." (absolute path), then the task, then the intake notes.
  Never your own summary of the evidence, never "run the checks" (Tinto runs
  them), never permission to touch a file outside `write_paths`.
- The flow gives each task's `next_stage`. Route findings as described
  below, and use the task's flow for subsequent stages.
- After **three** rounds on a task without a pass, stop and ask the user.

## 5. The flows

### 5.1 `research`

`write_paths`: the bibliography file(s) and the audit trail files the
repository keeps. `sources` and `sources-review` (guide `stages/research.md`)
get what the sources must support, in the user's words, and the references
to check (those in the paper's plan, if the user asks: plans written with AI
often cite wrong ones). Both have web access.

The pull request body follows the template and adds `sources`' report: one
line per reference, verified / corrected (what changed) / removed (why).

### 5.2 `protocol`

`write_paths`: the experiment's folder (for example
`research/experiments/EXP-P02-bench/**`) and the paper's manifest. `repos`:
the code repository at the commit the paper's manifest pins, so the protocol
is designed on the code it will run.

1. `protocol` (guide `stages/protocol.md`): the experiment's question and
   the code repository's checkout path (from the task's `repos`). It designs
   the experiment, fills the repository's template, writes the run entry
   point and lists the experiment in the paper's manifest. Its `handoff` is
   the Essentials plus any Questions for the user: ask those (section 6)
   while the review runs.
2. `methods-review`: the same inputs. Findings go back to `protocol`.
3. **Checkpoint**, once the review passed: the Essentials from its
   `handoff`, the protocol's path in `detail`. For example:

   ```
   Experimento EXP-P02-bench, para la RQ2 de P02:
   1. H1: firmar con ML-DSA-65 cuesta menos del doble que con ECDSA P-256 (se refuta si la mediana llega a 2×).
   2. Medida: latencia por firma, de latency.csv de cada ejecución.
   3. Muestra: 30 repeticiones de 1000 firmas, para un IC 95 % de ±5 %.
   4. Análisis: mediana con IC bootstrap; ninguna otra prueba.
   5. Se detiene tras 3 ejecuciones fallidas seguidas.
   Comando: make -C research/experiments/EXP-P02-bench run (unas 2 h).
   Si lo apruebas tal cual, se abre el PR; si cambias algo, se rehace el protocolo y se revisa otra vez.
   ```

   An answer that changes anything sends it back to `protocol` with the
   answer, then `methods-review`, then this checkpoint again.
4. Propose the run command with the checkpoint, so the user approves it with the protocol:
   `propose_settings` `qa_commands_add`, exactly as the protocol writes it.
   A command stage runs only a command of the repo's settings, and those may
   not run a script by relative path, name an absolute path or use `..`:
   that is why the protocol puts the run behind the repository's task runner.

The pull request body adds the essentials in a few lines. It merges before
any run.

### 5.3 `experiment`

Create it with `repos`: `[{"repo": "<code repository>", "commit": "<the full
commit the protocol pins>", "role": "code the experiment runs"}]`. Before
`prepare`, check that the protocol is merged on the base and its run command
is in `read_settings` `qa_commands` (else propose it as in 5.2 step 4).

`write_paths` follow the stage, so only Tinto's run writes raw results:

| Before dispatching | `write_paths` (`EXP` is the experiment's folder) |
|---|---|
| `prepare` | `EXP`'s manifest, protocol and run entry point |
| `run` | `EXP/results/**` |
| `analysis` | `EXP`'s manifest, `EXP/analysis/**`, and the paper's manifest if required |
| `write` | the brief's Target files |
| the commit | the combined lists |

1. `prepare` (guide `stages/experiment.md`): the experiment's folder and the
   code repository's path, commit and role.
2. `run`: `dispatch_job` with `role: "run"`, `command` exactly as in
   `qa_commands`, and `timeout_minutes` from the protocol's estimate plus
   half (at most 1440). Tinto runs it; no agent touches its output. If it
   fails, read `job_log`: a missing tool or service is the user's (one
   line); anything else goes to `prepare` as a deviation.
3. `analysis` (guide `stages/experiment.md`): the run folder to analyse.
   Use its successful handoff to give `brief` the protocol, exact run folder,
   completed manifest and analysis outputs in the same worktree. Treat any
   missing or inconsistent output as an experiment issue before scoping prose.
4. Continue with the shared prose stages in 5.4. Give `brief` the manuscript
   section or results report named in the request or paper plan. Use the
   repository's report convention when there is no manuscript target, or
   `EXP/analysis/results.md` when the repository has no convention. Cover
   each hypothesis, its result against the criterion, and any deviations.

Route an editorial finding to `write`, or to `brief` if it changes the
scope. Route an incorrect computation or figure back to `analysis` and
refresh the brief before the prose is revised and reviewed. For a methodology
error or a change to the experiment, dispatch `prepare` to document the
deviation, then `run` in a new run folder. Earlier runs stay untouched.
If the deviation changes a hypothesis, a measure or the analysis plan,
get the checkpoint from 5.2 after `prepare` and before `run`. Repeat the
downstream stages with the new evidence. Apply the same routing to findings
from the co-author and use 5.3's write paths for each dispatched stage.

The pull request includes the prose and its underlying evidence together.
Add one line per hypothesis with the observed value and whether it met the
criterion, and list the deviations.

Existing tasks whose saved flow ends in `results` finish that stage using
`stages/experiment.md` and Claude when there is no stage setting. To write
prose from such a run after it merges, start a Redacción task with the run
as evidence.

### 5.4 Shared prose stages

For `section` and `draft`, attach the code repositories the prose describes at
the commits its manifest pins or the researcher requests. When a presentation
or other older source supplies the argument, attach it separately and label
its purpose. For claims about the current implementation, pin and attach the
current agreed branch tip as implementation evidence, even when both inputs
come from the same repository. Give `brief` and `prose-review` both checkout
paths and commits. For `experiment`, use the run and analysis from
the preceding stages of the same task. For either flow, set `write_paths`
from the brief's Target files before `write`. Include the paper's manifest
only when the brief assigns a change to it. Expose the PDF in `documents`
when the prose is part of a document the repository builds.

1. `brief` (guide `stages/brief.md`): the section, revision or report, the
   paper and its manifest, the intake notes, and for a revision what must
   change and why, with paths and lines. That is the critique that motivates
   the revision, from the user, a supervisor, a co-author or an external
   reviewer: ask for it in the brainstorm when the request does not carry
   it. With none, say so in the assignment: the brief's design check is then
   the critique, and what it cannot fix comes back as questions. Never tell
   `brief` to keep the question, objectives or thesis statement unchanged
   unless the user said so. Its `handoff` is the brief: save it as
   `.agent/papers/<KEY>/brief.md`; if a template section is missing, dispatch
   `brief` again naming it. Record `set_task_state` `plan` from it (`what`
   from Task, `places` from Target, `risks` from Gaps, `out_of_scope` from
   Forbidden), with `plan_reason` when replacing an existing plan. Ask every
   question in its `findings` with `request_decisions` (section 1) before
   dispatching `write`, with the brief's option as the recommended one;
   never replace an answer with that option silently. Where section 1 lets
   you decide yourself, record the option you take as a non-blocking
   decision. Update the brief if answers change it.
2. `write`: the **full text of `MOD/stages/write.md` pasted in** (a cheap
   model follows its prompt better than a file) and the brief's absolute
   path. Nothing else of yours: if something is missing, fix the brief. In a
   round add one line: "Address only the round's items; change nothing
   else."
3. `prose-review` (guide `stages/review.md`): the brief's path and the
   intake notes, plus the protocol, raw run and analysis paths for an
   experiment. Never dispatch it, or any review stage, while a round opened
   by failing checks is open on the task: fix the cause and `run_checks`
   until it closes. Say in the assignment which review it is: the first
   review of a text is always the full checklist; only a later one is a
   "re-review in a round", with the round's items. Findings go back to
   `write`; one that starts with "Brief:" goes to `brief` first, then
   `write`. Findings about the experiment itself follow 5.3.

Changes a co-author asks for on the pull request go to `write`, or to `brief`
first when they change what the section claims.

## 6. When to stop and ask

Ask, as section 1 says, when:

- evidence a claim needs is missing or contradicts the plan (drop the claim,
  mark a gap, or wait for an experiment);
- a section needs a source that is not in the bibliography (a `research`
  task first, or write without it and mark a gap);
- two readings of the evidence lead to different claims (each with its
  consequence);
- the work needs a research question, contribution or result the manifest
  does not list;
- an experiment needs data that may not be published or a tool the system
  lacks, or its results look wrong before analysis;
- three rounds failed (another writer model, Claude writes it as `write`
  with `agent: "claude"`, or the user takes it over).

## 7. Delivery

For `draft`, stop before commit and push. Confirm the PDF appears in
`delivery_overview` `documents` after Tinto's checks and report the draft
path, PDF version and editorial verdict to the researcher. Report a missing
saved version or a failed review separately from a successful build. Keep
the task and its worktree available for the next revision. Resume delivery
only if the researcher asks for it.

For work taken from the article plan or requested directly, use
`no_jira_steps: true` so the merge completes delivery. If missing, include
it in the settings proposal. Only use Jira when the user gives a Jira issue
as the work source. Keep its issue key and follow Tinto's Jira steps for
that task. If `no_jira_steps` is active for a Jira-sourced batch, propose
`false` during intake. Tinto applies this setting to the whole repository,
so flag the impact if other batches in it are still delivering.

1. **Commit** once the last stage passed and the checks pass, in the
   repository's convention. For experiments, restore the combined write
   paths before committing. Briefs stay in `.agent/`.
2. **Push**, then **PR**: the repository's template, filled for this task,
   plus what section 5 adds per flow. Never mention briefs, stages, models
   or Delivery: the pull request is about the paper. The user picks the
   reviewing co-author in Tinto.
3. **Merge** once the co-author approved. Answer each review thread with
   `pr_reply` after its fix is pushed.
4. **Archive** the task once delivered.

When the repository's rules say a delivered task moves the paper on, the
task's last writing stage updates the manifest's plan fields. For prose,
give the exact fields and values in the brief's Manifest line.

## 8. Comparing writer models

Only when the user asks and names the models (`provider/model`, as
`opencode models` lists them). One task per model, keys `<KEY>-<model-slug>`,
the same brief saved in each task's folder. Run
`brief` on the first task; on the others, so their flow counts it, dispatch
`brief` with "Return the brief at `<path>` verbatim as your handoff; change
nothing." Each `write` gets `agent: "opencode"` and
its `model`; `prose-review` stays on Claude. Use the same pinned sources,
document path and build check across the tasks so Tinto can show their PDFs
in the same history. Run each to a passing review, stop before the commit,
and report each model's saved version, rounds to pass,
unauthorised claims and citations the reviews found, the writer's tokens
(`usage`) and, if the user gives prices, cost per accepted section. The user
chooses which task goes on; the others are removed in Tinto.
