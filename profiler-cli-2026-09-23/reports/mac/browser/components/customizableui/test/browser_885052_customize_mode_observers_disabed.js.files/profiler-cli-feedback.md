## Listing only the per-test `test` markers (status of every test in the session)

- Question: "which tests ran in this browser session, and with which status" — the `test` markers (`PASS — <path>` / `FAIL — <path>`).
- Command: `profiler-cli thread markers --session 885052-1 --category Test --search name:test --list --limit 60`
- Expected: only the markers whose name is exactly `test`.
- Got: every `TEST-PASS` too (substring/case-insensitive name match), 471 markers, the `test` rows buried among them. `--search "— browser/components"` matched nothing.
- Workaround: `--category Test --min-duration 300 --list --limit 0 | grep ' test '`, which misses short tests. An exact-name filter (e.g. `name:=test`) or a `--name` flag would answer it directly.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <url> --session 885052-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then a follow-up "Unknown session" error from the next command in the same shell line.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. The error message itself was clear; the cost was one round trip.

## Which handle is the marker a link's `marker=N` points to (review-browser_885052_customize_mode_observers_disabed.js)

- Question: "which marker is `marker=306703` in this link", to check a report's link against the marker it quotes.
- Command: `profiler-cli marker info m-1323..m-1340 --json --session review-885052-1`, then scan `markerIndex` by hand, after guessing the handle range from the marker's time.
- Expected: a lookup by index (e.g. `marker info --index 306703`), or `markerIndex` printed in `thread markers --list` rows.
- Got: no way to go from an index to a handle; `--list` shows neither the index nor a way to filter on it.
- Also: `marker info` with one handle gives the record at the top level of its `--json` output, but with several handles it nests the records under `.markers`. That cost two jq round trips.
