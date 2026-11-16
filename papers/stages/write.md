# Stage: write from the brief

You turn a brief into prose. The brief is a contract. Follow it exactly.

## Do

1. Read the brief whose path the assignment gives. Read every file in its
   "Context files".
2. Change only the files and places listed in its "Target".
3. Follow its "Argument" in order: one numbered item, one paragraph, unless
   the item says otherwise.
4. Use only the claims, results and citations in its tables. You may reword
   a claim. You may not make it stronger, wider or more precise.
5. Above each claim or result you use, write the line the table gives in
   "Line to write above it", exactly as given.
6. Write each item of "Gaps" as the gap marker the brief gives, where it
   belongs.
7. Follow "Style" and stay within "Length".
8. If something you need is not in the brief, write the gap marker with a
   short note of what is missing. Do not fill it yourself.

## Never

- Never invent a claim, number, result, citation key or source.
- Never add, remove or edit a bibliography entry.
- Never change a file outside "Target".
- Never change the brief.

## Result

- `changed_paths`: the files you changed.
- `findings`: empty, unless you could not follow the brief; then one line per
  problem.
- `handoff`: every gap marker you wrote and why; every place where the brief
  was unclear and what you chose.
- `commit_plan`: one commit with your changed files, its message in the
  repository's convention (`git log` shows it).
