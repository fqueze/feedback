## `changed preference:` shown as the failure mode when it is fallout

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_smartbar_command_palette.js`
- Expected: the Issues row for the 9 FAIL runs to name the failure that made the runs fail: `This test exceeded the timeout threshold ... Test ran for 329s, limit was 90s`.
- Got: `9x FAIL changed preference: browser.search.region`. The harness logs the `changed preference:` lines at the end of the test, just before the timeout-threshold line, so "first failure per run" picks a side effect. I only found the real failure with `fx-tests task <id> --messages` and then the profile.
- Workaround: `fx-tests task <id> --messages` on each failing task.
- Suggestion: rank `changed preference:` (and `failed to upload profile:`) below other messages when picking a run's first failure, or show the second message too.

## `crash` gives an unsymbolicated stack that the log has symbolicated

- Command: `fx-tests crash QrTsxMMCQMW_KjIuWJHt2g.0 76ec0e73-e9b5-4e2b-9a9d-bc417611a2ce`
- Expected: a symbolicated crashing frame (Windows 32-bit debug).
- Got: `xul.dll + 0x010338d0` for every frame. The resource-usage profile of the same kind of job holds the answer as output markers: `Assertion failure: false (OOM while building cycle collector graph), at ...nsCycleCollector.cpp:2231`, plus a symbolicated stack.
- Workaround: search the job's resource-usage profile for `Assertion failure`.
- Suggestion: when the dump's frames are unsymbolicated, print the `Assertion failure` / `MOZ_CRASH` line the log has for that pid.
