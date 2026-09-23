## Marker list timestamps lose sub-second precision after the first minute

- Command: `profiler-cli thread markers --session <s> --search "will retry,Error:-2147009284" --list --limit 0`
- Expected: timestamps precise enough to order events a few ms apart (e.g. `t=131.558s`).
- Got: `t=2m12s` for every marker in that second, so the order of the first harness launch failure, the first in-process launch failure and the preceding child crash could not be read.
- Workaround: `--json` and print `start/1000` with a script.

## Question needing a script: "which tests were running at time T?"

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json` piped into a script filtering `start <= T <= start+duration`.
- The default output could answer it with a `--at <t>` / `--overlapping <t>` filter for interval markers (the `test` markers of a resource-usage profile), listing start, end and label.
