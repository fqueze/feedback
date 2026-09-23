## `thread samples --include-idle` does not say how many samples were idle

- Command: `profiler-cli thread samples --include-idle --session <s>` after `zoom push 2.468,3.118`.
- Expected: running and idle sample counts, to tell whether the main thread was busy or idle in the range.
- Got: `Categories (17 running samples)` with no idle count, even though `--include-idle` was passed. I had to work out that 650 ms at 10 ms is about 65 samples.

## (review) The missing idle count led to a wrong conclusion in the report

- Command: `profiler-cli thread samples --include-idle --session <s>` after `zoom push 2.468,3.118` on `FUsj5tAeQhuo_yMJMTCgDA` `profile_0_2771.json`.
- Expected: the number of samples in the range, with how many were idle.
- Got: `Categories (17 running samples)`. The workaround in the entry above ("650 ms at 10 ms is about 65 samples") was wrong: this debug profile got about 26 samples a second, so 17 was the whole range and none were idle. The report said the thread was "mostly idle" there. `thread info` gave the right answer (`88% for 558.0ms`), but it takes a second command to get it.
- Ask: print the range's total and idle sample counts, and the effective sample rate when it is far below the configured interval.
