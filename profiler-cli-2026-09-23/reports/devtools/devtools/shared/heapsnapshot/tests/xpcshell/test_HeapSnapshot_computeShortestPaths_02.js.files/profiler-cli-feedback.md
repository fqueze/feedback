## Question: in what order did these harness markers happen within one second?

- Command: `profiler-cli thread markers --session <s> --search "Cleaning up profile,name:test,launch_application,..." --list --limit 0`
- Expected: timestamps precise enough to order markers (harness logs many lines per second).
- Got: `t=12m44s` for every marker in that second (whole seconds once past one minute), so which slot
  was freed before which test started could not be read.
- Workaround: `--json` and a script printing `start` in ms.
- Could show: milliseconds (`t=12m44.338s`), at least in `--list` mode or when zoomed to a short range.

## Question: did these 1153 TIMEOUT tests all start at once?

- Command: `profiler-cli thread markers --session <s> --search "Status:TIMEOUT"` (aggregate view)
- Expected: something saying the markers' start times cluster (all start in 10 s, all last 45 s).
- Got: count and duration min/avg/max only; `--list` gives 1153 rows.
- Workaround: `--json` and a script bucketing `start` by 5 s.
- Could show: first/last start time (or a small start-time histogram) next to the duration stats.
