## Which document does this marker belong to? (browser_ext_find.js)

- Command: `profiler-cli thread markers --session browser_ext_find.js-1 --search innerWindowID:4294967338` and `--search 4294967338`, on the WebExtensions GeckoMain thread.
- Expected: every marker whose payload has `innerWindowID: 4294967338`. `marker info` shows that field on `RefreshDriverTick waiting for paint`, `DocumentLoad`, `DOMEvent`, `nsRefreshDriver`.
- Got: `No markers match the specified filters.` Nor is there a command that lists the profile's pages (innerWindowID to URL).
- Workaround: listed DocumentLoad markers by hand and compared their innerWindowID with `marker info` on each candidate, one at a time. `thread markers --list --json` would not help either: `flatMarkers[]` carries no payload fields, so it cannot be filtered on innerWindowID with jq.
- What would have answered it: innerWindowID searchable like other payload fields, and the page URL for it shown in `marker info` (and in `--list` rows).

## Session directory not writable in a sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_ext_find.js profiler-cli load <taskcluster URL> --session browser_ext_find.js-1`
- Expected: a loaded session.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message did suggest `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked. It cost one round trip.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call, and for `profile-link.py`.
