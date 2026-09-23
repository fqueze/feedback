## (review) `thread info --json` cpuActivity times disagree with their own `*Str` and with marker times

- Command: `profiler-cli thread info --json --session review-iframe-picker-1b` (JhERgFeCTyy6B8hT9Vv_1Q), zoomed 31.476,31.552
- Expected: `startTime`/`endTime` on the same base as `startTimeStr`/`endTimeStr` and as marker `start` (marker JSON 31550.839 prints as t=31.551s).
- Got: `startTime 31502.77` with `startTimeStr "31.495s"`, and `endTime 31548.62` with `"31.541s"`: a constant 7.35 ms offset (whole profile: `7.4538` against `"104.92μs"`). I could not tell whether the CPU burst ends at 31.541s or at 31.549s, which is the failure time.
- Workaround: quoted the CPU ms over the window without its exact bounds.

## (review) no way to search a literal containing `:`

- Command: `profiler-cli thread markers --search "priority: Idle" --list`
- Question: "which Idle-priority runnables ran in this window?"
- Got: 0 markers, because `priority` is parsed as a field name. `--search "Idle (0)"` then returned the wrong set (the guide's comma/field rules made it hard to tell why).
- Workaround: `--search Runnable --json` and a script filtering on `Idle (0)` in the description. It would help to have quoting, or `description:"…"`.
