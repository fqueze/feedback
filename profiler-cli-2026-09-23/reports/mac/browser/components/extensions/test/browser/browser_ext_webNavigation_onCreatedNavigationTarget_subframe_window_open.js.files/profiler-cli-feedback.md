## Question: at what time, to the millisecond, did each marker in this list happen?

- Command: `profiler-cli thread markers --category Test --search subframe_window_open --list --limit 0 --session bwocnt-sfwo-1` on a 7 min per-test profile (YpSGykRmTAimSGoj0tse0g).
- Expected: start times precise enough to order markers against each other and against markers on other threads (e.g. `t=418.484s`).
- Got: `t=6m58s` for every row from the test's first `INFO` to `checking for open popups`, and `t=7m7s` for the failure; `profile markers --search RefreshDriverTick` likewise printed `t=6m59s` for hundreds of rows. Nothing in the text output could be lined up against another thread's markers.
- Workaround: `--json` and a Python one-liner printing `start/1000` with 3 decimals, on each list I needed.
- Could show: seconds with millisecond precision (`418.484s`) once the profile is longer than a minute, or at least whenever a list has several rows sharing one rounded value.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session review-subframe_window_open-1 thread markers --category Test --search subframe_window_open --list --limit 0`
- Expected: a global option like `--session` to be accepted in front of the subcommand too, as `git -C` or `cargo --manifest-path` are.
- Got: `error: unknown option '--session'` and `(Did you mean --version?)`, which points away from the real fix.
- Workaround: put `--session` after the subcommand's arguments.
- Could show: accept it in either position, or say "`--session` goes after the subcommand".
