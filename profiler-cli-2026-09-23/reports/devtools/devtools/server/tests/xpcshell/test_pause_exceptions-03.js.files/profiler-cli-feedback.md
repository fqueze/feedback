## Marker times in text output are rounded to the second (test_pause_exceptions-03.js, 2026-09-22)

- Command: `profiler-cli thread markers --session test_pause_exceptions-03.js-1 --search test_pause_exceptions-03 --list --limit 0` and `profiler-cli marker info m-1 m-3 m-6`
- Expected: start times precise enough to order this test against its neighbours (they start 1-5 ms apart) and against the first `Failed to launch` warning.
- Got: `t=2m13s` in the list, `Time: 2m13s - 2m58s (45.042s)` in `marker info`. Durations have ms precision, start times do not.
- Workaround: `--json` and read `start`.

## Question: how many tests with a given status started before / after a given time

- Question: "of the 487 TIMEOUT tests in this job, which started before the first launch failure at t=131.676 s, and over what span did the rest start".
- Command: `profiler-cli thread markers --search "name:test,TIMEOUT" --list --limit 0 --json` then a Python script splitting on `start`.
- What the output could have shown: a `--group-by field:status` with first/last start per group, or zoom semantics that keep only markers *starting* in the range (the zoom keeps every interval marker overlapping it, so a 1 s zoom listed tests that started 10 s earlier).
