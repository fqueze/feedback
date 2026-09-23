## Question: "what was the machine's CPU and iowait while this test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push m-1 --session S` (the `test` marker), then `profiler-cli thread markers --session S --search "name:CPU Use"` and `--list`.
- Expected: some summary of the `CPU Use` markers' `cpuPercent` / `iowait_pct` fields over the zoomed range (mean, share of samples >= 95%, or a sparkline like `counter info` gives), since these markers are the only CPU track in a resource-usage profile (`counter list` says "No counters in this profile").
- Got: the aggregate shows only count and durations; `--list` rows show no field values (the CPU Use markers have no description), so every row reads `m-2040 CPU Use t=4m15s 99ms`.
- Workaround: `--list --limit 0 --json` piped to a Python script averaging `data.cpuPercent` and `data.iowait_pct`, bucketed by time.
- What would have answered it: render the resource-monitor `CPU Use` markers as a counter (then `counter info` with its "over time" buckets works under zoom), or a `--stats field:cpuPercent` on `thread markers`.

## Question: "what was the machine doing during this span of the per-test profile?"

- The per-test profile and the job's resource-usage profile have different zero times. I aligned them by hand by finding the same `Starting dismissResult` INFO marker in both (t=14.565 s vs t=269.314 s) and subtracting.
- What would have answered it: a way to open a range of one profile in the other's time base (both carry absolute start times in meta), e.g. `zoom push --abs <ISO time range>` or printing absolute wall-clock time next to markers.

## Question: "how much of the test's time went into waiting between log line A and log line B?"

- Command: `profiler-cli thread markers --session S --category Test --search name:INFO --list --limit 0`.
- Needed: the sum and list of intervals between each `forceSync Syncing Rust backend` and the next `forceSync Done syncing Rust backend` INFO marker (25 pairs). The list is there but pairing requires a script over `--json`.
- Also: every test log line appears twice (once plain, once JSON-quoted with surrounding `"`), which doubles `--list` output; had to filter `label.startswith('"')`.

## Bug (review): after `zoom pop` back to the full profile, samples commands keep the popped range's samples

- Commands (session on DBIWBaYCR6iWXhVq9HeyQg's per-test profile, thread t-0): `profiler-cli zoom push 29.5,30.3 --session S`, `profiler-cli thread samples-top-down --session S --include-idle`, `profiler-cli zoom pop --session S`, then `profiler-cli thread samples --session S --include-idle --limit 3`.
- Expected: the full profile's 2906 samples (80.1% Idle), as the header `View: Full profile` says.
- Got: `Categories (65 running samples)` — the popped 800 ms window's samples — under a `View: Full profile` header. `status` also says `View range: Full profile`. Same with `thread functions` and `samples-top-down`. Reproduced twice. `zoom clear` says "Already at full profile view" and does not fix it.
- Workaround: `zoom push 0,<profile end>` then `zoom pop`, after which the full view shows 2906 samples.
- Risk: silently wrong numbers, since the header claims the full range.
