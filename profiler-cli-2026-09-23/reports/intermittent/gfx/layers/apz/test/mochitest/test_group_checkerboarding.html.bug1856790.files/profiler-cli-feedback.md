## Question: what was the machine's average CPU use over one test, versus the tests around it (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use"` (with and without `zoom push m-<test marker>`)
- Expected: the average/min/max of the `CPU Percent` field over the view, since that is the only thing a `CPU Use` marker carries.
- Got: only the markers' interval durations (min=85ms, avg=99.9ms, max=117ms), which say nothing. No counter exists in resource-usage profiles, so `counter info` is not an alternative.
- Workaround: `--list --limit 0 --json` piped to a Python script averaging `data.cpuPercent` over time windows (3,000+ markers per 5 minutes).
- What would have answered it: for markers with a numeric payload field (CPU Percent, User %, ...), print that field's mean/min/max in the aggregate view, or a `--stat <field>` option; ideally a sparkline like `counter info` has.

## Question: at what exact second did this log line happen, in a profile longer than a minute

- Command: `profiler-cli thread markers --session <s> --search checkerboarding --list --limit 0` and `profiler-cli marker info m-N`
- Expected: sub-second timestamps, to compute per-iteration durations of a loop (7-8 s steps) and the gap from test start to the first subtest.
- Got: `t=14m9s`, `t=15m2s`, `Time: 5m31s` — rounded to the second once past a minute, so two markers 0.6 s apart look simultaneous, and the timeout firing looked like it came after the last iteration when it came 3 s before it.
- Workaround: `--json` and `start/1000`.
- What would have answered it: keep millisecond precision in `--list` and `marker info` (e.g. `14m09.243s`), at least in `marker info`.

## Question: how many cores was each process using during this range (review)

- Command: `profiler-cli counter info c-15 --session <s>` and `c-0`, `c-22` after `zoom push 13.34,22.04` (Windows per-test profile, 8 logical CPUs)
- Expected: Process CPU in a stated unit, the same for every process. For example cores, or % of one core, or % of the machine.
- Got: bare percentages with no basis. The GPU process shows 97–100%, which matches its raw CPU time divided by 8 cores (7.6 cores). The parent shows 2.3–5.1%, but its raw CPU time is 0.006 cores: 0.6% of one core, or 0.08% of the machine. So the processes are not on one scale, and nothing says that the GPU process was using the whole machine.
- Workaround: downloaded the profile and summed `counters[].samples.count` (ns) over `timeDeltas` in Python.
- What would have answered it: state what 100% means, and print CPU time in cores (or ms per s) next to the percentage. Ideally also the part of the process's CPU that its profiled threads account for, since in CI most of a GPU process's work runs on threads that are not recorded.
