## `load` selects a content thread by default, and a Test search on it silently returns nothing

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/GWJW1ek9SAannAUi7DR3tQ/runs/0/artifacts/public/test_info/profile_browser_preferences.js.json --session browser_preferences.js-3`, then `profiler-cli thread markers --category Test --search browser_preferences --list --limit 0 --session browser_preferences.js-3`
- Expected: the parent process GeckoMain (t-0) selected after load, as for the other CI per-test profiles I loaded, so the test log shows.
- Got: `t-20 (GeckoMain, Privileged Content)` was selected; the Test-category search printed "No markers match the specified filters", which reads like "this test has no log in this profile".
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`.
- Suggestion: for mochitest profiles, default to the parent GeckoMain, or when a `--category Test` search is empty on a non-parent thread, say which thread holds Test markers.
