## `thread samples` keeps a popped zoom's sample set

- Commands (session loaded from a Taskcluster per-test profile, 30.5 s long):
  1. `profiler-cli zoom push 24.5,25.5 --session S`
  2. `profiler-cli thread samples-top-down --session S --include-idle`
  3. `profiler-cli zoom pop --session S`
  4. `profiler-cli thread samples --session S --include-idle`
  5. `profiler-cli zoom clear --session S` -> "Already at full profile view"
  6. `profiler-cli thread samples --session S --include-idle`
- Expected: steps 4 and 6 cover the full profile (940 samples; the header says "View: Full profile").
- Got: both reported "Categories (31 running samples)" -- the 1 s zoom's samples -- under a
  "View: Full profile" header. `--json` returned the same 31-sample figures.
- Workaround: `zoom push 0,<end>` then `zoom pop`; after that `thread samples` reported 940 again.
- Cost: I nearly quoted the 1 s window's self-time table as the whole test's.

## Question: "how busy was the machine while this test ran?"

- Command: `profiler-cli zoom push m-1` (the test's `test` marker in a resource-usage profile),
  then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`.
- Expected: the CPU percentage of each `CPU Use` marker, or a mean/min/max over the zoom.
- Got: 368 rows of `CPU Use  t=2m39s  100ms` with no value; the CPU figure is only in
  `marker info` or `--json`.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` (mean 100%, min 100%).
- What would have answered it: a value column in the list for `CPU Use` markers, or an
  aggregate of numeric payload fields (mean/min/max) in the non-list view, or `CPU Use` exposed
  as a counter so `counter info` works under the zoom (`counter list` said "No counters").

## (review) `thread samples` after `zoom clear` still reports a stale sample set

- Seen again by the reviewer, on the same profile (RXvnIcunSYalu2ZlShIhwg per-test, 940 samples).
  The zooms were `zoom push 20.086,20.356`, then `zoom push 20.2575,20.3289` / `zoom pop`, then
  `zoom clear` ("returned to full profile"), then `thread samples --include-idle`.
- Expected: 940 samples under "View: Full profile".
- Got: "Categories (9 running samples)" under a "View: Full profile" header. That is neither the
  full profile nor the 270 ms zoom (2 samples).
- Workaround: `zoom push 0,<end>` gives 940.
- Cost: one wasted query, and a wrong number that looks plausible if you don't already know the
  total.
