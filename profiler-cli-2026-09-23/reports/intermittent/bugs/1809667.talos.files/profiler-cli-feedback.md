# profiler-cli feedback (1809667.talos)

## Question: what did the machine do during a talos job's silent hang until the task max run time?

- Command: `PROFILER_CLI_SESSION_OWNER=1809667.talos profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/UuYCBzDER_qRC5WSMMfALw/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session 1809667.talos-1`
- Expected: the job's resource-usage timeline (CPU Use, IO, NetIO markers) loaded, as for a finished job.
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11518 (line 2 column 1)`. A task killed at its max run time uploads the mozsystemmonitor stream (one JSON object per line: meta, thread, then markers), never finalized.
- Workaround: `res.py` / `net.py` / `series.py` in this directory parse the line stream for CPU user/system %, IO read bytes and NetIO bytes per 100 ms sample, windowed on log timestamps.
- Cost: every one of the 26 failing jobs of this mode, and every max-run-time job in general, has only this format; no link can be given for any observation from them.
- What the output could have shown: the same marker tables and tracks as for a finished profile, so the silent interval could be linked and zoomed.

## Question: which NetIO samples in a range carry large (loopback) transfers?

- Command: `profiler-cli zoom push 260.5,265 --session 1809667.talos-2` then `profiler-cli thread markers --search NetIO --list --limit 60 --session 1809667.talos-2`
- Expected: each NetIO marker with its Received / Sent bytes, or a way to filter on a payload value.
- Got: name, time and duration only (`m-3  NetIO  t=4m23s  167ms`), 45 identical-looking rows.
- Workaround: `--json` and a Python filter on `fields[].value` (`recv_bytes > 50000`) to find `m-3` (122,880 B each way).
- What the output could have shown: the marker's table label fields in `--list`, or a numeric filter such as `--search recv_bytes>50000`.
