## Question: in which order did these markers, less than 1 ms apart, happen?

- Command: `profiler-cli marker info m-81 m-70 m-122 m-11 m-123 --session <id>` on a 3m22s profile
- Expected: each marker's time with sub-second precision (e.g. `t=3m20.617s`), so the order of a metric set, an observer notification and a test failure can be read directly.
- Got: `Time: 3m21s (instant)` for all of them; the `--list` view also rounds to `t=3m21s`. The metric set (200.617 s), the notification (201.147 s) and the failure (201.149 s) are indistinguishable.
- Workaround: `--json` and read `start`/`end` in ms.

## Question: which FOG-related markers does this thread have, grouped by name?

- Command: `profiler-cli thread markers --session <id> --search fog --limit 30` (aggregated view, no `--list`) on a parent main thread of 3,017,231 markers
- Expected: the grouped summary, as quick as the `--list` form of the same search (which answers in seconds).
- Got: `Error: Timed out after 30000ms waiting for the daemon ... to answer.` The next command also timed out while the daemon was still busy.
- Workaround: use `--list` with a narrow `--search`.

## Question: where is the marker the list shows as `characteristics.svg_bbox : {...}`? (review)

- Command: `profiler-cli thread markers --session <id> --search "characteristics.svg_bbox" --list --limit 0` on a `String::set` Telemetry marker whose list row reads `characteristics.svg_bbox : {"x":…}`
- Expected: that marker, since the search text is copied from the row the tool printed.
- Got: `No markers match the specified filters.` The row is built from two payload fields, `cat` = `characteristics` and `id` = `svg_bbox`, and no single field holds the joined text. `--search svg_bbox` finds it.
- Workaround: search for one field's value only, after checking the keys with `marker info --json`. What could show it: match `--search` against the row as printed, or say in the "no match" message that the row text combines several fields.
