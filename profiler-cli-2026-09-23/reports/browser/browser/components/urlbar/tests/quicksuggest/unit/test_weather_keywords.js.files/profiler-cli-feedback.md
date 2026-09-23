## Question: "what was the machine CPU while this one test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push m-1 --session S` (the test's `test` marker), then
  `profiler-cli thread markers --session S --search 'name:CPU Use' --list --limit 0`
- Expected: the CPU percent per row, or better a summary (mean / min / max / share >= 95%) of
  `cpuPercent` over the zoomed range, like `counter info` gives for counters.
- Got: 319 rows with only name, time and duration (`m-157 CPU Use t=4m54s 104ms`), no CPU value;
  the non-list aggregate gives only duration stats. The resource-usage profile has no counters, so
  `counter info` cannot answer either.
- Workaround: `--list --limit 0 --json | jq` over `.flatMarkers[].fields[] | select(.key=="cpuPercent")`.
- Also: times in this 30-minute profile print as `t=4m54s`, one-second resolution, so the rows
  cannot be ordered or matched against the per-test profile's timestamps without `--json`.

## Question: "how long was the longest silence in the test's log?"

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session S`
- Expected: a way to see the largest gaps between consecutive listed markers (a stall is the
  first thing to check in a timeout).
- Got: only the chronological list; 3,291 rows.
- Workaround: `--json | jq` computing consecutive `start` differences.

## Question: "what share of the whole profile was this thread idle?" (review, profiler-cli 0.9.0)

- Command: after loading a link with `range=28000m2614`, `profiler-cli zoom clear --session S`
  (prints "returned to full profile"), then `profiler-cli thread samples --include-idle --session S`.
- Expected: the categories over all 2,801 samples of the thread, as `thread info` counts them.
- Got: header says `View: Full profile`, but the breakdown covers only 164 (then 244) samples,
  those of the last range that was pushed. `zoom pop` does the same thing: after `zoom push 10,11`
  and then `zoom pop`, `thread samples` still counts 81 samples. Markers are not affected. So an
  idle share read after a pop or clear is silently wrong.
- Workaround: `zoom push 0,<profile end>` explicitly before any samples query.
