# Tinto mods

Mods for [Tinto](https://github.com/ElZaWarudo/tinto)'s Delivery: flows that
take a batch of work from a branch to a merged pull request, each one shaped
for a kind of work.

Delivery always runs the same engine: one worktree and branch per task, jobs
by stage (scope, implementation, review...), Tinto's checks, rounds when work
comes back, and the approvals for commit, push, PR and merge. A **mod** does
not change that engine. It is instructions for the agents that run it plus a
few Delivery settings:

- a guide the coordinating agent reads before it starts a batch: how to cut
  the work into tasks, which agent runs which stage, what each job is told,
  when to stop and ask you;
- the stage guides that guide points the jobs to;
- the settings the mod needs, which Tinto proposes to you when you choose the
  mod for a repository. Nothing changes until you approve them.

A mod is never code. Tinto runs nothing from it, and its work is always a
diff you review in a git repository.

This repository is a **catalog**: a git repository with one mod per folder.
Tinto ships with it already added.

## Using it in Tinto

1. Open **Complementos**. This catalog is listed by default; if you removed
   it, add it again with its URL, `https://github.com/ElZaWarudo/tinto-mods`.
2. Refresh the catalog and install a mod.
3. Choose the mod for a repository in its Delivery settings. If the mod has
   settings, Tinto opens a proposal with them; approve the ones you want.
4. Start a batch as usual. The coordinator reads the mod's guide before it
   creates anything.

Mods never update by themselves: when this catalog has a newer version,
Complementos says so and you update it.

## Mods

| Mod | What it does |
|---|---|
| [`papers`](papers/) | Takes an academic paper from its sources to its PDF, in four flows. **Research**: Claude finds and verifies sources on the web and writes the bibliography entries. **Protocol**: Claude designs and writes an experiment's protocol, an independent methodology review checks it, and you approve its essentials before it is preregistered. **Experiment**: Tinto runs it on the code repository at a pinned commit, Claude analyses it, and you read the results per hypothesis in a record where you can report problems; a correction is a new run, never an edit. **Section**: Claude writes a brief, you see what will be written, a cheap OpenCode model writes it and Claude reviews it; you open each version of the PDF in Tinto. Each task ends in a pull request a co-author reviews. It installs LaTeX (TinyTeX) by itself in WSL. |

## Building your own mod

### Format

A mod is a folder whose name is its id (lowercase letters, digits and `-`):

```
my-mod/
  mod.json          the manifest
  COORDINATOR.md    the guide the coordinating agent follows
  ...               any other files the guide points to (stage guides, templates)
```

`mod.json`:

```json
{
  "id": "my-mod",
  "name": "My mod",
  "version": "0.1.0",
  "description": "One line, shown in Complementos.",
  "min_tinto": "2.1.0",
  "coordinator": "COORDINATOR.md",
  "settings": {}
}
```

Only `id`, `name`, `version` and `coordinator` are required; the table below
lists the rest. `stages`, `flows` and `start` need Tinto 2.1.0.

| Field | Meaning |
|---|---|
| `id` | Must equal the folder's name. |
| `name` | The name Complementos shows. |
| `version` | The mod's version. Raise it with every change you publish: an installed mod shows an update when it differs. |
| `description` | One line for Complementos. |
| `min_tinto` | The oldest Tinto that can run it. |
| `coordinator` | The guide's path, inside the folder. |
| `icon` | Optional. An `.svg` or `.png` inside the folder, at most 64 KB, shown only as a picture. |
| `settings` | Optional. Delivery settings proposed to the user when they choose the mod, with the same fields a coordinator's `propose_settings` takes (for example `checks_add`, `setup_add`, `bootstrap`, `verification`, and `stages` keyed by stage id). They never apply by themselves. |
| `settings_reason` | Optional. One or two plain lines shown with that proposal: what the settings do, and for a tool they install, what it is, where it goes and how big it is. |
| `stages` | Optional. The mod's own stages, by id (`[a-z0-9-]+`, not a built-in role such as `review` or `qa`): `{"label": "…", "as": "…", "web": true}`. `as` is the built-in behaviour it has: `explore` (reads, its findings are questions), `implementation` (writes), `review` (reads, findings send work back), `qa` (writes a record the user reads and reports problems on) or `command` (Tinto runs one of the repository's allowed commands; it passes on exit 0). `web` gives its jobs web access. |
| `flows` | Optional. Ordered lists of stages a task can be created with: `{"label": "…", "stages": ["…"]}`, mixing the mod's stages and the built-in `explore`, `tests`, `implementation`, `review`, `qa`. The next stage is the first without a passing attempt; work sent back goes to the nearest earlier stage that writes. |
| `start` | Optional. Up to five questions shown as a short form when the user starts a batch: `{"id", "label", "options"}` (a fixed choice), `{"id", "label", "from"}` (a choice among the repository's files matching a glob) or free text, with `"optional": true` when it may be left empty. The coordinator receives the answers as its first message. |

Tinto ignores fields it does not know. A manifest that does not parse, or
whose paths leave the folder, makes that mod invalid: it is listed with its
error and cannot be installed, and the other mods are not affected.

### Writing the guides

- Tinto tells the coordinator where the mod's guide is; every job of the
  repository can read the mod's folder, so the guide can send a job to a stage
  file by its absolute path.
- Write for an agent that has never seen your mod or the repository it works
  on: concrete and in order. Agents read the guides, so write them in English.
- Use Delivery's tools as Tinto describes them (the `tinto-delivery` MCP
  server's instructions and tool descriptions). A guide adds a way of working
  on top of them; it cannot change how they behave.
- Let the target repository's own rules (its `AGENTS.md`) win over the mod.
- Put no secrets, private names or project-specific content in a public mod.

### Testing it from a local folder

A catalog is any git repository Tinto can clone, a folder on your disk
included:

1. Make a git repository with your mod's folder at its root, and commit it:
   Tinto installs what is committed, not what is in the working tree.
2. In Complementos, add the folder's path as a catalog.
3. Install the mod and choose it for a test repository.
4. After each change, commit, raise `version`, refresh the catalog and update
   the mod.

### Your own catalog

Any git repository with mod folders at its root is a catalog (folders without
a `mod.json` are ignored). Add its URL in Complementos; Tinto clones it with
your git credentials, so a private repository works too. When two catalogs
have a mod with the same id, the installed one keeps it and the other is shown
as a conflict.

## Contributing a mod

1. Fork this repository and add your mod's folder at its root.
2. Test it from a local folder, as above, on a real repository.
3. Open a pull request that says what kind of work the mod is for, which
   agents it expects, and what you tested it on.

Changes to an existing mod raise its `version`.

## License

GPL-3.0. See [LICENSE](LICENSE).
