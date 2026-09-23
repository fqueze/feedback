## Which came first within the same second: the crash or the launch failure?

- Command: `profiler-cli thread markers --session <s> --search "Killing background,Detected crash,Failed to launch" --list --limit 0`
- Expected: timestamps with ms precision, so I could order the markers.
- Got: every marker past one minute is printed as `t=2m12s`, with no sub-second part. Nine markers in a row all read `t=2m12s`.
- Workaround: `--json` and a Python one-liner printing `start/1000`.
- What would have answered it: print `t=2m12.595s` (or seconds with 3 decimals) in `--list` mode.

## Which tests were running at time t?

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a script (`running_at.py`) that keeps markers with start <= t <= start+duration.
- Expected: a way to list the interval markers overlapping one instant.
- Got: `zoom push` keeps markers that overlap the range, but the list only shows start and duration, so I could not tell at a glance which ones were still running at the instant.
- What would have answered it: a `--at <t>` filter for interval markers, or an end time column in `--list`.

## Did any test pass after time t?

- Command: same JSON, then a script (`after.py`) counting `data.status` for tests that started before, straddled, or started after t.
- Expected: `thread markers --search name:test --group-by field:status` inside a `zoom push t,end`.
- Got: the zoom keeps markers that started before t but overlap it, so the counts mixed "running at" with "started after". I needed a strict "started after t" split.
- What would have answered it: an option to filter by start time only (`--started-after <t>`).
