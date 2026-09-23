## Question: "the most recent failing tasks of this test"

- Command: `fx-tests test <path> --task-ids --limit 12`
- Expected: the newest failing tasks (recent tasks have live artifacts and match current code).
- Got: the 12 oldest, from the first day of the window (2026-09-01); the newest are behind `… 800 more`.
- Workaround: `--task-ids --limit 0 | tail -15`. `--day <date>` would also work.
- Could have shown: newest first, or a `--sort recent` option.
