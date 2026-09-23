# profiler-cli feedback

## Question: was the machine saturated while this test ran?

- Command: `profiler-cli zoom push 250.3,467.4 --session S` on a resource-usage profile, then
  `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0 --json`
  piped into a Python script computing mean / median / p90 of `cpuPercent`.
- Expected: a summary of the machine CPU over the zoomed range (the resource-usage profile has no
  counters, only 2,000+ `CPU Use` interval markers per test).
- Got: `thread markers` aggregates `CPU Use` by duration only (min/avg/max 93–107 ms), not by its
  `CPU Percent` field; `counter list` says "No counters in this profile".
- Could have shown: field statistics for numeric marker fields in the aggregated view (mean,
  percentiles of `cpuPercent` over the range), or the CPU Use markers exposed as a counter.

## Question: the exact start time of a marker, to zoom between two of them

- Command: `profiler-cli marker info m-6 m-21 --session S`
- Expected: `Time: 241.492s`.
- Got: `Time: 4m1s (instant)`; the list view also prints `t=4m1s`. Past one minute the text
  output rounds to the second, too coarse to zoom on a test's start. Workaround:
  `marker info ... --json` and read `start`.
- Could have shown: milliseconds in `marker info`'s Time line.

## Question: how many browser windows were alive at once during this test?

- Command: `profiler-cli thread markers --session S --search "name:DocShell" --list --limit 0 --json`
  on a debug resource-usage profile, then a script counting parent `browser.xhtml` DocShell
  intervals overlapping given times.
- Expected: some way to ask "how many of these interval markers overlap time T" (or the peak
  concurrency over a range), as `thread network` already does for requests ("peak concurrency").
- Got: counts and durations per name only.
- Could have shown: peak / time-sampled concurrency of interval markers matching a search.

## Question (review): a link to a marker in a profile opened from a report's profiler link

- Command: `profiler-cli load "<the report's profiler.firefox.com/from-url/... link>" --session S`
  (as the review brief says: loading the link opens it at its thread and range), then
  `python3 $D/profile-link.py --session S --marker m-183`.
- Expected: a link to that marker in the same profile.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or
  a profiler link.` for every call; the session had to be stopped and the same profile reloaded
  from the raw artifact URL (another multi-minute download and symbolication), and every marker
  handle found so far re-found.
- Could have shown: profile-link.py could take the artifact URL out of the `from-url/` link the
  session loaded (it is right there, URL-encoded), instead of refusing it.
