
## review-2070011: `zoom push` silently misparses the time format the tool itself prints

- Command: `profiler-cli zoom push 6m33s,6m36s --session review-2070011-1`
- Expected: a 3 s range at the end of a 6m35s profile (markers are listed as `t=6m33s`, so that format looks accepted), or an error.
- Got: `Pushed view range: ts-1 (6s) to ts-1 (6s) (duration: 0s)` with no warning; the following `thread markers --list` showed only markers overlapping t=6s.
- Workaround: `zoom push 393,396` (seconds).
