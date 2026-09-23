## `try` calls a 6-of-6 failure on its only config a "new intermittent", and shows no message for it

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`
- Expected: `browser_styleeditor_media_sidebar_sourcemaps.js` under PERMA-FAILS for `test-linux2404-64/opt-mochitest-devtools-chrome-a11y-checks-5`. It failed every execution there (3 jobs, first run and retry each; `--test ... --all-jobs` agrees: 3 jobs, 0 passed), and the failure message printed on the row.
- Got: it is listed under NEW INTERMITTENTS as `6/7`, with no failure message on the row. The 7th "run" looks like task `e6fPXv0dTUWlpLrNpuNcMw` run 0, a `worker-shutdown` exception that has no profile. `fx-tests task e6fPXv0dTUWlpLrNpuNcMw` (with `.0` assumed) then exits 4 for that run.
- Workaround: `fx-tests try <rev> --test <path> --all-jobs` for the per-config counts, then `fx-tests task <id>.1` for the message.
