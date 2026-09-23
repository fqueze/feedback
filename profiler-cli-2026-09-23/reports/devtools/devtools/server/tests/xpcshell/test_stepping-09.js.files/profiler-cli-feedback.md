## Question: how many tests passed/failed after time T (onset of an infra breakdown) in a resource-usage profile

- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0 --json`, then a Python script counting `label` prefixes (PASS/TIMEOUT/SKIP) split by `start > T`.
- Expected: a way to get marker counts grouped by a label prefix within a zoom range, e.g. `zoom push T,end` then `thread markers --search name:test --group-by label-prefix` (or a `--group-by field:status` for the harness `test` markers, whose status is only in the label text).
- Got: the default aggregated view groups by marker name only (`test`), not by status; the list is 1,389 rows.
- Workaround: JSON + script.

## Question: average whole-machine CPU per few seconds over a range (was the machine idle during a timeout?)

- Command: `zoom push 44,96` then `thread markers --search "CPU Use" --list --limit 0 --json`, then a script bucketing `data.cpuPercent` (a string like "100.0%") per 4 s.
- Expected: `CPU Use` markers summarised over the zoom (mean/min/max of cpuPercent, maybe per bucket) in the default output, like `counter info` does for counters.
- Got: 515 individual rows.
- Workaround: JSON + script.

## Question (review): which tests were running at time T in a resource-usage profile

- Command: `profiler-cli thread markers --session <s> --search "name:test" --list --limit 0 --json`, then a script keeping `test` markers with `start < T < start + duration`. Repeated on 6 profiles to compare the onset of an infra breakdown across jobs.
- Expected: something like `thread markers --search name:test --overlapping <T>` (or `zoom push T,T` listing interval markers that span the point, not only those starting in the range).
- Got: `zoom push` keeps markers overlapping the range, but mixed with the 1,000+ other markers; `--list` over the whole thread is 1,389 rows per profile.
- Workaround: JSON + script.

## Exclusion by label does not work on `test` markers

- Command: `thread markers --search "name:test,-label:SKIP" --list --limit 0 --json`
- Expected: `test` markers without the SKIP ones (the status is only in the label, e.g. `SKIP — path`).
- Got: all 1,389 `test` markers, SKIP included: `label` is not a payload field, so `-label:` matches nothing, silently.
- Workaround: filtered in the script.
