# profiler-cli feedback (bug 1809667)

## Partial resource-usage stream of a task killed for max run time cannot be loaded

- Command: `PROFILER_CLI_SESSION_OWNER=1809667 profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Wcc7_-9MT3uebNKvLVHj5g/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session 1809667-2`
- Expected: the profile loaded (the file is the resource monitor's streamed format: a `meta` object, then one `marker` object per line).
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11689 (line 2 column 1)`
- Workaround: parsed the lines with a Python script; could not link any observation from these jobs, so the links in the report point at a finished profile of a passing-but-slow run of the same pool instead.

## A failed load leaves the session id taken

- Command: after the failed load above, `profiler-cli load <another url> --session 1809667-2`
- Got: `Error: Session 1809667-2 is already running. Stop it first or choose a different session id.` — `session list` showed a live daemon for it.
- Expected: a load that failed to not leave a daemon (holding memory) behind.
- Workaround: `profiler-cli stop 1809667-2`, then load again.

## No numeric aggregation over marker fields

- Question: the mean `IO Wait %` over the 29,244 `CPU Use` markers of a run, to compare with another run.
- `thread markers` gives counts and durations only; `marker info` one marker at a time. Had to parse the raw file.

## (review-1809667) Question: how much did this job write to disk, and at what rate over time?

- Command: `profiler-cli thread markers --session review-1809667-1 --search name:IO` on a resource-usage profile (29,244 `IO` markers with `write_bytes`/`write_count` fields).
- Expected: the sum of `write_bytes` over the view, and its rate per time bucket, so two runs can be compared (3.6 GB vs 24.6 GB across one push; 57–70 MB/s dropping to a flat 11 MB/s within one run).
- Got: counts and durations only; `marker info` gives one 100 ms sample at a time.
- Workaround: `review/agg.py` and `review/ts.py`, run over the raw profiles downloaded with curl. What the output could have shown: per numeric field, its sum/mean over the view, and with `--bucket <s>`, the series.
