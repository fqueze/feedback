## Listing only the per-test-file `test` markers (one PASS/FAIL row per test file)

- Question: which test files ran in this browser before mine, and which failed?
- Command: `profiler-cli thread markers --category Test --search "browser/components,vsync,TEST-UNEXPECTED" --list --limit 0 --session S`
- Expected: the `test` markers (`PASS — <path>`) plus the vsync waits.
- Got: every INFO/TEST-PASS marker too, since each carries the test path in a payload field. `--search name:test` also matches every Test-category marker.
- Workaround: `--search name:test ... | grep " test  "`. A way to filter on the marker's own name exactly (e.g. `--name test`) would answer it directly.

## `--search` does not match fields that have no schema (innerWindowID)

- Question: which other markers belong to the window whose refresh driver is stuck (innerWindowID 4294967338)?
- Commands: `profiler-cli thread markers --search "innerWindowID:4294967338" --session S` and `--search 4294967338` — both 0 matches.
- Expected: the DocumentLoad / RefreshDriverTick / ViewManagerFlush markers carrying that innerWindowID (they do: `marker info` lists it under "Other payload fields (no schema)", and `--group-by field:innerWindowID` groups on it).
- Workaround: `marker info` on candidate markers one by one until the DocumentLoad for popup.html showed the same innerWindowID.

## (review) Loading a raw Taskcluster profile URL selects a WebExtensions thread, not the parent main thread

- Question: what does the parent's test log say in job IT0S_eCQRJ-SkMnxZ3h6jA?
- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/IT0S_eCQRJ-SkMnxZ3h6jA/runs/0/artifacts/public/test_info/profile_browser_ext_commands_execute_sidebar_action.js.json --session S`, then `thread markers --category Test --search "No anchor node" --list`
- Expected: the parent's GeckoMain thread (t-0) selected, since the Test markers live there.
- Got: `t-14 (GeckoMain, WebExtensions)` selected, so the search returned 0 markers, with nothing saying they were on another thread.
- Workaround: `thread select t-0`. Selecting the parent main thread by default, or adding "0 here; N on t-0" to an empty result, would have avoided the extra call.
