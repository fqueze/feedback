## Question: how did one pid's process name change across the harness's `get_process_list` log lines?

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0 --json` (78 MB of JSON for 1,415 markers), then a Python script that parses each label as a Python literal and tracks name changes per pid.
- Expected/wanted: this is harness-log specific, so it is probably not profiler-cli's job. Noting it because the default `--list` output truncates each label to the terminal width, so even a single ps listing cannot be read without `--json` or `marker info`. A `--full` or `--wrap` option for `--list` that prints the whole label would have let me read the 3–4 listings around each failure without a script.

## Question (review): in what order did harness log lines within one second happen?

- Command: `profiler-cli zoom push 665.5,676 --session <s>` then `profiler-cli thread markers --list --limit 0 --session <s>`.
- Expected: timestamps precise enough to order markers a few ms apart (a ps listing, then `Launched Test App`, then the failure message).
- Got: `t=11m6s` for every marker in that second, so the order within a second, and the gaps, cannot be read. Workaround: `--json` and a script printing `start/1000` to 3 decimals.
- Could have shown: millisecond precision (`t=666.064s`, or `11m6.064s`) in `--list` when zoomed to a range under a minute, or as an option.
