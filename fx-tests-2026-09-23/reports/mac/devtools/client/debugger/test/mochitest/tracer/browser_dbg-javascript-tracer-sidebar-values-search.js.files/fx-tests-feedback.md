## Question: per-config runs and failures before a fix landed, vs after

- Command: `fx-tests test <path> --since 10 --coverage --limit 0` answers "after" (2026-09-12 … 09-21). For "before" (09-01 … 09-11) there is no `--until` / `--before` / date range, so I ran `fx-tests test <path> --day <d> --coverage --limit 0` eleven times and summed the macOS rows by hand.
- Expected: a date range (`--from 2026-09-01 --to 2026-09-11`, or `--until`) so one call gives the pre-landing per-config runs and failures.
- Also: `fx-tests test <path> --since 10 --json` returns `"configs": []` when the window has no failure, so the JSON doesn't give per-config run counts either; only `--coverage` has them.
- Workaround: one `--day` call per day.
