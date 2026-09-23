## Default selected thread after `load` is not the parent main thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Sp7yAFe-SReiey4b_GdKxQ/runs/0/artifacts/public/test_info/profile_browser_library_telemetry-2.js.json --session <s>` then `thread markers --category Test --search browser_library_telemetry --list`
- Expected: the parent process GeckoMain selected (as for the first profile of the same task, which got t-0).
- Got: `t-17 (GeckoMain, Privileged Content)` selected, and later `t-18`/`t-19 (GeckoMain, WebExtensions)` for two other per-test profiles; the Test marker query answered "No markers match", which reads like "the test logged nothing".
- Workaround: `profile info --search Parent`, then `thread select t-0`, on every load.

## `--search eventType:command` also matches `commandupdate`

- Command: `thread markers --search "eventType:activate,...,eventType:command,..." --list`
- Expected: `command` DOM events only (field:value looked like an exact match).
- Got: hundreds of `commandupdate` rows burying the rest.
- Workaround: add `-eventType:commandupdate`. An exact-match form (e.g. `eventType:=command`) would help.
