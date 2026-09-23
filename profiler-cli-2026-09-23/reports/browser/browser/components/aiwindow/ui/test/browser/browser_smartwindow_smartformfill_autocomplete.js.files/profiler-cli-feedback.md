## `zoom push` silently misreads the time format the tool itself prints

- Command: `profiler-cli zoom push 13m52s,14m35s --session browser-sffauto-1`
- Expected: a zoom to 832 s..875 s, since `thread markers --list` prints times as `t=13m52s`.
- Got: `Pushed view range: ts-2 (13s) to ts-3 (14s) (duration: 1s)`, no warning; the next query returned 0 markers.
- Workaround: convert to seconds by hand (`zoom push 832,875`).

## `thread markers --list` prints times rounded to the second on long profiles

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0` on a 14-minute profile.
- Question: in which order did keydown, the login autofill `input` events and the IPC replies happen (all within 10 ms)?
- Got: every row `t=13m53s`; the order within that second is not visible. `marker info` also prints `Time: 13m53s`.
- Workaround: `--json` and print `start` myself. Millisecond precision in the text output (e.g. `t=833.348s`) would have answered it.


## `--session` before the subcommand is rejected (review)

- Command: `profiler-cli --session browser-review-sff-1 thread list`
- Expected: the global option accepted anywhere, as most CLIs do.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.

## `profile markers --json` and `thread markers --json` name their list differently (review)

- Commands: `profile markers --search X --json` returns `markers`; `thread markers --list --json` returns `flatMarkers`.
- Cost: one script had to handle both. The same key would do.
