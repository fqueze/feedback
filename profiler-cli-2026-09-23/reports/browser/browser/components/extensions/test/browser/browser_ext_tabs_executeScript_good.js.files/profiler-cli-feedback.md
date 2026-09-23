## Times past 60 s print as "1m", losing all precision

- Command: `profiler-cli thread markers --search "...menusInternal..." --list --limit 0 --session <s>` and `profiler-cli marker info m-24 m-15 ... --session <s>` on a 65 s profile (UeCGQWF9S3G-pTRChPAXkw per-test profile).
- Expected: timestamps like `t=59.580s` / `1m0.580s`, as for markers under 60 s.
- Got: every marker after 60 s shows `t=1m`, and `marker info` shows `Time: 1m - 1m (128.79μs)`, so the ordering of the create call, popupshowing, popupshown and the failure within one second could not be read.
- Workaround: `marker info <m> --json` and read `start` (ms) with a python one-liner, per marker.

## Question: "was the machine in iowait / writing to disk while this test ran?"

- Command: `profiler-cli counter list --session <resource-usage session>` → "No counters in this profile"; `profile info` → "CPU activity over time: No significant activity."
- The answer was in `CPU Use` and `IO` markers (100 ms each; `iowait_pct`, `write_bytes`), which I only found through `thread markers` grouping, then had to tabulate with `thread markers --search "name:CPU Use,name:IO" --list --json` and a python script.
- What would have answered it: `profile info` (or `counter list`) on a resource-usage profile summarising those markers as time series — CPU %, iowait %, write bytes per bucket — over the current zoom.

## Question: "what was the main thread doing during this 270 ms runnable?" (review)

- Command: `profiler-cli zoom push m-41` then `profiler-cli thread samples --limit 3 --session <s>` on a CI per-test profile (CfJCtHqYTWOBoBYVeDSs2Q), where the runnable was blocked in `libc.so.6!_unlink`.
- Expected: about 27 samples in `nsIFile.remove` → `_unlink`, or some sign that the thread was blocked in a syscall.
- Got: `total: 1`. Idle is excluded by default, and even `thread functions --include-idle --search _unlink` over 25–30 s (five runnables of ~270 ms) counted only 6 samples. The call tree reads as "1 sample of work", not "blocked for 270 ms".
- Workaround: took the durations from the `Runnable` markers, and used `samples-top-down --include-idle` only to find which frame they were in.
- What would have answered it: saying that a thread is blocked in its leaf frame (samples de-duplicated while sleeping) and for how long, e.g. "blocked in _unlink for 267 ms (1 distinct sample)".
