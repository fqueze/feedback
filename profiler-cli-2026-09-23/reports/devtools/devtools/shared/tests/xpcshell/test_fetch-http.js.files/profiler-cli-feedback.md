## Resource-usage profile: "profile info" says no CPU activity while the CPU Use markers show 100%

- Command: `profiler-cli profile info --session test_fetch-http.js-3` on the resource-usage profile of task ZtblArCLSx6O6KzfEkL1YQ
- Question: was the machine saturated or idle when 750 tests timed out?
- Expected: the CPU timeline of the job.
- Got: `CPU activity over time: No significant activity.` The machine's CPU is only in `CPU Use` interval markers (100% at t=50-60 s, ~1% from t=70 s), so I scripted over `thread markers --search 'name:CPU Use' --list --json` to bucket `cpuPercent`.
- What could show it: `profile info` (or a `thread markers` summary of `CPU Use`) printing cpuPercent over time for these profiles, instead of "No significant activity".

## "When did these markers happen" has no answer without --json

- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json` piped to a script bucketing TIMEOUT test markers by start time.
- Question: did the 750 TIMEOUTs of this job all start in one burst (a collapse) or spread over the job?
- Expected: a time distribution of the matching markers (the summary gives count and durations, not when).
- Got: only count/duration stats, or a 1,742-row list.
- What could show it: a per-time-bucket count (sparkline or table) of the matching markers in the non-`--list` summary.

## (review) profile-link.py refuses a session loaded from a report's profiler.firefox.com link

- Command: `profile-link.py --session review-test_fetch-http.js-1 --marker m-3`, after `profiler-cli load "<the report's https://profiler.firefox.com/from-url/...?marker=4770&thread=0&v=17 link>"` as review-brief.md says
- Expected: a link for a new observation in the same profile.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: stopped the session and reloaded the raw Taskcluster URL (another ~0.6 GB load) to link. The script could recover the raw URL from a `from-url/` link, which carries it verbatim.
