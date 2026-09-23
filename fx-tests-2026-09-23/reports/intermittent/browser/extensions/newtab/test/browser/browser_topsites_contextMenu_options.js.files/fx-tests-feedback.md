## Question: did test X run before the failing test in every failing job of one failure mode?

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 1`, then for each of the 46 tasks
  `fx-tests task <id> --passed --limit 0 --json | jq '[.passed[], .failures[]] | map(select(.path|test("browser_newtab_towindow.js")))'`
- Expected: a way to ask "in the failing jobs of issue N, which tests ran earlier in the same
  browser session" (e.g. `fx-tests test <path> --issue 1 --preceded-by` listing the preceding
  tests with the fraction of failing jobs each appears in), so a leaker candidate can be
  confirmed or ruled out across all failures without a loop.
- Got: only per-job output; `--passed` mixes PASS and SKIP in the text output (a first pass of the
  loop counted `browser_open_tab_focus.js`, which is `run-if` mac/win, as run on Linux), and has
  no order or timestamps, so "before" has to be inferred from manifest order.
- Workaround: a shell loop over the 46 task ids reading `.statuses` from the JSON.
