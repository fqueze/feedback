## `zoom push` with minute-second times silently mis-parsed

- Command: `profiler-cli zoom push 19m50s,24m55s --session <id>` (times copied from the `t=19m51s` column of `thread markers --list`)
- Expected: zoom to 1190 s .. 1495 s, or an error.
- Got: success, but the status line showed `View: ts-1→ts-2 (5s)`; the following marker search returned 0 markers with no hint the range was wrong.
- Workaround: `zoom push 1190,1495` (seconds).
