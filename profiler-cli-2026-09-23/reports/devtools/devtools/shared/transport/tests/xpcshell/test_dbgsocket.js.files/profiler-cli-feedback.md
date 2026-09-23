## Question: how busy was the machine over the job's timeline? (resource-usage profile)

- Command: `profiler-cli counter list --session <s>` / `profiler-cli profile info --session <s>` on `.../XfKT04wZR42kSNwV6wuRpQ/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Expected: a CPU-over-time summary. The profile has 1,163 `CPU Use` markers with user/system/idle.
- Got: `No counters in this profile.` and `CPU activity over time: No significant activity.`. Both are true of samples but misleading for a resource-usage profile.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a script that averages user/system per 5 s bucket.
- Could show: `profile info` summarising `CPU Use` (and `Memory`) markers over time when the profile has no samples/counters.

## Question: when did tests in this job stop passing? (outcome over time)

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a script that buckets `test` markers by start time and by the `PASS`/`TIMEOUT`/`SKIP` prefix of the label.
- Expected: some way to group markers by time bucket (e.g. `--group-by label-prefix --bucket 5s`), to see that no test started after 54 s passed.
- Got: aggregate counts only (`test 3561 markers`), with no time axis.

