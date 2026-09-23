## When did tests start failing in this job, and how many were started at once

- Command: `profiler-cli thread markers --session <s> --search name:test --list --json --limit 0`, then a Python script bucketing `test` markers' start/end times by `data.status`.
- Question: "from what time on does every test in this job fail, and did the failing ones all start/end together?" (answer: 750 TIMEOUT tests all started within 63-68 s and all ended at 110-113 s, i.e. 45 s later).
- Could show: `thread markers --group-by field:status` with first/last start per group, or a per-status histogram of start times over the zoom range.
- Minor: in `--json` flat lists, instant markers have no `duration` key at all (KeyError in scripts), rather than `duration: null`.
