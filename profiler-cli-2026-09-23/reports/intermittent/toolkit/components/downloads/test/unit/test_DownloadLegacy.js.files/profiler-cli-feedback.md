# profiler-cli feedback — test_DownloadLegacy.js

## `thread samples` in the full view returns the last zoomed range's samples after `zoom pop`

Profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/QDGnHrqTQyiccW5G5G7-oA/runs/0/artifacts/public/test_info/profile_test_DownloadLegacy.js.json (715 samples on t-0).

```
profiler-cli zoom push m-90 --session S        # a 1.341 s range
profiler-cli thread samples --session S        # "31 running samples"  (right)
profiler-cli zoom pop --session S
profiler-cli status --session S                # "View range: Full profile"
profiler-cli thread samples --session S        # "31 running samples"  (wrong: header says Full profile)
```

- Expected: the full profile's 715 samples once the zoom is popped.
- Got: the same 31 samples and function list as the popped zoom, under a header saying
  "View: Full profile". Sometimes a number matching no range I had used (5 samples). Silent:
  it read as "the whole 30 s test only has 31 samples", which I nearly built on.
- Workaround: `zoom push 0,<profile end>` explicitly, query, then pop. That returns 715 samples.

## Machine CPU during one test, from a resource-usage profile

Question: "was the machine saturated for the whole duration of this test, and how was it during
its retry?" `zoom push m-<test marker>` then `thread markers --search "name:CPU Use" --list`
prints 350 rows with no CPU value in the text output; the value is only in `--json`
(`data.cpuPercent`), so I scripted a mean/min/max. A `counter`-style summary (mean, min, max,
per-bucket over time) of `CPU Use` markers in the current zoom would have answered it directly,
as would printing `cpuPercent` in the list row.

## (review) Same stale-range bug in `function info`

After several `zoom push <a,b>` / `zoom pop` cycles on the same profile, `profiler-cli function info f-104 --session S` printed `View: Full profile` but "running (19 samples, 59.4% of thread)". Those are the figures of the last popped 1.2 s range. `thread functions --search` over the full profile gives 395 samples. Workaround as above: `zoom push 0,<end>` explicitly.
