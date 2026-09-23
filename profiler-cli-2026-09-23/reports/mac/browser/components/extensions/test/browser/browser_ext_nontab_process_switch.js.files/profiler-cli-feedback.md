## Default session directory is not writable under the agent sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_ext_nontab_process_switch.js profiler-cli load <taskcluster url> --session browser_ext_nontab_process_switch.js-1`
- Expected: the session starts.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call, including `profile-link.py`. It cost one round trip. Other agents in this batch logged the same thing.

## Question: "which window's refresh driver is ticking, and from when to when"

- Command: `profiler-cli thread markers --session <s> --search "innerWindowID:4294967338" --group-by name`
- Expected: the markers for that window. `--group-by field:innerWindowID` groups on that key, and `marker info --json` shows it under `rawFields`.
- Got: "No markers match the specified filters." `field:value` search does not match `innerWindowID`.
- Workaround: `thread markers --search "RefreshDriverTick waiting for paint" --list --limit 0 --json`, then a script grouping `data.innerWindowID` and printing the count, first and last time per window. The grouped text output could answer this by printing the first and last time of each group next to its count.

## Question: "in what order did the tests in this job run, and with which status"

- Command: `profiler-cli thread markers --session <resource-usage session> --search "components/extensions/test/browser/" --list --limit 0 --json`, filtered to `name == "test"` by a script.
- Expected: a way to list only the per-test `test` markers ("PASS — <path>", "FAIL — <path>") in order.
- Got: the search also returns every PASS/INFO/output marker of those tests, thousands of rows. `--search name:test` is ambiguous because `name` is a payload key too.
- Workaround: the script above. A name-only filter would answer it, or `--group-by name` letting you pick one group to `--list`.

## (review) Default session directory not writable, again

- Command: `PROFILER_CLI_SESSION_OWNER=review-browser_ext_nontab_process_switch.js profiler-cli load <profiler link> --session review-browser_ext_nontab_process_switch.js-1`
- Expected: the session starts.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, the same as above.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`. It cost one round trip.

## (review) profile-link.py refuses a session loaded from a profiler link

- Command: `python3 profile-link.py --session <s> --marker m-258` after `profiler-cli load <profiler.firefox.com/from-url/… link>`, which is how `review-brief.md` says to open a report's links.
- Expected: a link, to add an observation to the report.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: stop the session and reload the same profile from its Taskcluster URL. That is a second load of a 700k-marker profile. profile-link.py could get the artifact URL out of the `from-url/` link it was given.

## (review) Loading a Taskcluster URL selected the WebExtensions thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Xh6MnwM2R3OFkWAeuPuZLA/runs/0/artifacts/public/test_info/profile_browser_ext_nontab_process_switch.js.json --session <s>`, then `thread markers --search "waiting for vsync"`.
- Expected: the parent process main thread selected, as with the Mac profile's link (`thread=0`).
- Got: `t-22 (GeckoMain, WebExtensions)` was selected, so the search found "No markers". The result header named t-22, but I missed it. My `tail` had also cut the thread line out of the `load` output.
- Workaround: `thread select t-0`. Selecting the parent process main thread by default when no `thread=` is given would avoid it.
