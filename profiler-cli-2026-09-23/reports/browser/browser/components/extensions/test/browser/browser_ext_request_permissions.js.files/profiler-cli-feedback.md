## Question: "when do the first and last markers matching this search happen, and how many are there?"

- Command: `profiler-cli thread markers --session S --thread t-0 --search name:RefreshDriverTick --list --limit 0 --json | python3 ...` (first/last `start` and count), inside a `zoom push`.
- Wanted: whether the parent's refresh driver stopped ticking early in the vsync wait while the WebExtensions process kept ticking to the end. The grouped (non-`--list`) output gives a count per group but no first/last time; the `--list` text output gives rows only, printed at 1 s resolution (`t=3m15s`).
- What could answer it: a first/last time (or time span) per group in the default aggregate `thread markers` output.
