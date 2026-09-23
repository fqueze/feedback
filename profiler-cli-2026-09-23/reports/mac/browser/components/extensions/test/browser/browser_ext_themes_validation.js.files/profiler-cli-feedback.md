## Question: which document's refresh driver keeps vsync on during a time range

- Command: `profiler-cli thread markers --thread t-20 --search "waiting for paint" --list --limit 0` (with and without `zoom push` on the vsync wait)
- Expected: a way to see the markers grouped by the document they belong to (innerWindowID or URL), with count, first/last time and max gap.
- Got: thousands of identical rows `RefreshDriverTick waiting for paint ... instant` with no innerWindowID shown; `--group-by field:innerWindowID` does not apply because innerWindowID is in `data`, not in `fields`.
- Workaround: `--json` then `jq 'group_by(.data.innerWindowID)'`, and matching the id against the `nsRefreshDriver initial timer start <url>` marker by hand. A `--group-by innerWindowID` (with the window's URL where a DocumentLoad marker names it) would have answered it directly.

## Question: which tests ran in this session, and with which status

- Command: `profiler-cli thread markers --search "type:Test" --list --limit 0`
- Expected: the per-test `test` interval markers (status + path) in order, as a short list.
- Got: every TestStatus/Log marker of every test mixed in; `--search name:test` also matches the `TEST-*` names and Text payload `name` fields.
- Workaround: `--json | jq 'select(.name=="test")'`. An exact-name match (e.g. `name=test`) would answer it.
- Correction to the first entry: I did not actually try `--group-by field:innerWindowID`; whether it works on `data.innerWindowID` is untested.
