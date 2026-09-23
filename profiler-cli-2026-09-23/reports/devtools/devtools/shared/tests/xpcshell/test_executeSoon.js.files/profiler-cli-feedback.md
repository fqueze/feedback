## Question: which tests were running, or had just ended, at time T (leaker candidates around an onset)?
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json` then a Python script computing, per `test` interval marker, start/end relative to T; and intersecting across 4 resource-usage profiles.
- Expected: a way to list interval markers overlapping, or ending within N s before, a given time, e.g. `thread markers --search name:test --overlapping 63.9` / `--ending-before 63.9,15`.
- Got: `zoom push` keeps markers overlapping the range, but the list sorts by start and prints no end time, so "ended 0.3 s before T" is not readable without the JSON.

## Question: how do the test statuses evolve over the job (when did everything start failing)?
- Command: same JSON dump, bucketed by start time and status prefix in Python.
- Could have shown: `--group-by` on a label prefix (TIMEOUT/PASS/FAIL) combined with time buckets, e.g. `thread markers --search name:test --group-by label-prefix --buckets 10s`.
