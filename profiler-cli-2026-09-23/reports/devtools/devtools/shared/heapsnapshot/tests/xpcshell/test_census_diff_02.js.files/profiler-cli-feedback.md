## Question: "how did a process's name change across successive `ps` snapshots?"

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0 --json`, then a script (`renames.py`) diffing pid→name across snapshots.
- The default `--list` output cuts each message to the terminal width, which shows only the first few kernel threads of the 370-process list. Nothing in the default output could answer this. This is harness-log specific, so it probably belongs in fx-tests (a per-job "process names over time" view for Android) rather than in profiler-cli.
