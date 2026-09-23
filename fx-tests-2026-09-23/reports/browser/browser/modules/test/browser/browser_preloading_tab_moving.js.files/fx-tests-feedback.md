## `--bugs` prints nothing when no sheriff-annotated bug exists

- Command: `fx-tests test browser/modules/test/browser/browser_preloading_tab_moving.js --bugs`
- Expected: a Bugs section, or a line saying no sheriff-annotated bug names the test in the window, ideally with open bugs whose summary names it (here bug 1720203, open, "Intermittently times out on tsan").
- Got: exactly the output without `--bugs`; no section and no "none found" line, so it reads the same as the flag being ignored.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>"`.

## Question: where was each failing run of this test when it timed out?

- Needed: the last test log line (INFO/PASS) before each `Test timed out`, across all 17 failing jobs, to tell whether every run hangs at the same step. 15 of the 17 jobs (all TSan) have no per-test profile.
- Command used: a loop loading each job's `profile_resource-usage.json` in profiler-cli, `thread markers --search <test> --list --limit 0 --json`, and a Python filter for the last INFO/PASS before the FAIL marker.
- What could have shown it: `fx-tests test <path> --task-ids` (or a `--last-step` flag) printing, per failing task, the last log message before the timeout, from the resource-usage profile's Test markers.
