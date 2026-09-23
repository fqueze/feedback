## Marker times in a long profile are printed to the second

- Question: at what millisecond did each `console.log` line of the test land in the job's resource-usage profile (23 min long), to compare the gap between two store dispatches in the failing run and in the passing retry?
- Command: `profiler-cli thread markers --search "editor-variable" --list --limit 0 --session <s>` on `.../P2GCPtPfQVGhaGBUJ_us4g/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Expected: `t=` with millisecond precision, as in short profiles (`t=13.715s`).
- Got: `t=6m20s`, `t=6m21s` for every row, so consecutive markers 100-300 ms apart are indistinguishable.
- Workaround: `profiler-cli marker info m-470 m-471 ... --json` and a Python one-liner reading `start`. The default list could keep milliseconds (`t=6m20.633s`).
