## Question: "what was this one test's outcome in this job?"
- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles` (then grep for the test path)
- Expected: a way to ask for one test's row, e.g. `--test <path>` / `--search <path>`.
- Got: the FAILED list truncated to the first ~20 of 1154 tests (it does say `… N more (--limit 0 for all)`), so the test was not in it; had to re-run five tasks with `--limit 0` and grep ~700-line outputs.
- Workaround: `--limit 0` and grep.
