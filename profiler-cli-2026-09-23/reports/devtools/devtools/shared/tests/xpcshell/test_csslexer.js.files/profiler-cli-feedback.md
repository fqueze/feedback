## Question: "what was the machine's CPU use, second by second, around t=65 s?" (resource-usage profile)

- Command: `profiler-cli zoom push 60,80 --session S; profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0`
- Expected: each `CPU Use` row with its CPU percent (or a per-second CPU summary for the zoomed range).
- Got: rows labelled only `CPU Use`, no value; `counter list` says "No counters in this profile".
- Workaround: collected the handles with `--json`, then `marker info <handles> --json` and a Python script to pull `cpuPercent` / `idle_pct` from `fields`. What the output could have shown: the CPU percent in the list label, or a `--summary` over the zoomed range (mean/min/max CPU %).

## `marker info` shows times rounded to the second

- Command: `profiler-cli marker info m-1..m-6 --session S`
- Expected: millisecond start times, to compare a test's start with its "will retry" line 13 ms later.
- Got: `Time: 1m6s - 1m51s (45.006s)`, and `1m6s (instant)` for the INFO line; the ms difference is invisible.
- Workaround: `thread markers --list --json` and read `start`.

## Question: "which log lines were emitted at this instant?" (review-test_csslexer.js)

- Command: `profiler-cli zoom push 110.85,110.856 --session S; profiler-cli thread markers --list --limit 0 --session S`
- Expected: the instant markers (log lines) that start in that 6 ms window.
- Got: 640 rows, led by every interval marker that overlaps the window (hundreds of 45 s TIMEOUT `test` markers), so the dozen log lines are buried.
- Workaround: `--json`, then a script that filters `flatMarkers` on `start` within the window. What the output could have shown: an option to list only markers that start in the zoomed range, or instants listed separately.

## Question: "which tests started but never ended?" (resource-usage profile, review-test_csslexer.js)

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0 --json` and `--search "will retry" ... --json`
- Expected: some trace of a test that logged test_start but never test_end.
- Got: no `test` marker at all for such tests. In ZtblArCL, 320 tests exist only as "will retry" INFO lines.
- Workaround: a script that diffs the test ids in "will retry" against the `test` markers. What the output could have shown: an open-ended `test` marker (through to the profile's end), or a count of unfinished tests in `profile info`.
