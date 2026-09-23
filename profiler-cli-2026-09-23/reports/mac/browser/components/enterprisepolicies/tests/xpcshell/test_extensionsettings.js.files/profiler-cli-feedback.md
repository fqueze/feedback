# profiler-cli feedback (test_extensionsettings.js)

## Question: "what was the machine's CPU use while this test ran?" (resource-usage profile)

- Command: `profiler-cli counter list --session <ru>` then
  `profiler-cli thread markers --session <ru> --search "name:CPU Use" --list --limit 0` after `zoom push m-1` (the test's `test` marker).
- Expected: the CPU percentage over the zoomed range, as a track summary or at least one value per row.
- Got: `No counters in this profile.`; the list prints one row per 100 ms `CPU Use` marker with only its
  duration, no `cpuPercent`. `marker info m-802..m-1164` then refused: "covers 363 handles, more than the maximum of 256".
- Workaround: `--json` list for the handles, `marker info` in chunks of 200 with `--json`, and a Python
  script averaging `cpuPercent` per 3 s bucket. Five calls and a script per window, for a number the
  resource-usage profile exists to answer. A `counter`-like view of the `CPU Use` markers (mean/min/max over
  the zoom, bucketed like "CPU activity over time") would answer it in one call.

## `--search name:task` matched `TaskController::AddTask` and hung

- Command: `profiler-cli thread markers --session <per-test> --search "name:task" --list --limit 0`
- Expected: the 20 `task` markers (one per subtask).
- Got: substring match on 431,258 `TaskController::AddTask` markers; still running after 120 s, killed.
- Workaround: `--category Test`, which includes the `task` markers. An exact-name form (`name=task`) would avoid it.

## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session test_extensionsettings.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message suggested
  `PROFILER_CLI_SESSION_DIR`, which worked. One wasted call; the brief does not mention the variable.

## (review) `zoom pop` leaves the samples commands on the popped range

- Commands, on the Linux per-test profile of MKiOFl-6RcaqTI4Wr9y5ow (session review-…-3):
  `thread samples --include-idle` (2,605 samples, 940 Idle), then `zoom push 2.604,7.740`
  (420 samples), then `zoom pop`; `status` says "View range: Full profile".
- Expected: `thread samples`, `samples-top-down` and `samples-bottom-up` back on the full profile.
- Got: all three still report the popped range (420 samples with idle, 234 without), under a
  header that reads `View: Full profile`. `thread markers` was not affected. A later
  `zoom push` with a new range recomputes.
- Cost: whole-profile function counts I had taken for the full 30 s were the 5 s window's; caught
  only because a count matched the zoomed one exactly. Workaround: `zoom push 0,<end>` instead of
  popping back to the full profile.

## (review) Question: "how far apart were this thread's samples?"

- Mac per-test profile of MW0liHVvRzCj_6YnjS9Wpw: `profile meta` says `Sampling interval: 10ms`,
  but `samples-top-down --json` totals 488 samples over 30.874 s, one per 63 ms on average, and
  52 in a 3.3 s window where a reader expects about 330.
- Expected: `thread info` or `thread samples` saying the actual sample count and spacing next to
  the nominal interval (mean and max gap), so a missing-sample gap is not read as idle time.
- Got: only the nominal interval; the count needs `--json`, and the spacing is not shown anywhere.
