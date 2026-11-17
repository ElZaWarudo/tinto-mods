# Stages: research

Two stages, both with web access. Read the section for yours. The
repository's rules (its `AGENTS.md`, its citation procedure) win over this
guide.

A source counts only when you opened it: the publisher's page, arXiv, IACR
ePrint, the RFC, the official text of a regulation or standard. A search
snippet, a matching title or another paper's reference list is not enough.
References in AI-written plans are often wrong: identifiers that point to
another paper, guessed venues and years.

## `sources`: find and verify, then write the entries

1. Read the repository's citation procedure, its bibliography file(s) and,
   if it keeps one, its audit trail.
2. For each claim or topic in the assignment, find sources that support it.
   For each reference to check, find the source itself.
3. For every source: check title, authors, venue, year and identifier
   (DOI, arXiv id, ISBN, URL) against the source; read the part that
   supports the claim and note, in one line, what it supports and what it
   does not. For a regulation or standard, use the version in force and say
   when a date comes from a proposal, not a final text.
4. Write each verified entry in the bibliography file the repository's rules
   say, from the source, with its locator (`doi`, `url` or `isbn`). Reuse an
   existing key when the source is already there; never duplicate it.
   Correct an existing entry only when the source shows it is wrong.
5. Update the audit trail where the repository keeps one, in its format.
6. Change nothing else: no paper text, no other file.

A source that does not support the claim as worded is reported with what it
does support, not stretched.

## `sources-review`: verify independently

You change nothing. Open every source behind every entry the change adds or
modifies (`git diff` against the task's base), yourself, on the web: do not
trust the `sources` stage's notes.

Each of these is one finding (file:line, what is wrong, what the source
says):

1. The source does not exist, or the identifier points to another work.
2. Title, authors, venue, year or identifier differ from the source.
3. The source does not say what the report says it supports.
4. An entry lacks the locator the repository requires, duplicates another,
   or breaks the repository's bibliography rules.
5. A file outside the bibliography and its audit trail changed.

Wording you would have chosen differently is not a finding. With no findings,
your status is `pass`.

## Output

- `summary`: one line: how many references, how many verified, corrected,
  removed (or for the review: how many checked, how many wrong).
- `handoff` (`sources` only): the report, one line per reference, exactly:
  `<key>: verified` / `<key>: corrected: <what changed>` /
  `<key>: removed: <why>` / `<topic>: no source found: <what was searched>`.
- `findings`: one line each, a defect only; no praise, no restating the
  task.
- `commit_plan` (`sources` only): one commit in the repository's convention.
