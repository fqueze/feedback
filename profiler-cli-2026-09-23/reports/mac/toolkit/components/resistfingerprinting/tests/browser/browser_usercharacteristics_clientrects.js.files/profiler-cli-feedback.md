## Sub-second order of markers more than a minute into the profile

- Question: in which order, to the millisecond, did `String::set characteristics.*`, `NotifyObservers user-characteristics-populating-data-done`, `TEST-UNEXPECTED-FAIL` and `Ping::submit` happen inside one second at t=85.3s?
- Command: `profiler-cli thread markers --search 'Ping::submit,String::set,TEST-UNEXPECTED' --list --limit 0 --session <s>`
- Expected: a start time precise enough to order markers that are ms apart.
- Got: every row reads `t=1m25s`; `marker info` also prints `Time: 1m25s (instant)`. Order within the second is only visible through `--json` (`.flatMarkers[].start`) and jq.
- Workaround: `--json | jq -r '.flatMarkers[] | "\(.start) \(.handle) \(.name) \(.label)"'`.
- What would have answered it: ms precision (e.g. `t=85.313s`) in `--list` rows, at least when neighbouring rows share the same rounded time.

## `--search` does not match the label shown for Glean metric markers

- Command: `profiler-cli thread markers --search 'characteristics.' --list --session <s>`
- Expected: the `String::set` / `Text::set` markers whose printed label starts with `characteristics.clientrects_element_gcr_01 : ...`.
- Got: 37 unrelated markers (test names containing the word). `--search clientrects` does match them (through the `Metric` field), so the printed label `category.metric` is composed at display time and not searchable.
- Workaround: `--category Telemetry --list --json | jq 'select(.label|startswith("characteristics."))'`.

## Zoom given in ms is taken as seconds and the next query is silently empty

- Command: `profiler-cli zoom push 85000,86000 --session <s> >/dev/null` then `thread markers ... --list --json`.
- Expected: an error, since the range is entirely outside the 85.7 s profile.
- Got: a warning on the push (which I had discarded) and then empty output from every following query, with nothing saying the view is empty because of the zoom.
- Workaround: `zoom clear`, then `zoom push 84.5,85.7`.

## Default session directory not writable in this sandbox

- Command: `profiler-cli load <url> --session <s>` with `PROFILER_CLI_SESSION_DIR` unset.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked. The brief does not mention the variable, so every agent here pays this once.
