## `load` returned before the profile was usable

- Command: `profiler-cli load "<taskcluster url>" --session <s> >/dev/null 2>&1`, then `profiler-cli thread select t-0 --session <s>`
- Expected: `load` to return once the profile is loaded (it did for the first profile of this investigation).
- Got: for the second profile, `load` returned, and the next commands failed with `Error: Profile still loading, try again shortly`.
- Workaround: poll `profiler-cli status --session <s>` until it stops saying "still loading" (about 1–2 min).

## `zoom push` takes seconds, while marker `--json` `start` values are in ms

- Command: `profiler-cli zoom push 306707,350400 --session <s>` (values copied from `flatMarkers[].start`)
- Expected: the range of those markers.
- Got: a 12-hour range with 0 markers, plus a warning suggesting the `ms` suffix.
- Workaround: `zoom push 306707ms,350400ms`. Either unit would do, as long as the JSON and `zoom push` used the same one.

## (review) `load` exits 1 on its 60 s timeout, while the daemon goes on loading

- Command: `profiler-cli load "<profiler.firefox.com from-url link>" --session <s>` (Windows profile of ML56zK70SaGuils82VeavQ)
- Expected: `load` to wait until the profile is usable, or to say that it is still loading in the background.
- Got: exit 1 with `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`; `status` then said "still loading", and the session became usable about a minute later.
- Workaround: poll `status`; for the next profile, `PROFILER_CLI_LOAD_TIMEOUT_MS=500000`.

## (review) Question: the exact time and order of a set of markers

- Command: `profiler-cli thread markers --search "Entering test,TabGroupCreate" --list --limit 0`
- Wanted: which two `TabGroupCreate` events are how many ms apart, and which subtest each fell in.
- Got: times rounded to the second (`t=5m2s`), so two events 0.3 ms apart, and the gap between them, could not be read.
- Workaround: `--json` and a script printing `flatMarkers[].start`. The list could print ms times, at least when rows share a second.
