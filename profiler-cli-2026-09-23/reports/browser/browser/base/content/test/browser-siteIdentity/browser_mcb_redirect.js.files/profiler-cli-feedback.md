## Marker times in `--list` are rounded to the second past one minute

- Question: at what time exactly did the failure happen, to pull the screenshot just before it and to order the test's assertions (many share one second).
- Command: `profiler-cli thread markers --session <s> --category Test --search browser_mcb_redirect --list --limit 0`
- Expected: millisecond times, e.g. `t=63.434s`, as it already prints below one minute (`t=2.430s`).
- Got: `t=1m3s` for every marker in that second, so they cannot be ordered or fed to `screenshots --at`; `marker info` prints the same `1m3s`.
- Workaround: `--json` and reading `flatMarkers[].start`.

## Loading a raw artifact URL selects a WebExtensions thread, not the parent main thread (review)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/d-Vz3n8BToGDYJyyg5DHGA/runs/0/artifacts/public/test_info/profile_browser_mcb_redirect-2.js.json --session <s>`, then `thread markers --category Test --search TEST-UNEXPECTED --list`
- Expected: the parent process GeckoMain (t-0) selected by default, as it was when the same profile was loaded from its profiler.firefox.com link with `thread=0`.
- Got: `t-15 (GeckoMain, WebExtensions)` was selected, so the search returned 0 markers and no hint of the thread was given.
- Workaround: `--thread t-0`.
