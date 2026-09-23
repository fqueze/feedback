## Question: in what order, and how far apart, did these markers happen?

- Command: `profiler-cli thread markers --category Test --search browser_roundedWindow_open_max_inner --list --limit 0 --session ...` (and `marker info m-6 m-16`)
- Expected: timestamps precise enough to order markers inside one test (ms).
- Got: `t=3m7s` / `Time: 3m7s (instant)` for every marker of a 2-second test in a 3-minute profile: no ordering or spacing visible.
- Workaround: `--json` and a script printing `start/1000` to ms.
- What the output could show: ms resolution (e.g. `187.458s`) whenever the listed markers span less than a few seconds.

## Question: which content processes were alive at time t?

- Command: `profiler-cli profile info --search example.net --all --session ...`
- Expected: process lifetimes in seconds, to find the content process of a tab opened at t=187.05s.
- Got: `[ts-W → ts-W2]` timestamp handles only; had to read `profile info --all --json` and filter `startTime/endTime` with a script. (Turned out no content process alive at capture time was in the profile: the latest ended at 179.75s of a 190s profile. Saying so in `profile info` would also have saved time.)
- What the output could show: start/end in seconds next to the ts handles, and a `--at <t>` filter.

## Question (review): how many samples does this range hold, and how far apart are they?

- Command: `profiler-cli zoom push 187.004,187.147 --session ...` then `profiler-cli thread samples --include-idle --limit 15 --session ...`
- Expected: the sample count and spacing for the range, so I can tell "idle for 143 ms" from "one sample".
- Got: `Categories (1 running samples)`. It says "running" although `--include-idle` was passed and the one sample is `mach_msg2_trap`. `profile meta` says `Sampling interval: 10ms`, but the thread actually has about 20 samples a second. I only found that by zooming into four 500 ms ranges and counting.
- What the output could show: `N samples (M idle), median spacing X ms` for the view, next to the nominal interval.
