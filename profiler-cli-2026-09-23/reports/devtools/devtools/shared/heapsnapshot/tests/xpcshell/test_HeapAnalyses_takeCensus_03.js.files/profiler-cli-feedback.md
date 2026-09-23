## `thread markers --list` rounds times past one minute to the whole second

- Command: `profiler-cli thread markers --session <s> --search "DEBUG,INFO" --list --limit 0` on the Android resource-usage profile of Qvlqn5S8SxS70kgUU9PocQ, zoomed to 178.2,180.4.
- Question: in what order did a dozen harness log lines within one second happen (the test's last setup line, the `ps` snapshots, the failure line)?
- Expected: millisecond times (e.g. `t=2m59.271s`), since the zoom was only 2.2 s wide.
- Got: every row printed as `t=2m59s` or `t=3m`, so rows within the same second could not be ordered or spaced.
- Workaround: `--json` and a script printing `start/1000` with 3 decimals.

## `--list` cuts long messages from the end, so the discriminating tail is lost

- Command: `profiler-cli thread markers --session <s> --search launch_application --list --limit 0`.
- Question: which slot (`XpcshellTestRunnerService$iN`) and which test (`_TEST_NAME = "..."`) did each launch use?
- Got: each row cut to the terminal width, after `am startservice -W -n '...$i3' -a android.intent.action.` and before `_TEST_NAME`. `marker info` on each one would work but is one call per marker, and there are hundreds.
- Workaround: `--json` plus a regex over `fields[].value`. A `--full-messages`-style flag or a `--field <key>` column selector would have answered it.

## Tracking a value across many markers' payloads needed a script

- Question: "what name did each pid have in each `get_process_list` snapshot over time". The harness logs a whole `ps` table as one DEBUG marker, 1,300 times per job.
- Command: `profiler-cli thread markers --search get_process_list --list --limit 0 --json` (72 MB), then a Python regex over each payload.
- This is specific to the harness's log format. It is noted as a question the default output could not answer, not as a bug.
