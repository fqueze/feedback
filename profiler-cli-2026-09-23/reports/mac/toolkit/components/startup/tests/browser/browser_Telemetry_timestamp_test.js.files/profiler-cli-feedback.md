## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session browser_Telemetry_timestamp_test.js-1`
- Expected: a session.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, and a follow-up
  error from the next command. The message did suggest `PROFILER_CLI_SESSION_DIR`, which worked.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call.

## Question: how many samples fall in this range? (review-browser_Telemetry_timestamp_test.js)

- Question: is the sampling in a 266 ms window dense enough to tell whether the main thread was
  ever idle there?
- Command: `profiler-cli zoom push 1.492,1.758` then `thread info` — it prints "This thread
  contains 7685 samples", the whole thread's count, ignoring the zoom. `thread samples
  --include-idle` gives only percentages (16.7% each, so 6 samples, by inference).
- What would have answered it: the sample count of the current view in `thread info` or in the
  samples header, and the largest gap between samples (here 0 samples in 1.56–1.625s despite a
  10 ms interval).
- Workaround: `thread samples --include-idle --json`, read `categoryBreakdown.totalSamples`,
  and repeat per sub-range to find the idle sample.
