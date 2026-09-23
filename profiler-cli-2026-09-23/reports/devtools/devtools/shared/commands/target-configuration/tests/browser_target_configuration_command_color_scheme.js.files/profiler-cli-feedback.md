## Question: when did this content process start? (review)

- Command: `profiler-cli profile info --all --session review-cs-1`
- Expected: each process's start (and end) time in seconds, e.g. `p-5: https://example.org (2/2) [pid 1748] 2.469s → 2.819s`.
- Got: `p-5: https://example.org (2/2) [pid 1748] [ts-S → ts-w]`: timestamp handles, not times. Checking the report's "started at t=2.469" needed `profile info --json` and a script walking it for `startTime`.
- Workaround: `profile info --json | python3 ...` looking for objects with `pid` and `startTime`.
