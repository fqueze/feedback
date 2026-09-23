## Question: in what order did these markers, all within a few ms after t=60s, happen?

- Command: `profiler-cli thread markers --session test_protocol_stack.js-1 --search test_protocol_stack --list --limit 0`
  (then `zoom push 98.299,98.304` + `thread markers --list --category Other`)
- Expected: list timestamps precise enough to order markers and to zoom on them (e.g. `t=98.299s` or `t=1m38.299s`).
- Got: every marker past one minute prints as `t=1m38s`; the log-replay Begin/ERROR/End markers of 20 tests, spread over 20 ms, were indistinguishable, and so was the 18 ms gap between a test's start and its "will retry" (shown as 53.280s vs 53.298s only because it was below 60s).
- Workaround: `marker info m-4 m-5 m-2 --json` to read `start`, then zoom on those ms values.
- What the default output could show: sub-second precision in `--list` for times >= 60s (the zoom is 5 ms wide, and the header says so).

## Question: which instant markers fall inside a 5 ms window?

- Command: `profiler-cli zoom push 98.299,98.304` then `thread markers --list --limit 0`
- Expected: the markers that happened in the window.
- Got: 869 markers, led by every long `test`/`Phase` interval marker overlapping the window (hundreds of TIMEOUT tests that started 45 s earlier), burying the ~20 instants that happened there.
- Workaround: `--category Other`, which happened to exclude the `test` markers (category Tasks).
- What would help: a `--starting-in-range` / `--instant` option, or sorting instants first, so "what happened in this window" does not need guessing a category.

## (review) Question: which tests that logged "will retry" never got a TIMEOUT `test` marker?

- Command: `profiler-cli thread markers --search 'will retry' --list --limit 0 --json` and `--search name:test --list --limit 0 --json`, then a Python join on the test id.
- Expected: a way to see, for a set of log messages naming tests, which of those tests have a `test` marker and with what status.
- Got: two unrelated lists; the answer (312 tests failed before the kill timer, with no TIMEOUT) needed a script.
- What could show it: a per-test view (`thread tests`?) listing each test id with its `test` marker status and the log markers naming it, including tests with a test_start and no test_end.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session review-x-1 thread markers ...`
- Got: `error: unknown option '--session' (Did you mean --version?)`. It only works after the subcommand.
- Expected: a global option accepted in either position.

## (review) `--search` does not match the displayed field labels of CPU Use markers

- Command: `profiler-cli thread markers --search 'CPU Percent' --list` (the text the report quotes, as shown in the UI tooltip)
- Got: no markers. `--search 'CPU Use'` (the marker name) matched; the fields are keyed `cpuPercent`, `idle_pct`.
- What could help: say in "No markers match" that labels are not searched, or match labels too.
