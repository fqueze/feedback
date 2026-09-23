## Question: how busy was the machine over time, in a resource-usage profile?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (with a `zoom push 42,94`)
- Expected: each row showing its CPU percent / idle percent, or a per-bucket summary of machine CPU over the view.
- Got: 500 rows of `CPU Use  t=...  93ms` with no value; `counter list` says "No counters in this profile", and `profile info` says "No significant activity". The values are only in `marker info` (one call per marker) or `--json`.
- Workaround: `--json`, then a Python script bucketing `data.cpuPercent` per 2 s.
- What could have shown it: the CPU fields in the `--list` row description, or `counter`-like "over time" buckets for CPU markers.

## Question: when exactly did this marker happen, past the first minute?

- Command: `profiler-cli thread markers --session <s> --search test_promise_state-03 --list --limit 0`, and `profiler-cli marker info m-17`
- Expected: millisecond start times, as for markers under 60 s (`t=47.896s`).
- Got: `t=2m13s` in the list and `Time: 2m9s (instant)` in `marker info`: whole seconds only. Comparing a test's start with the onset of the breakdown 1.7 s earlier is impossible from that.
- Workaround: `--json` and read `start`.
- What could have shown it: `t=2m13.403s`.

## review-test_promise_state-03.js: a session loaded from a report's profiler link cannot make new links

- Command: `profiler-cli load '<profiler.firefox.com/from-url/... link from the report>' --session S`, then `profile-link.py --session S --marker m-473`.
- Expected: a link to the new marker. The review brief says to load the report's links, and a reviewer then needs to cite new places in the same profile.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: loaded the profile again from the raw Taskcluster URL, which costs another full load (about 1 min, 0.6 GB). `profile-link.py`, or `profiler-cli` when it loads a `from-url` link, could get the raw URL back out of the link.
