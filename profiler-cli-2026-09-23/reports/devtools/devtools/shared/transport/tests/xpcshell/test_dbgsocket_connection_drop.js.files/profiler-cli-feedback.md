## Question: "which instant markers happened inside this 20 ms window?"

- Command: `profiler-cli zoom push 100.750,100.770 --session <s>` then `profiler-cli thread markers --list --limit 40 --session <s>`
- Expected: the markers that happen inside the window, e.g. the `Begin/End of full log` and ERROR lines of the test whose log was replayed there.
- Got: 1,471 markers "in view". The first 40 were all interval markers that only overlap the window: the `run-tests` Phase, `parallel`, and ~1,400 `test` markers that started 45 s earlier. The instant markers at the window were not in the first 40 rows.
- Workaround: `--list --limit 0 --json`, then a script keeping only markers whose `start` falls inside the window.
- What could answer it: a `--starts-in-view` option (or a default sort) that lists the markers starting inside the zoom first, or instants separately from intervals that span the whole window.

## Question: "how many tests timed out, and when did they start, relative to the first launch failure?"

- Command: `profiler-cli thread markers --search TIMEOUT --list --limit 0 --session <s>`
- Expected: a count and start-time range of the `test` markers with status TIMEOUT, before and after t=54.121s.
- Got: a list of 1,465 rows (plus `ERROR` rows matching the text). Counting and splitting them at a time needed `--json` and a script.
- What could answer it: `--group-by field:status` together with a start-time histogram, or `--before/--after <t>` counts in the aggregated view.
