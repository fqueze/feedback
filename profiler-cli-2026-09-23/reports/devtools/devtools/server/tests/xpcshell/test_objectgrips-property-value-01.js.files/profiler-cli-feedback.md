## Question: "what was the machine's CPU % over this time window, per second?"
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` on a resource-usage profile (zoomed to 118,180).
- Expected: each CPU Use row to show its CPU Percent (the only thing the marker carries), or a summary of the series.
- Got: rows with name, time and duration only; the percentage needs `marker info` per handle or `--json` plus a script.
- Workaround: `--json` and a Python loop over `flatMarkers[].data.cpuPercent`.
- Also: `zoom push 2m10s,2m14s` was accepted and silently produced a 1 ns view (the format printed in every row is not accepted as input); seconds work.

## (review) profile-link.py refuses a session loaded from a profiler.firefox.com link
- Command: `profiler-cli load '<profiler.firefox.com/from-url/...marker=8321&thread=0&v=17>' --session S`, then `profile-link.py --session S --marker m-1350`.
- Expected: a link, since the review brief says to check a report by loading its links, and the from-url link wraps the Taskcluster URL.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: stopped the session and reloaded the same profile from the raw Taskcluster URL (a second ~0.6 GB load) just to build two links. profile-link.py could unwrap the `from-url/` part itself.
