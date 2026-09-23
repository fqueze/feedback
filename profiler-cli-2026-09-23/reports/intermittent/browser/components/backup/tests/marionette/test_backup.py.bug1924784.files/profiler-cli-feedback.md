# profiler-cli feedback (review-test_backup.py.bug1924784)

## profile-link.py refuses a session loaded from a profiler.firefox.com link

- Command: `profiler-cli load '<the report's https://profiler.firefox.com/from-url/...?marker=4523&thread=0&v=17 link>' --session review-test_backup.py.bug1924784-2`, then `profile-link.py --session review-test_backup.py.bug1924784-2 --marker m-60 --range 72.0,89.0`
- Expected: a link, since the review brief says to check a report's links by loading them, and the session knows the underlying Taskcluster URL (it is inside the from-url link).
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: stopped the session and reloaded the same profile from the raw Taskcluster URL (a second 30 min profile load) to link a new observation.

## Question with no answer: was the machine CPU-bound or I/O-bound over this range?

- Command: `profiler-cli zoom push 72.0,89.0` then `profiler-cli thread markers --search 'CPU Use' --list --limit 0` (and the same for `name:IO`) on a resource-usage profile.
- Got: 170 one-line `CPU Use` markers with no field values in the list, and in the summary only counts and durations. The values (cpuPercent, iowait_pct, write_bytes) are only in `marker info` or `--json`.
- Needed: per-field aggregates over the zoomed range (mean/max of cpuPercent and iowait_pct, sum of write_bytes), ideally per second. That alone showed the machine writing ~169 MB/s at ~40% iowait for the whole slow launch.
- Workaround: `--json` and a Python script bucketing fields per second.
