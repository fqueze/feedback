## Question: the task IDs and profile URLs of this test's occurrences on one platform (Windows) of a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --task-ids --profiles --limit 0`
- Expected: `--limit 0` to list every task and profile of the row (21 jobs, 7 configs).
- Got: the row still ends with `... 16 more profiles` / `... 10 more tasks`; `--limit 0` does not expand the per-row task and profile lists. `--config windows` is refused for `try`. Without `--all-jobs`, the Windows jobs (green, test passed on retry) are absent entirely, so the default view showed 5 configs / 15 failures while `--test ... --all-jobs` showed 7 configs / 21.
- Workaround: `--json` and a Python script over `knownIntermittents[].taskIds` to find the Windows task IDs.
- Could have shown: with `--limit 0`, the full task and profile list of each row, each profile next to its job name.
