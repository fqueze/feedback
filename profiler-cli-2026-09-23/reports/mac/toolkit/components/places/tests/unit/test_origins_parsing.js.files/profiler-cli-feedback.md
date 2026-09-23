## Question: "what was the machine's average CPU (and iowait) while this one test ran?"

- Command: `profiler-cli zoom push 830.424,865.954 --session S` then `profiler-cli thread markers --session S --search "CPU Use" --list --limit 0 --json`, piped to a script averaging `data.cpuPercent` / `data.iowait_pct`.
- Expected: the resource-usage profile's `CPU Use` markers summarised over the zoomed range (mean, min, max, share of markers at >= 99%, mean iowait), since that is the one number every timeout diagnosis needs.
- Got: the aggregate view only counts the markers; `--list` prints one row per 100 ms marker (357 rows for a 35 s test) without the percentages in the row text.
- Workaround: `--json` plus a 10-line Python script (`cpu.py`, `cpu2.py` here). Its output could have been a line under the marker-type summary such as `cpuPercent: mean 100.0%, min 100.0%, max 100.0%`.

## Question: "how fast was the test's loop progressing, and how far did it get?"

- Command: `profiler-cli thread markers --search "Testing userinfo" --list --limit 0`, then a script over the `t=` column to get the gaps between successive markers.
- Expected: some way to get the intervals between successive matching instant markers (median, max), e.g. a `--gaps` option on `--list`.
- Got: only the timestamps; the intervals had to be computed by hand (`iters.py`).
- Workaround: regex over the text output.

## Question (review): "was the main thread running, or stalled, during this range?"

- Command: `profiler-cli zoom push 20.37,20.93 --session review-test_origins_parsing.js-6` then `profiler-cli thread samples --include-idle` and `thread samples-bottom-up --include-idle` on the Linux per-test profile of QpUxbA6bTPSDlQLA9BM2vg.
- Expected: some sign that the 56 samples in that range were copies, not stack walks: the sampler duplicates the previous sample when the thread's CPU time did not advance (`tools/profiler/core/platform.cpp`, `CanDuplicateLastSampleDueToSleep() || GetThreadCPUDelta() == 0`).
- Got: 56 samples, 100% `JIT (ion)`, all in `Sqlite.sys.mjs!_executeStatement` with a JIT-address leaf, which reads as "running JIT code for 560 ms" while the process CPU counter says 0.4-0.9% and the `Awake` markers say the thread was asleep.
- Workaround: read the sampler code, then cross-check against the `Awake` gap and the `Process CPU` counter. A per-sample CPU-delta column, or "56 samples (55 duplicated, 0 ms CPU)" in the category summary, would have answered it directly. It would also split a thread's non-idle samples into running and starved, the question every timeout on a saturated machine asks.

## Question (review, same as the first entry above): "what was the machine's average CPU over this range?"

- Needed again for four ranges and two whole parallel phases; same `--json` plus `cpu.py` workaround (`review/cpu.py`).
