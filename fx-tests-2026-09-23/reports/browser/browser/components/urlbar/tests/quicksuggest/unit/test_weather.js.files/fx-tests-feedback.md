## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_weather.js --bugs`
- Expected: a line such as "Annotated bugs: none" so the flag's result is visible.
- Got: the ordinary output with no bugs section at all, identical to running without `--bugs`; only `--json` shows `"annotatedBugs": []`.
- Workaround: `--bugs --json | jq .annotatedBugs`.

## Question: "is this step change specific to this test, or does the whole config/component slow down?"

- Commands: `fx-tests test <path> --durations --day <d> --config <c>` run once per day per test, for four tests, in a shell loop (about 40 calls).
- What would have answered it: a per-day median duration column in `--history` (or `--durations --history`), so a slowdown window (here 2026-09-15..09-18, +40% on every Linux config for the quicksuggest tests) shows up in one call.
