## `--since` takes a day count, not a date

- Command: `fx-tests test <path> --since 2026-09-17 --coverage`
- Expected: runs since that date (the `--history` output is by date, so a date is what one has in hand).
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-17"`.
- Workaround: `--since 5`, counting days back from the window end by hand.

## Question: "which landing stopped this failure?"

- Command: `fx-tests test <path> --config android --history`, then `fx-tests test <path> --task-ids` for the revisions of the last failures.
- Expected: the revision of the last failing run and of the first runs after it, per config, so the stopping landing can be bracketed (e.g. "last failure at autoland X; N passes on revisions after Y").
- Got: per-day counts only; revisions exist only for failing tasks (via `fx-tests task`), so the window after the last failure cannot be bounded by revision.
- Workaround: bracketed by time (last failing push time vs. the candidate landing's push time) and git history.
