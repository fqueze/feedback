## `zoom push` silently misreads the minute format the tool itself prints

- Command: `profiler-cli zoom push 7m40s,14m45s --session <s>` (times copied from `thread markers --list`, which prints `t=7m48s`)
- Expected: a zoom on 460 s to 885 s, or an error saying only seconds are accepted.
- Got: `Pushed view range: ts-1 (7s) to ts-2 (14s)`, and the next listing was the first 7 seconds of the job with no warning.
- Workaround: converted to seconds by hand (`zoom push 440,900`).
