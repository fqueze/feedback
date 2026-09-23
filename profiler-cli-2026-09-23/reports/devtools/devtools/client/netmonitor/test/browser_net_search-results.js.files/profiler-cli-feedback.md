## Default selected thread after `load` is not the parent main thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/AysvS8y9Q_uNRSNEO26DTg/runs/0/artifacts/public/test_info/profile_browser_net_search-results.js.json --session browser_net_search-results.js-3`, then `thread markers --category Test --search browser_net_search-results.js --list --limit 0`
- Expected: the parent process GeckoMain (t-0) selected, as with the other CI per-test profiles I loaded the same way.
- Got: `t-23 (GeckoMain, WebExtensions)` selected, so the Test-marker query returned "No markers match" with no hint that the test log lives on another thread.
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`. One wasted round trip; a "0 markers here, N on t-0" hint would have avoided it.
