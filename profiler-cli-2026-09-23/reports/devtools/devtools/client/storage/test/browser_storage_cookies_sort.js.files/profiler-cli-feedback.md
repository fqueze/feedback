## Default thread after `load` is not the parent main thread
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/N8msyxsqQAuJRg59yd7avg/runs/0/artifacts/public/test_info/profile_browser_storage_cookies_sort-2.js.json" --session browser_storage_cookies_sort.js-2`, then `thread markers --category Test --search ... --list`
- Expected: the parent process GeckoMain (t-0) selected, as happened for the first-run profile of the same test.
- Got: `t-15 (GeckoMain, WebExtensions)` selected; the Test-category search returned "0 markers", which reads like "the test logged nothing".
- Workaround: `thread select t-0` explicitly after every load. For mochitest per-test profiles, defaulting to the parent GeckoMain (or saying which thread was auto-selected, and why, in the "No markers match" message) would avoid the false negative.
