## Question: "when did tests start failing in this job, and how many started or ended within the same seconds?"
- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json` plus a Python script to sort by start, count statuses and find the tests running at a given time.
- Expected: a way to list the markers overlapping a time (running at t=X), or a per-status histogram over time. That would have shown the burst of ~1,000 tests starting within 4 s and all ending 45 s later.
- Got: flat list only; `zoom push` keeps markers that intersect the range, but the 2,600 `test` markers were too many to read.
- Workaround: the script over JSON.

## Bare negative term in --search silently matches nothing
- Command: `profiler-cli thread markers --session <s> --search "-name:test,-name:SKIP,-name:CPU Use,-will retry" --list` (zoomed)
- Expected: markers whose text contains "will retry" excluded, or an error saying exclusion needs `field:value`.
- Got: "0 markers … No markers match the specified filters", with no warning. The bare `-will retry` seems to be taken as a positive term.
- Workaround: dropped the term and piped through `grep -v "will retry"`.
