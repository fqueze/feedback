## Which untagged harness lines belong to this test

- Question: "which `not killing -- proc or pid unknown` / `xpcshell return code` line is this test's?" These INFO lines carry no test name, and 1,149 of them are interleaved at the same millisecond.
- Command: `profiler-cli thread markers --search "not killing,xpcshell return code,creationTime_01" --list --limit 0 --json`, then a script printing the neighbours of the test's own markers, and `marker info --json` to read `markerIndex`.
- What would have answered it: a `--context N` option on `--list` (N markers before/after each match, in marker-index order), or `markerIndex` shown in the text list so adjacency is visible.

## Counting a job's `test` markers by status

- Question: "did every test after t=50 s time out, and did any pass?"
- Command: `thread markers --search name:test --list --limit 0 --json` plus a Python grouping by `data.status` with min/max start and duration.
- What would have answered it: the aggregated `thread markers --search name:test` view broken down by the `status` field (count, first/last start, min/max duration per status).

## (review) Which "will retry" lines have no matching `test` marker

- Question: "which tests failed without a `test_end` (no `test` marker), and when?". 312 extension tests logged `failed or timed out, will retry.` from 50.458 s with no `test` marker. The report missed them, which made the harness failures look as if they started at 52.2 s.
- Command: `thread markers --list --limit 0 --json`, then a script joining the INFO `will retry` lines to `test` markers by test id.
- What would have answered it: listing tests that have a `test_start` and no `test_end` as unfinished `test` markers, or a flag on the `test` aggregate that counts them.

## (review) CPU over a time window

- Question: "what was the CPU use in 44–50.4 s, just before the failures?". The `Phase` marker only gives a phase-wide average (11.4%), and that average hid 6.4 s at 100%.
- Command: `thread markers --list --limit 0 --json`, then a script averaging `CPU Use` marker `data.cpuPercent` per window. `counter list` says "No counters in this profile".
- What would have answered it: a `CPU Use` summary (mean/max) for the current zoom range, e.g. in `zoom push` or `thread info` output.
