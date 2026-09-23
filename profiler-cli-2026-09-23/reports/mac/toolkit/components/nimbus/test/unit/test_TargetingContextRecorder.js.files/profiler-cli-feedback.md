## `zoom clear` / `zoom pop` leave `thread samples` on the last zoomed range

- Command:
  ```
  profiler-cli zoom push m-86 --session S          # an 8.9 s marker
  profiler-cli thread samples --include-idle --json --session S   # totalSamples 141, currentViewRange set
  profiler-cli zoom clear --session S
  profiler-cli status --session S                  # "View range: Full profile"
  profiler-cli thread samples --include-idle --json --session S   # totalSamples 141, currentViewRange null
  profiler-cli zoom push 0,61 --session S
  profiler-cli thread samples --include-idle --json --session S   # totalSamples 921
  ```
- Expected: after `zoom clear`, the samples commands cover the whole profile (921 samples, as
  `thread info` says).
- Got: they keep returning the last zoomed range's samples (141, the same category split and the
  same call tree), while the header and `status` say "Full profile". Same after `zoom pop`: it
  returned the 264 samples of the last popped range. It made the whole 61 s profile look as if it
  held only the submit callback's stacks, which I nearly reported.
- Workaround: `zoom push <start>,<end>` covering the whole profile instead of `zoom clear`.

## Session directory not writable under the sandbox

- Command: `profiler-cli load <url> --session test_TargetingContextRecorder.js-1`
- Expected: a session.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message named
  the fix, so this cost one call.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call.
  The brief could say so next to `PROFILER_CLI_SESSION_OWNER`.

## Question: "how saturated was the machine while this test ran?" (min / share of CPU Use above a threshold)

- Command: `profiler-cli zoom push m-1` (the resource-usage `test` marker), then
  `profiler-cli thread markers --search "name:CPU Use" --group-by field:cpuPercent --top-n 200`
- Expected: a summary of the numeric field over the range: min, median, max, and the share of
  markers above a level.
- Got: one group per distinct value, sorted by count (dozens of groups for 143 markers on Linux). The answer is in
  there, but only after a Python script over the lines. The first attempt, reading the top 20
  groups, misled me into writing "94–100%" when the minimum was 49.5%.
- Workaround: a script over the `--group-by` lines. A numeric-field summary (`--stats
  field:cpuPercent`) would answer it directly.
