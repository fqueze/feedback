## Question: at the moment this test launched its updater, how many tests of its directory were running, and which failing ones launched within 3 s?

- Command: `scan2.py` in this directory, over 19 resource-usage profiles: `thread markers --search unit_update_binary --list --limit 0 --json`, then Python over the `test` markers' start/duration and the `launching the program` log lines' starts, plus `--search "CPU Use"` for the machine CPU over the 30 s after the launch. About 20 minutes.
- What could have shown it: `thread markers --at <t> --search name:test` (interval markers overlapping an instant), and millisecond times in the `--list` column when rows share a second (already asked for in `marFailurePartialZucchiniBadAlloc.js.files/profiler-cli-feedback.md`).

## Question: which failure mode is each of the 22 failing jobs in?

- Command: `scan.sh` in this directory: per job `load`, `thread markers --search <test>.js --list --limit 0`, `stop`, then grep for `Callback log does not exist`, `ACCESS_DENIED`, `launching the program`. Same need as the sibling report's; a batch count over several profiles would have answered it without 22 daemons.
