## Question: are the fast passing runs the harness retries?

- Command: `fx-tests test browser/components/preferences/tests/home/browser_homepage_firefox_home.js --durations --day <d> --config a11y-checks`, then `--executions`.
- Expected: durations split into first-run vs harness-rerun passes (the pass durations here are bimodal, ~15-20s and ~35-45s, and the split is the whole story: in manifest order the test is slow, rerun alone in a fresh browser it is fast).
- Got: one distribution; `--executions` says the aggregates record no order.
- Workaround: loaded a job's resource-usage profile and read the two `test` markers (1m4s FAIL, then 17.2s PASS), and matched the count of fast runs to the count of failures per day.

## Question: which tests ran before this one in the same browser, and did that change?

- The step on 2026-09-15 came from a manifest merge (bug 2049027) that moved the test from 9th to 22nd in its browser session. `fx-tests test --history` shows the step but nothing about the test's position/manifest per day; a "manifest and index within it" column per day would have shown it directly. Found with `git log` on the manifest.
