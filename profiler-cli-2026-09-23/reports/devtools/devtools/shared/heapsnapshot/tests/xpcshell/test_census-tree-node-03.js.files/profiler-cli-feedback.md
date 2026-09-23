## Question: "when did a job-wide collapse start, and which tests passed after it?"
- Command: `profiler-cli thread markers --search "will retry" --list --limit 0 --json | python3 …` (bucketed by second) and `--search name:test --json | python3 …` (PASS markers by start and end time).
- Expected: a way to list `test` markers filtered by status and by start or end time. `zoom push` filters on overlap, which does not answer "started after X".
- Could have shown: `--group-by field:status` with time buckets, or a `--starts-after/--ends-before` filter.

## Minor: exclusion search needs every noise marker name spelled out
- Command: `thread markers --search "-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval"` on a resource-usage profile.
- Expected: one flag to hide the resource-sampling markers, which make up about 30% of the markers in these profiles.
