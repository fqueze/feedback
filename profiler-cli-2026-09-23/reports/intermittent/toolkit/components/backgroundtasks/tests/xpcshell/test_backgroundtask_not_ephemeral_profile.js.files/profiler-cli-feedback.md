## Which background tasks did the job launch, and with which arguments?

- Command: `profiler-cli thread markers --search "launching background task" --list --limit 0 --session <id>` on a resource-usage profile.
- Expected: each launch's `args:[...]` visible, so I could tell `--backgroundtask message --profile /tmp/...` apart from `--backgroundtask not_ephemeral_profile`.
- Got: each row cut at the terminal width, before the args that matter.
- Workaround: `--json`, then a Python regex over `flatMarkers` to pull out `args:[...]`. A `--full-messages` flag, like fx-tests has, would have answered this directly.
