## Question: what was the whole machine's CPU use over a time range? (resource-usage profile)

- Command: `profiler-cli thread markers --session 2058686-2 --search "name:CPU Use" --list --limit 0` (zoomed to 652–780 s)
- Expected: the CPU percentage per marker, or an aggregate over the zoomed range (mean, or per-bucket), since that is the only thing a `CPU Use` marker carries.
- Got: `profile info` says "No significant activity" and `counter list` says "No counters", because resource-usage profiles keep CPU as `CPU Use` markers, not as a counter. The marker list shows only name, start and duration, with no `cpuPercent`.
- Workaround: `--json`, then a Python script that buckets `fields[key=cpuPercent]` by 10 s. It showed the machine at 1% for 100 s while a test timed out, which was the deciding observation.

## Question (again, from the review): what was the whole machine's CPU use over a time range? (resource-usage profile)

- Command: `profiler-cli thread markers --session review-2058686-3 --search "name:CPU Use" --list --limit 0 --json` (zoomed to 670–772 s)
- Expected: a per-bucket or mean `cpuPercent` over the zoomed range, so I could check a report's "1–3% in every 10 s bucket" claim directly.
- Got: the same as above: `--list` shows no `cpuPercent`, so I had to dump 1024 markers as JSON.
- Workaround: the same Python bucketing script. It found 0.8–2.7%, which corrected the report's figure. Second reviewer to need this for one profile.
