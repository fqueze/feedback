## Cannot load a resource-usage profile left as a partial stream (task aborted)

- Command: `PROFILER_CLI_SESSION_OWNER=1977276 profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/JG7vqs8CRAiuci2fL5JCUA/runs/0/artifacts/public/test_info/profile_resource-usage.json --session 1977276-1`
- Expected: the job timeline (Memory / CPU Use markers) of a talos task that taskcluster aborted for memory usage above 90%. That is exactly the case where the resource-usage profile is the only evidence.
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11668 (line 2 column 1)`. The file is a JSON-lines stream (`{"type":"meta",...}` then one `{"type":"marker",...}` per line) that mozharness never finalised because the task was killed.
- Workaround: parsed the JSONL with a python script to get the Memory markers (`data.used`) over time.

## A failed `load` leaves the session id taken

- Command: after the failed `load ... --session 1977276-1` above, `profiler-cli load <another URL> --session 1977276-1`
- Expected: the id free again, since nothing was loaded.
- Got: `Error: Session 1977276-1 is already running. Stop it first or choose a different session id.` followed by the previous load's error again.
- Workaround: `profiler-cli stop 1977276-1`, then load.

## (review) The peak of a resource-usage counter, and when it happened

- Question: "what is Memory `used` at 20 s, what is its peak and when, and when does it drop at the end". Same for the sum of IO `write_bytes`.
- Command: `profiler-cli thread markers --search name:Memory --list --limit 0 --json --session review-1977276-1`, then a python script over `flatMarkers[].data.used`.
- Expected: the resource-usage Memory/IO/CPU Use markers are really counters (one per 100 ms). The default output could give per-field min/max/sum with the handle and time of the extreme, or a coarse time series, e.g. `thread markers --search name:Memory --field used --stats`.
- Got: only duration stats for the markers, and nothing about their payload values. The 5,862-row list is too long to read.
- Workaround: `--json` plus a script, for each of the 6 profiles.
