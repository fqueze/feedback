# profiler-cli feedback — test_ext_contentscript_context.js

## Question: what was the machine's CPU use during one test's time range?

- Command: `profiler-cli zoom push m-1` (the test's `test` marker in `profile_resource-usage.json`), then
  `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: each row, or a summary line, giving the CPU percent (the payload's `cpuPercent`).
- Got: 784 rows reading only `CPU Use  t=3m34s  99ms`; the percentage is only in `marker info`, one
  marker at a time. The resource-usage profile has no counters, so `counter info` cannot answer it
  either.
- Workaround: `--list --limit 0 --json` and a Python script averaging `flatMarkers[].data.cpuPercent`
  per 10 s bucket.
- What the output could show: the CPU percent in each `CPU Use` list row, or a
  min / mean / share-at-100% summary of `CPU Use` markers over the current zoom (the question behind
  "was the machine saturated when this test timed out").

## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session <id>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set
  `PROFILER_CLI_SESSION_DIR`. One wasted call; the hint was enough.
