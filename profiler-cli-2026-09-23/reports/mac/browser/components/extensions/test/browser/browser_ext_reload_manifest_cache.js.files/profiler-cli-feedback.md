## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session browser_ext_reload_manifest_cache.js-1`
- Expected: the session loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message did say what to do.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (`profile-link.py` honours it as well). The subagent brief does not mention this variable.

## Question: which tests ran in this browser session, in order, with their status?

- Command: `profiler-cli thread markers --category Test --search "name:test" --list --limit 0`
- Expected: only the per-test `test` interval markers (`PASS — <path>` / `FAIL — <path>`).
- Got: 1,707 markers. `name:` is a substring match, so it also catches `TestUtils`, `TEST-PASS`, `task` and others.
- Workaround: `--search "browser/components/extensions/test/browser/"` piped through `grep -E "^\s+m-[0-9]+\s+test "`.
- What would have answered it: an exact-name match (e.g. `name:=test`), or a `profile tests` summary that lists each test with its start, duration and status.

## Default selected thread differs between two per-test profiles of the same job type

- Command: `profiler-cli load .../profile_browser_ext_commands_execute_page_action-2.js.json --session browser_ext_reload_manifest_cache.js-2`, followed by `thread markers --category Test ...`
- Expected: parent-process GeckoMain selected, as for the first profile (session -1 picked t-0).
- Got: t-22 (WebExtensions GeckoMain) selected. The Test search printed "0 markers" and gave no hint that the wrong thread was selected, apart from the header line.
- Workaround: an explicit `thread select t-0`.

## (review) Question: which document does each `RefreshDriverTick waiting for paint` belong to?

- Command: `profiler-cli thread markers --session <s> --search "name:RefreshDriverTick" --list --limit 0`, then `marker info m-N --json`, then `thread markers --search "innerWindowID:4294967338"`
- Expected: the marker's inner window, ideally resolved to the page URL from the profile's pages table, in the list or in `marker info`.
- Got: neither `--list` nor `marker info` (text or `--json`) shows `innerWindowID`, and `--search "innerWindowID:<id>"` matches 0 markers. Only `thread markers --json` exposes it, under `flatMarkers[].data.innerWindowID`, as a bare number. The original report concluded that these markers name no document, and so left its key link to rest on timing.
- Workaround: a script over `thread markers --json` that groups the ticks by `data.innerWindowID`, and matches the IDs against the `DocumentLoad` / `nsRefreshDriver` markers of each `popup.html`.
- What would have answered it: an inner-window/page column in `--list` (URL from the pages table), `innerWindowID` in `marker info`, and `innerWindowID:` or `page:` accepted by `--search`.
