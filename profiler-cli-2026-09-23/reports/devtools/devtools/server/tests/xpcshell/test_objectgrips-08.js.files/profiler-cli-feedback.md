## Question: "which tests were running at time T, and which ended just before it?"

- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, and then a Python script to intersect the intervals with T (the moment the first launch failure happened).
- Expected: a way to list the interval markers that overlap an instant. `zoom push T,T+0.001` followed by `--list` might have worked, but a marker that started before the zoom range is not clearly marked as overlapping, and a 45 s marker is listed the same way as an instant one.
- Could show: `thread markers --at <t>` (the interval markers covering t), plus `--ended-before <t> --within <s>`.

## Question: "how many tests ran concurrently?" (482 tests started within 1.4 s under a 20-thread limit)

- Same JSON export and a script that bucketed start and end times. A `--group-by` over start-time buckets, or a concurrency count for interval markers of one name, would have answered it.

## `marker info --json` records have no `handle`

- Command: `profiler-cli marker info m-1298 m-110 m-64 m-1318 --session ... --json`
- Expected: each record to carry its handle, so the output of a multi-handle call can be matched back to its handles.
- Got: `handle` missing (None). I relied on the order of the records instead.

## (review) `profiler-cli session stop <id>` gives a misleading error

- Command: `profiler-cli session stop review-test_objectgrips-08.js-1`
- Expected: the session stopped, or "unknown subcommand stop; use `profiler-cli stop <id>`".
- Got: `error: too many arguments for 'list'. Expected 0 arguments but got 2: stop, review-test_objectgrips-08.js-1.`
- Workaround: `profiler-cli stop <id>`.

## (review) Question: "is this marker handle the link's marker=N?"

- Command: `profiler-cli marker info m-14 --session ...`
- Expected: the marker's index in the output, since checking a `marker=N` link is the reason to call it.
- Got: no index in the text output; `--json` and a script were needed to read `markerIndex` for each of ~30 links.
- Could show: `Index: 4775` next to `Type:` in the default output.
