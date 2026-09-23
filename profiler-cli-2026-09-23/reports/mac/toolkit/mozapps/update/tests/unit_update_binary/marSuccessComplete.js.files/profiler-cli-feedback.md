## `load` of a large per-test profile exits 0 and leaves no session

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marSuccessComplete.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/COszRY6cQ3eYMfxkW91UAg/runs/0/artifacts/public/test_info/profile_marSuccessComplete.js.json" --session marSuccessComplete.js-1` (55 MB gzipped, 883 MB of JSON)
- Expected: the profile loads, or `load` exits non-zero saying the daemon died (out of memory).
- Got: `load` printed only `Loading profile from ...` and returned; the next command said `Unknown session marSuccessComplete.js-1` after "Cleaned up 1 stale sessions". The daemon log ends at `Fetching profile from ...`, no error. Same as the sibling reports in this directory.
- Workaround: HEAD every failing task's artifact for `x-goog-stored-content-length` (58 requests) and picked those under 25 MB. Half of this test's failing runs (all opt and shippable, most bare-metal debug) cannot be read. `fx-tests task --profiles` / `fx-tests test --task-ids --profiles` could print each profile's size.

## Question: "which tests were running while my test's helper process ran?" (resource-usage profile)

- Command: `profiler-cli thread markers --search unit_update_binary --category Tasks --list --limit 0 --json --session marSuccessComplete.js-3`, then a Python script over `flatMarkers` keeping `test` markers that overlap a time window, dropping SKIPs.
- Why a script: `zoom push` keeps markers that start in the range, not those that overlap it, and there is no `--min-duration` on the list to drop 1 ms SKIPs. The default output could have shown: `thread markers --overlapping <start,end|m-N>` with start, end, status per row.

## Question: "what was the machine's CPU, second by second, over this range?" (resource-usage profile)

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0 --json` inside a zoom, piped to a script averaging `data.cpuPercent` per 2 s bucket. Same need as the sibling reports: a bucketed summary like `counter info`'s "over time" for `CPU Use` markers.
