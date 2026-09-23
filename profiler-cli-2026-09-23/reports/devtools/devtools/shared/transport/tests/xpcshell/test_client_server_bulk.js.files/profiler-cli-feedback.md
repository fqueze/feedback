## Bare `-term` exclusion in `thread markers --search` silently matches nothing
- Command: `profiler-cli thread markers --session <id> --search "-name:test,-name:CPU Use,...,-not killing,-xpcshell return code" --list` (zoomed to 118,127)
- Expected: either the free-text exclusions applied, or an error saying only `-field:value` exclusions are supported.
- Got: "0 markers ... No markers match the specified filters." Without the two bare `-term` entries, the same query returned 144 markers.
- Workaround: pipe the output through `rg -v`.

## `thread markers --list` of `CPU Use` markers doesn't show the CPU value
- Command: `profiler-cli thread markers --session <id> --search "name:CPU Use" --list` (zoomed)
- Expected: the CPU percentage in each row, to answer "was the machine saturated at t?".
- Got: only the handle, the time and the duration. It would need a `marker info` per marker.

## (review) `thread markers --list` drops sub-second precision past one minute
- Command: `profiler-cli thread markers --session <id> --search "name:test" --list --limit 0`, and a zoom to 100.735,100.750
- Expected: start times with milliseconds all through the profile, as before 60 s (`t=55.731s`).
- Got: `t=1m41s` for every marker in a 15 ms window, and a bare `t=1m` for tests started at 60–70 s. Marker order within a second can't be read, and a script counting tests by start time silently missed the 318 `t=1m` rows.
- Workaround: `marker info <handles> --json` for the exact `start`.
