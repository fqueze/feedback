# profiler-cli feedback — test_errorhandler_1.js

## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=test_errorhandler_1.js profiler-cli load "<taskcluster url>" --session test_errorhandler_1.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then "Unknown session".
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (the error message suggested it, which helped). One wasted call; the brief does not mention the variable.

## `thread network --sort start` prints no start times

- Command: `profiler-cli thread network --session test_errorhandler_1.js-1 --sort start --limit 0`
- Question: when did each request start, relative to the test's other markers?
- Expected: a `t=` per request, as `thread markers --list` prints.
- Got: chronological order, phases and durations, but no timestamp. The request order was there, the times were not.
- Workaround: `thread markers --category Network --search localhost --list --limit 0 | rg Load`.

## `zoom push` past the end of the profile is accepted silently (review-test_errorhandler_1.js)

- Command: `profiler-cli zoom push 175,189 --session review-test_errorhandler_1.js-1`, on a 193 ms profile, with
  the range copied from `t=175.80ms`-style marker times.
- Expected: an error or a warning, since 175 s to 189 s lies entirely after the profile's end.
- Got: the zoom was pushed (`View: ts>10S to ts>10W (14s)`), and the next `thread markers` said "No markers match
  the specified filters", which reads as a filter problem, not a range one.
- Workaround: `zoom push 0.175,0.189`. One wasted call. A `ms` suffix, or a warning when the range is outside
  `Full: 193.14ms`, would have caught it.
