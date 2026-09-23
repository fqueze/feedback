## `marker info` prints times at minute resolution late in a profile

- Command: `profiler-cli marker info m-466 --session <s>` (marker at ~470.2 s in an 8-minute profile)
- Expected: a start time and end time precise enough to reuse (`t=470.215s`, duration 8.274 s).
- Got: `Time: 7m50s`, and `--list` rows also say `t=7m50s`; I needed `--json` and a script to get `start: 470214.57`.
- Question it could not answer: "exactly when did this marker start and end", to compare with other threads' markers.
- Workaround: `marker info --json`, or `zoom push m-N` which takes the precise range.

## (review) No way to list only the markers named `test`

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0` on the parent main thread of a mochitest per-test profile.
- Expected: the 49 `test` markers, one per test run in this browser, with their PASS/FAIL label.
- Got: 9,955 markers, mostly `Runnable` ones whose `name` payload field contains "test" (`AsyncExecuteStatements…`); the documented `name:` caveat. `--category Test` still mixes them with 4,400 log markers.
- Question it could not answer: "which tests ran before this one in the same browser, and did each pass".
- Workaround: `--category Test --list --limit 0 --json` and a script keeping `name == "test"`.
