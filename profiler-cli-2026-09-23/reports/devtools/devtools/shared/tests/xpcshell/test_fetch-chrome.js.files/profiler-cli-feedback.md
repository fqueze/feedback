## Question: in which order did markers within the same second happen?

- Command: `profiler-cli thread markers --session <s> --search test_fetch-chrome --list --limit 0`, then the same for `get_process_list`.
- Expected: list times precise enough to order the harness's `launch_application` (166.046 s), `Launched Test App` (166.376 s) and the `get_process_list` snapshots (166.373 s, 166.405 s) against each other.
- Got: past one minute every row prints `t=2m46s`, so all of them look simultaneous.
- Workaround: `--json` and a script reading `start`. A `--precise-times` flag, or millisecond precision in `--list` rows once zoomed below a few seconds, would have answered it.

## `marker info --json` has a different top-level shape for one handle than for several

- Command: `profiler-cli marker info m-1 --session <s> --json` vs `profiler-cli marker info m-1 m-7 --session <s> --json`.
- Expected: the same shape, e.g. always `{"markers": [...]}`.
- Got: several handles give `{"type","requested","markers","errors","context"}`; one handle gives the bare marker object, so a script written for one fails (`KeyError: 'markers'`) on the other.
- Workaround: always pass two handles.
