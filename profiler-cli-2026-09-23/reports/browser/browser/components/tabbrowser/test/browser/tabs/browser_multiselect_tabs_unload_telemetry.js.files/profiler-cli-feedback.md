## Question: "the test's log, without the console-message stacks"

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_multiselect_tabs_unload_telemetry --list --limit 0`
- Expected: one line per TEST-PASS / TEST-UNEXPECTED-FAIL / INFO marker.
- Got: each `INFO Console message: ...` marker printed with its full 50-line JS stack, so the 62 markers were several hundred lines, and the assertions were buried after the first four.
- Workaround: `--json` and jq, dropping labels matching "Console message" and cutting labels to 200 characters. A `--label-width`/single-line mode for `--list`, or truncating multi-line labels to their first line by default, would have answered it.

## `zoom push` with millisecond values silently zooms outside the profile

- Command: `profiler-cli zoom push 154295,154790` (values copied from `--json` `start`, which is in ms)
- Expected: an error or a warning, since 154295 s is past the 2m38s profile end.
- Got: a range shown as "8m15s" and "0 markers in view", with no warning.
- Workaround: divide by 1000. A warning when the range lies outside the profile, or accepting an `ms` suffix, would have saved the round-trip.

## Question: "link a marker I found while checking a report's profiler link" (review)

- Command: `profiler-cli load '<profiler.firefox.com/from-url/...?range=154550m85&thread=0>'` (as review-brief.md prescribes for checking a link), then `python3 profile-link.py --session <s> --marker m-8`
- Expected: a link to m-8, since the session knows the underlying Taskcluster URL (it is inside the from-url path).
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.` The review brief says to load the link; the link script refuses that session, so any new observation needs a second 0.6 GB load of the same profile.
- Workaround: stop the session and reload the raw Taskcluster URL. profile-link.py could decode the `from-url/<encoded>` segment itself.

## `load` reports "Error: Profile load timeout" but the session keeps loading (review)

- Command: `profiler-cli load --session <s> <taskcluster url>` (CHZDjjiuQJqhXjvVR7tCyQ, 2M+ markers on the parent thread)
- Expected: either success or a failed, cleaned-up session.
- Got: `Error: Profile load timeout after 60000ms`, yet the daemon lived on and `status` answered "Profile still loading" and then loaded fine about a minute later. An agent that trusts the error reloads, and holds two copies of 0.6 GB.
- Workaround: poll `status --session <s>` until it stops saying "still loading". Saying "still loading in the background, poll status" instead of "Error" would avoid the double load.
