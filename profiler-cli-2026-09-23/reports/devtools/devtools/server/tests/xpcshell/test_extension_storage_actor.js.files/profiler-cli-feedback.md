## Large profile kills the daemon with SIGABRT (Node heap limit), with no hint in the log

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/BVocTvHpQFyOEPkRU7jNpw/runs/0/artifacts/public/test_info/profile_test_extension_storage_actor.js.json --session test_extension_storage_actor.js-1`
  (65 MB gzipped per-test xpcshell profile)
- Expected: the profile loads, or an error saying the heap limit was hit and how to raise it.
- Got: first try, "Session ... is not reachable. The daemon exited without cleaning up." Second try, "The profiler-cli daemon died while loading the profile (killed by signal SIGABRT)". The daemon log's last line is "Fetching profile from ...", nothing about memory.
- Workaround: `NODE_OPTIONS=--max-old-space-size=8192` in front of `profiler-cli load` made it load. The daemon could raise its own heap limit, or detect SIGABRT during load and suggest it.

## Question: "how busy was the machine while this test ran?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <ru> --search "name:CPU Use" --list --limit 0` after `zoom push m-<test marker>`
- Expected: the CPU percent of each `CPU Use` marker, or a summary (mean/max over the zoomed range).
- Got: one row per marker with only the name, time and duration; the CPU percent is only in `--json` (`data.cpuPercent`), so I needed a Python script to average it per 2 s bucket and per minute.
- Could have shown: the `CPU Percent` field in the list row, or a per-bucket summary like `counter info` gives for counters (this profile has no counters, the CPU data is only in markers).

## (review) `marker info --json` truncates `rawFields[].value` to 200 chars, which leaves invalid JSON

- Command: `profiler-cli marker info m-1 --session review-test_extension_storage_actor.js-2 --json`, on a `GCSlice` marker
- Expected: the full `timings` payload, including `phase_times`, which tells how far the slice swept.
- Got: `rawFields[0].value` is a JSON string cut at about 200 chars, so `json.loads` on it fails ("Unterminated string"). The non-JSON output is cut too (`timings: {...,"m…`).
- Workaround: `zoom push` onto the slice, then `thread markers --search name:GCSlice --list --json`: there `data.timings` is a complete object.

## (review) Question: "which GC state was the parent in when this IPC message arrived?"

- Command: `profiler-cli thread markers --session <s> --search name:GCSlice --list --limit 0`
- Expected: each slice's `initial_state → final_state` (and `major_gc_number`) in the list row, as `GCSlice` rows carry no other label.
- Got: rows with only name, time and duration, so I needed a Python script over `--json` to put the slice states next to the `getWatcher()` and `PBrowser::Msg_OnLocationChange` markers.
- Could have shown: `Prepare→Mark #2` as the row's label, as the profiler UI does in its tooltip.
